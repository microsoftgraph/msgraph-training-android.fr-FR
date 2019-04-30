<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l'application. Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour Java](https://github.com/microsoftgraph/msgraph-sdk-java) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d'Outlook

Commencez par étendre la `GraphHelper` classe pour ajouter une fonction afin d'obtenir les événements de l'utilisateur. Ouvrez le fichier **GraphHelper** et ajoutez les instructions `import` suivantes en haut du fichier.

```java
import com.microsoft.graph.options.Option;
import com.microsoft.graph.options.QueryOption;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import java.util.LinkedList;
import java.util.List;
```

Ajoutez les fonctions suivantes à la `GraphHelper` classe.

```java
public void getEvents(String accessToken, ICallback<IEventCollectionPage> callback) {
    mAccessToken = accessToken;

    // Use query options to sort by created time
    final List<Option> options = new LinkedList<Option>();
    options.add(new QueryOption("orderby", "createdDateTime DESC"));


    // GET /me/events
    mClient.me().events().buildRequest(options)
            .select("subject,organizer,start,end")
            .get(callback);

}

// Debug function to get the JSON representation of a Graph
// object
public String serializeObject(Object object) {
    return mClient.getSerializer().serializeObject(object);
}
```

Examinez le contenu du `getEvents` code.

- L'URL qui sera appelée est `/v1.0/me/events`.
- La `select` fonction limite les champs renvoyés pour chaque événement à ceux que l'affichage utilise réellement.
- Le `QueryOption` nommé `orderby` est utilisé pour trier les résultats en fonction de la date et de l'heure de leur création, avec l'élément le plus récent en premier.

À présent `CalendarFragment` , mettez à jour pour utiliser ces nouvelles fonctions. Ajoutez les instructions `import` suivantes en haut du fichier **CalendarFragment** .

```java
import android.util.Log;
import android.widget.ListView;
import android.widget.ProgressBar;
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.Event;
import com.microsoft.graph.requests.extensions.IEventCollectionPage;
import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.AuthenticationResult;
import com.microsoft.identity.client.exception.MsalException;
import java.util.List;
```

Ajoutez les membres suivants à la `CalendarFragment` classe.

```java
private List<Event> mEventList = null;
private ProgressBar mProgress = null;
```

À présent, ajoutez les fonctions suivantes `CalendarFragment` à la classe pour masquer et afficher la barre de progression, et pour fournir un `getEvents` rappel pour `GraphHelper`la fonction dans.

```java
private void showProgressBar() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            mProgress.setVisibility(View.VISIBLE);
        }
    });
}

private void hideProgressBar() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            mProgress.setVisibility(View.GONE);
        }
    });
}

private ICallback<IEventCollectionPage> getEventsCallback() {
    return new ICallback<IEventCollectionPage>() {
        @Override
        public void success(IEventCollectionPage iEventCollectionPage) {
            mEventList = iEventCollectionPage.getCurrentPage();

            // Temporary for debugging
            String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
            Log.d("GRAPH", jsonEvents);

            hideProgressBar();
        }

        @Override
        public void failure(ClientException ex) {
            Log.e("GRAPH", "Error getting events", ex);
            hideProgressBar();
        }
    };
}
```

Enfin, substituez la `onCreate` fonction dans la `GraphHelper` classe pour obtenir les événements de l'utilisateur à partir de Microsoft Graph.

```java
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    mProgress = getActivity().findViewById(R.id.progressbar);
    showProgressBar();

    // Get a current access token
    AuthenticationHelper.getInstance()
            .acquireTokenSilently(new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                    final GraphHelper graphHelper = GraphHelper.getInstance();

                    // Get the user's events
                    graphHelper.getEvents(authenticationResult.getAccessToken(),
                            getEventsCallback());
                }

                @Override
                public void onError(MsalException exception) {
                    Log.e("AUTH", "Could not get token silently", exception);
                    hideProgressBar();
                }

                @Override
                public void onCancel() {
                    hideProgressBar();
                }
            });
}
```

Notez ce que fait ce code. Tout d'abord, `acquireTokenSilently` il appelle pour obtenir le jeton d'accès. L'appel de cette méthode à chaque fois qu'un jeton d'accès est nécessaire est une meilleure pratique, car il tire parti des fonctionnalités de mise en cache et d'actualisation des jetons d'MSAL. En interne, MSAL recherche un jeton mis en cache, puis vérifie s'il a expiré. Si le jeton est présent et n'a pas expiré, il renvoie simplement le jeton mis en cache. Si elle a expiré, elle tente d'actualiser le jeton avant de le renvoyer.

Une fois le jeton récupéré, le code appelle la `getEvents` méthode pour obtenir les événements de l'utilisateur.

Vous pouvez maintenant exécuter l'application, se connecter et appuyer sur l'élément de navigation **calendrier** dans le menu. Vous devriez voir une image JSON des événements dans le journal de débogage dans Android Studio.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale. Commencez par remplacer le `TextView` dans **app/res/layout/fragment_calendar. xml** par un `ListView`.

