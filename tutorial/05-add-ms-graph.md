<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cf582-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="cf582-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="cf582-102">Pour cette application, vous allez utiliser le [Kit de développement logiciel (SDK) Microsoft Graph pour Java](https://github.com/microsoftgraph/msgraph-sdk-java) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cf582-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="cf582-103">Obtenir des événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="cf582-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="cf582-104">Dans cette section, vous allez étendre `GraphHelper` la classe pour ajouter une fonction afin d’obtenir les événements de l' `CalendarFragment` utilisateur et la mise à jour pour utiliser ces nouvelles fonctions.</span><span class="sxs-lookup"><span data-stu-id="cf582-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="cf582-105">Ouvrez le fichier **GraphHelper** et ajoutez les instructions `import` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="cf582-105">Open the **GraphHelper** file and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="cf582-106">Ajoutez les fonctions suivantes à la `GraphHelper` classe.</span><span class="sxs-lookup"><span data-stu-id="cf582-106">Add the following functions to the `GraphHelper` class.</span></span>

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

    > [!NOTE]
    > <span data-ttu-id="cf582-107">Examinez le contenu du `getEvents` code.</span><span class="sxs-lookup"><span data-stu-id="cf582-107">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="cf582-108">L’URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="cf582-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="cf582-109">La `select` fonction limite les champs renvoyés pour chaque événement uniquement > ceux que la vue utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="cf582-109">The `select` function limits the fields returned for each events to just > those the view will actually use.</span></span>
    > - <span data-ttu-id="cf582-110">Le `QueryOption` nommé `orderby` est utilisé pour trier les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="cf582-110">The `QueryOption` named `orderby` is used to sort the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="cf582-111">Ajoutez les instructions `import` suivantes en haut du fichier **CalendarFragment** .</span><span class="sxs-lookup"><span data-stu-id="cf582-111">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

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

1. <span data-ttu-id="cf582-112">Ajoutez les membres suivants à la `CalendarFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="cf582-112">Add the following members to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    private ProgressBar mProgress = null;
    ```

1. <span data-ttu-id="cf582-113">Ajoutez les fonctions suivantes à la `CalendarFragment` classe pour masquer et afficher la barre de progression, et pour fournir un rappel pour `getEvents` la fonction `GraphHelper`dans.</span><span class="sxs-lookup"><span data-stu-id="cf582-113">Add the following functions to the `CalendarFragment` class to hide and show the progress bar, and to provide a callback for the `getEvents` function in `GraphHelper`.</span></span>

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

1. <span data-ttu-id="cf582-114">Remplacez la `onCreate` fonction dans la `GraphHelper` classe pour obtenir les événements de l’utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cf582-114">Override the `onCreate` function in the `GraphHelper` class to get the user's events from Microsoft Graph.</span></span>

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

<span data-ttu-id="cf582-115">Notez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="cf582-115">Notice what this code does.</span></span> <span data-ttu-id="cf582-116">Tout d’abord, `acquireTokenSilently` il appelle pour obtenir le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="cf582-116">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="cf582-117">L’appel de cette méthode à chaque fois qu’un jeton d’accès est nécessaire est une meilleure pratique, car il tire parti des fonctionnalités de mise en cache et d’actualisation des jetons d’MSAL.</span><span class="sxs-lookup"><span data-stu-id="cf582-117">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="cf582-118">En interne, MSAL recherche un jeton mis en cache, puis vérifie s’il a expiré.</span><span class="sxs-lookup"><span data-stu-id="cf582-118">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="cf582-119">Si le jeton est présent et n’a pas expiré, il renvoie simplement le jeton mis en cache.</span><span class="sxs-lookup"><span data-stu-id="cf582-119">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="cf582-120">Si elle a expiré, elle tente d’actualiser le jeton avant de le renvoyer.</span><span class="sxs-lookup"><span data-stu-id="cf582-120">If it is expired, it attempts to refresh the token before returning it.</span></span>

<span data-ttu-id="cf582-121">Une fois le jeton récupéré, le code appelle la `getEvents` méthode pour obtenir les événements de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cf582-121">Once the token is retrieved, the code then calls the `getEvents` method to get the user's events.</span></span>

<span data-ttu-id="cf582-122">Vous pouvez maintenant exécuter l’application, se connecter et appuyer sur l’élément de navigation **calendrier** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="cf582-122">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="cf582-123">Vous devriez voir une image JSON des événements dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="cf582-123">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="cf582-124">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="cf582-124">Display the results</span></span>

<span data-ttu-id="cf582-125">À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="cf582-125">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="cf582-126">Dans cette section, vous allez ajouter un `ListView` au fragment de calendrier, créer une mise en page pour chaque élément `ListView`dans le, et créer un adaptateur de liste `ListView` personnalisé pour le qui mappe les `Event` champs de chacun `TextView` aux éléments appropriés dans la vue.</span><span class="sxs-lookup"><span data-stu-id="cf582-126">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="cf582-127">Remplacez l' `TextView` dans **app/res/layout/fragment_calendar. xml** par un `ListView`.</span><span class="sxs-lookup"><span data-stu-id="cf582-127">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    ```xml
    <ListView
        android:id="@+id/eventlist"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:divider="@color/colorPrimary"
        android:dividerHeight="1dp" />
    ```

1. <span data-ttu-id="cf582-128">Cliquez avec le bouton droit sur le dossier **app/res/layout** , puis sélectionnez **nouveau**, puis **fichier de ressources de mise en page**.</span><span class="sxs-lookup"><span data-stu-id="cf582-128">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="cf582-129">Nommez le `event_list_item`fichier, changez l' `RelativeLayout` **élément racine** et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="cf582-129">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="cf582-130">Ouvrez le fichier **event_list_item. xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="cf582-130">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="cf582-131">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et sélectionnez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="cf582-131">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="cf582-132">Nommez la `EventListAdapter` classe et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="cf582-132">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="cf582-133">Ouvrez le fichier **EventListAdapter** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="cf582-133">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="cf582-134">Ouvrez la classe **CalendarFragment** et ajoutez la fonction suivante à la classe.</span><span class="sxs-lookup"><span data-stu-id="cf582-134">Open the **CalendarFragment** class and add the following function to the class.</span></span>

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

1. <span data-ttu-id="cf582-135">Ajoutez la ligne de code suivante dans le `success` remplacement après la `mEventList = iEventCollectionPage.getCurrentPage();` ligne.</span><span class="sxs-lookup"><span data-stu-id="cf582-135">Add the following line of code in the `success` override after the `mEventList = iEventCollectionPage.getCurrentPage();` line.</span></span>

    ```java
    addEventsToList();
    ```

1. <span data-ttu-id="cf582-136">Exécutez l’application, connectez-vous, puis appuyez sur l’élément de navigation **calendrier** .</span><span class="sxs-lookup"><span data-stu-id="cf582-136">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="cf582-137">La liste des événements doit s’afficher.</span><span class="sxs-lookup"><span data-stu-id="cf582-137">You should see the list of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)
