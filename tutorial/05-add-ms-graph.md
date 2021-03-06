<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser le [SDK Microsoft Graph pour](https://github.com/microsoftgraph/msgraph-sdk-java) Java pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

Dans cette section, vous allez étendre la classe pour ajouter une fonction afin d’obtenir les événements de l’utilisateur pour la semaine en cours et mettre à jour pour utiliser `GraphHelper` `CalendarFragment` ces nouvelles fonctions.

1. Ouvrez **GraphHelper** et ajoutez les `import` instructions suivantes en haut du fichier.

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.EventCollectionPage;
    import com.microsoft.graph.requests.EventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    import java.util.concurrent.CompletableFuture;
    ```

1. Ajoutez les fonctions suivantes à la `GraphHelper` classe.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > Réfléchissez à ce que fait `getCalendarView` le code.
    >
    > - L’URL qui sera appelée est `/v1.0/me/calendarview`.
    >   - Les `startDateTime` `endDateTime` paramètres de requête définissent le début et la fin de l’affichage Calendrier.
    >   - L’en-tête indique à Microsoft Graph de renvoyer les heures de début et de fin de chaque événement dans le `Prefer: outlook.timezone` fuseau horaire de l’utilisateur.
    >   - La fonction `select` limite les champs renvoyés pour chaque événement à ceux que la vue utilise réellement.
    >   - La `orderby` fonction trie les résultats par heure de début.
    >   - La `top` fonction demande 25 résultats par page.
    > - La `processPage` fonction vérifie si d’autres résultats sont disponibles et demande des pages supplémentaires si nécessaire.

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .** Nommez la classe `GraphToIana` et sélectionnez **OK.**

1. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. Ajoutez les `import` instructions suivantes en haut du **fichier CalendarFragment.**

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.Event;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalException;
    import java.time.DayOfWeek;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    import java.time.temporal.ChronoUnit;
    import java.time.temporal.TemporalAdjusters;
    import java.util.List;
    ```

1. Ajoutez le membre suivant à la `CalendarFragment` classe.

    ```java
    private List<Event> mEventList = null;
    ```

1. Ajoutez les fonctions suivantes à la `CalendarFragment` classe pour masquer et afficher la barre de progression.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. Ajoutez la fonction suivante pour obtenir la liste des événements à des fins de débogage.

    ```java
    private void addEventsToList() {
        // Temporary for debugging
        String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
        Log.d("GRAPH", jsonEvents);
    }
    ```

1. Remplacez la fonction `onCreateView` existante dans la classe par ce qui `CalendarFragment` suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

1. Exécutez l’application, connectez-vous et appuyez sur **l’élément** de navigation Calendrier dans le menu. Vous devriez voir un vidage JSON des événements dans le journal de débogage dans Android Studio.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais remplacer le vidage JSON par un contenu qui permet d’afficher les résultats de manière conviviale. Dans cette section, vous allez ajouter un élément au fragment de calendrier, créer une disposition pour chaque élément de l’affichage et créer une carte de liste personnalisée pour celle qui mase les champs de chacun d’eux à l’élément approprié dans `ListView` `ListView` l’affichage. `ListView` `Event` `TextView`

1. Remplacez `TextView` **l’application/res/layout/fragment_calendar.xml** par un `ListView` .

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. Cliquez avec le bouton droit sur le **dossier application/res/layout** et sélectionnez **Nouveau,** puis fichier **de ressources de disposition.**

1. Nommez le `event_list_item` fichier, modifiez **l’élément racine** `RelativeLayout` et sélectionnez **OK**.

1. Ouvrez **event_list_item.xml** fichier et remplacez son contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**

1. Nommez la classe `EventListAdapter` et sélectionnez **OK.**

1. Ouvrez **le fichier EventListAdapter** et remplacez son contenu par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. Ouvrez **la classe CalendarFragment** et remplacez la fonction `addEventsToList` existante par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. Exécutez l’application, connectez-vous et appuyez sur **l’élément de** navigation Calendrier. Vous devriez voir la liste des événements.

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)