```xml
<ListView
    android:id="@+id/eventlist"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:divider="@color/colorPrimary"
    android:dividerHeight="1dp" />
```

Ensuite, créez une mise en page pour chaque élément `ListView`dans le. Cliquez avec le bouton droit sur le dossier **app/res/layout** , sélectionnez Nouveau, puis fichier **de ressources de** **mise en page**. Nommez le `event_list_item`fichier, remplacez l' **élément racine** par `RelativeLayout`, puis choisissez **OK**. Ouvrez le fichier **event_list_item. xml** et remplacez son contenu par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">

    <TextView
        android:id="@+id/eventsubject"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Subject"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/eventorganizer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/eventsubject"
        android:text="Adele Vance"
        android:textSize="15sp" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/eventorganizer"
        android:orientation="horizontal">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingEnd="2sp"
            android:text="Start:"
            android:textSize="15sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/eventstart"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="1:30 PM 2/19/2019"
            android:textSize="15sp" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:paddingStart="5sp"
            android:paddingEnd="2sp"
            android:text="End:"
            android:textSize="15sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/eventend"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="1:30 PM 2/19/2019"
            android:textSize="15sp" />
    </LinearLayout>
</RelativeLayout>
```

Créez maintenant un adaptateur de liste personnalisé pour `ListView`le. Cela est nécessaire pour créer les affichages pour les éléments de la liste et pour mapper les champs de chacun `Event` dans l'affichage `TextView` approprié.

Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **nouveau**, puis **classe Java**. Nommez la `EventListAdapter` classe et cliquez sur **OK**. Ouvrez le fichier **EventListAdapter** et remplacez son contenu par ce qui suit.

```java
package com.example.graphtutorial;

import android.content.Context;
import android.support.annotation.NonNull;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.TextView;

import com.microsoft.graph.models.extensions.DateTimeTimeZone;
import com.microsoft.graph.models.extensions.Event;

import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.util.List;
import java.util.TimeZone;

public class EventListAdapter extends ArrayAdapter<Event> {
    private Context mContext;
    private int mResource;
    private ZoneId mLocalTimeZoneId;

    // Used for the ViewHolder pattern
    // https://developer.android.com/training/improving-layouts/smooth-scrolling
    static class ViewHolder {
        TextView subject;
        TextView organizer;
        TextView start;
        TextView end;
    }

    public EventListAdapter(Context context, int resource, List<Event> events) {
        super(context, resource, events);
        mContext = context;
        mResource = resource;
        mLocalTimeZoneId = TimeZone.getDefault().toZoneId();
    }

    @NonNull
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        Event event = getItem(position);

        ViewHolder holder;

        if (convertView == null) {
            LayoutInflater inflater = LayoutInflater.from(mContext);
            convertView = inflater.inflate(mResource, parent, false);

            holder = new ViewHolder();
            holder.subject = convertView.findViewById(R.id.eventsubject);
            holder.organizer = convertView.findViewById(R.id.eventorganizer);
            holder.start = convertView.findViewById(R.id.eventstart);
            holder.end = convertView.findViewById(R.id.eventend);

            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }

        holder.subject.setText(event.subject);
        holder.organizer.setText(event.organizer.emailAddress.name);
        holder.start.setText(getLocalDateTimeString(event.start));
        holder.end.setText(getLocalDateTimeString(event.end));

        return convertView;
    }

    // Convert Graph's DateTimeTimeZone format to
    // a LocalDateTime, then return a formatted string
    private String getLocalDateTimeString(DateTimeTimeZone dateTime) {
        ZonedDateTime localDateTime = LocalDateTime.parse(dateTime.dateTime)
                .atZone(ZoneId.of(dateTime.timeZone))
                .withZoneSameInstant(mLocalTimeZoneId);

        return String.format("%s %s",
                localDateTime.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM)),
                localDateTime.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT)));
    }
}
```

Enfin, mettez à `CalendarFragment` jour la classe pour `EventListAdapter` utiliser l'pour `ListView`initialiser le. Ouvrez la classe **CalendarFragment** et ajoutez la fonction suivante à la classe.

```java
private void addEventsToList() {
    getActivity().runOnUiThread(new Runnable() {
        @Override
        public void run() {
            ListView eventListView = getView().findViewById(R.id.eventlist);

            EventListAdapter listAdapter = new EventListAdapter(getActivity(),
                    R.layout.event_list_item, mEventList);

            eventListView.setAdapter(listAdapter);
        }
    });
}
```

Ajoutez la ligne de code suivante dans le `success` remplacement après la `mEventList = iEventCollectionPage.getCurrentPage();` ligne.

```java
addEventsToList();
```

Exécutez l'application, connectez-vous, puis appuyez sur l'élément de navigation **calendrier** . La liste des événements doit s'afficher.

![Capture d'écran du tableau des événements](./images/calendar-list.png)
