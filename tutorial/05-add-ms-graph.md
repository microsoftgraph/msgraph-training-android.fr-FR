<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="48a27-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="48a27-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="48a27-102">Pour cette application, vous allez utiliser le [SDK Microsoft Graph pour](https://github.com/microsoftgraph/msgraph-sdk-java) Java pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="48a27-102">For this application, you will use the [Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="48a27-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="48a27-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="48a27-104">Dans cette section, vous allez étendre la classe pour ajouter une fonction afin d’obtenir les événements de l’utilisateur pour la semaine en cours et mettre à jour pour utiliser `GraphHelper` `CalendarFragment` ces nouvelles fonctions.</span><span class="sxs-lookup"><span data-stu-id="48a27-104">In this section you will extend the `GraphHelper` class to add a function to get the user's events for the current week and update `CalendarFragment` to use these new functions.</span></span>

1. <span data-ttu-id="48a27-105">Ouvrez **GraphHelper** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="48a27-105">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.options.Option;
    import com.microsoft.graph.options.HeaderOption;
    import com.microsoft.graph.options.QueryOption;
    import com.microsoft.graph.requests.extensions.IEventCollectionPage;
    import com.microsoft.graph.requests.extensions.IEventCollectionRequestBuilder;
    import java.time.ZonedDateTime;
    import java.time.format.DateTimeFormatter;
    import java.util.LinkedList;
    import java.util.List;
    ```

1. <span data-ttu-id="48a27-106">Ajoutez les fonctions suivantes à la `GraphHelper` classe.</span><span class="sxs-lookup"><span data-stu-id="48a27-106">Add the following functions to the `GraphHelper` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="GetEventsSnippet":::

    > [!NOTE]
    > <span data-ttu-id="48a27-107">Réfléchissez à ce que fait `getCalendarView` le code.</span><span class="sxs-lookup"><span data-stu-id="48a27-107">Consider what the code in `getCalendarView` is doing.</span></span>
    >
    > - <span data-ttu-id="48a27-108">L’URL qui sera appelée est `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="48a27-108">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
    >   - <span data-ttu-id="48a27-109">Les `startDateTime` `endDateTime` paramètres de requête définissent le début et la fin de l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="48a27-109">The `startDateTime` and `endDateTime` query parameters define the start and end of the calendar view.</span></span>
    >   - <span data-ttu-id="48a27-110">L’en-tête indique à Microsoft Graph de renvoyer les heures de début et de fin de chaque événement dans le `Prefer: outlook.timezone` fuseau horaire de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48a27-110">the `Prefer: outlook.timezone` header causes the Microsoft Graph to return the start and end times of each event in the user's time zone.</span></span>
    >   - <span data-ttu-id="48a27-111">La fonction `select` limite les champs renvoyés pour chaque événement à ceux que la vue utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="48a27-111">The `select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="48a27-112">La `orderby` fonction trie les résultats par heure de début.</span><span class="sxs-lookup"><span data-stu-id="48a27-112">The `orderby` function sorts the results by start time.</span></span>
    >   - <span data-ttu-id="48a27-113">La `top` fonction demande 25 résultats par page.</span><span class="sxs-lookup"><span data-stu-id="48a27-113">The `top` function requests 25 results per page.</span></span>
    > - <span data-ttu-id="48a27-114">Un rappel est défini ( ) pour vérifier si d’autres résultats sont disponibles et demander des `pagingCallback` pages supplémentaires si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="48a27-114">A callback is defined (`pagingCallback`) to check if there are more results available and to request additional pages if needed.</span></span>

1. <span data-ttu-id="48a27-115">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="48a27-115">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="48a27-116">Nommez la classe `GraphToIana` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="48a27-116">Name the class `GraphToIana` and select **OK**.</span></span>

1. <span data-ttu-id="48a27-117">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="48a27-117">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphToIana.java" id="GraphToIanaSnippet":::

1. <span data-ttu-id="48a27-118">Ajoutez les `import` instructions suivantes en haut du **fichier CalendarFragment.**</span><span class="sxs-lookup"><span data-stu-id="48a27-118">Add the following `import` statements to the top of the **CalendarFragment** file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.ListView;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.Event;
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

1. <span data-ttu-id="48a27-119">Ajoutez le membre suivant à la `CalendarFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="48a27-119">Add the following member to the `CalendarFragment` class.</span></span>

    ```java
    private List<Event> mEventList = null;
    ```

1. <span data-ttu-id="48a27-120">Ajoutez les fonctions suivantes à la `CalendarFragment` classe pour masquer et afficher la barre de progression.</span><span class="sxs-lookup"><span data-stu-id="48a27-120">Add the following functions to the `CalendarFragment` class to hide and show the progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="48a27-121">Ajoutez la fonction suivante pour fournir un rappel pour la `getCalendarView` fonction dans `GraphHelper` .</span><span class="sxs-lookup"><span data-stu-id="48a27-121">Add the following function to provide a callback for the `getCalendarView` function in `GraphHelper`.</span></span>

    ```java
    private ICallback<List<Event>> getCalendarViewCallback() {
        return new ICallback<List<Event>>() {
            @Override
            public void success(List<Event> eventList) {
                mEventList = eventList;

                // Temporary for debugging
                String jsonEvents = GraphHelper.getInstance().serializeObject(mEventList);
                Log.d("GRAPH", jsonEvents);

                hideProgressBar();
            }

            @Override
            public void failure(ClientException ex) {
                hideProgressBar();
                Log.e("GRAPH", "Error getting events", ex);
                Snackbar.make(getView(),
                    ex.getMessage(),
                    BaseTransientBottomBar.LENGTH_LONG).show();
            }
        };
    }
    ```

1. <span data-ttu-id="48a27-122">Remplacez la fonction `onCreateView` existante dans la classe par ce qui `CalendarFragment` suit.</span><span class="sxs-lookup"><span data-stu-id="48a27-122">Replace the existing `onCreateView` function in the `CalendarFragment` class with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="OnCreateViewSnippet":::

    <span data-ttu-id="48a27-123">Notez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="48a27-123">Notice what this code does.</span></span> <span data-ttu-id="48a27-124">Tout d’abord, il `acquireTokenSilently` appelle pour obtenir le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="48a27-124">First, it calls `acquireTokenSilently` to get the access token.</span></span> <span data-ttu-id="48a27-125">Il est préférable d’appeler cette méthode chaque fois qu’un jeton d’accès est nécessaire, car elle tire parti des capacités de mise en cache et d’actualisation des jetons de MSAL.</span><span class="sxs-lookup"><span data-stu-id="48a27-125">Calling this method every time an access token is needed is a best practice because it takes advantage of MSAL's caching and token refresh abilities.</span></span> <span data-ttu-id="48a27-126">En interne, MSAL recherche un jeton mis en cache, puis vérifie s’il a expiré.</span><span class="sxs-lookup"><span data-stu-id="48a27-126">Internally, MSAL checks for a cached token, then checks if it is expired.</span></span> <span data-ttu-id="48a27-127">Si le jeton est présent et qu’il n’a pas expiré, il renvoie simplement le jeton mis en cache.</span><span class="sxs-lookup"><span data-stu-id="48a27-127">If the token is present and not expired, it just returns the cached token.</span></span> <span data-ttu-id="48a27-128">S’il a expiré, il tente d’actualiser le jeton avant de le renvoyer.</span><span class="sxs-lookup"><span data-stu-id="48a27-128">If it is expired, it attempts to refresh the token before returning it.</span></span>

    <span data-ttu-id="48a27-129">Une fois le jeton récupéré, le code appelle ensuite `getCalendarView` la méthode pour obtenir les événements de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="48a27-129">Once the token is retrieved, the code then calls the `getCalendarView` method to get the user's events.</span></span>

1. <span data-ttu-id="48a27-130">Exécutez l’application, connectez-vous et appuyez sur **l’élément** de navigation Calendrier dans le menu.</span><span class="sxs-lookup"><span data-stu-id="48a27-130">Run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="48a27-131">Vous devriez voir un vidage JSON des événements dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="48a27-131">You should see a JSON dump of the events in the debug log in Android Studio.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="48a27-132">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="48a27-132">Display the results</span></span>

<span data-ttu-id="48a27-133">Vous pouvez désormais remplacer le vidage JSON par un autre qui permet d’afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="48a27-133">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="48a27-134">Dans cette section, vous allez ajouter un élément au fragment de calendrier, créer une disposition pour chaque élément de l’affichage et créer une carte de liste personnalisée pour celle qui mase les champs de chacun d’eux à l’élément approprié dans `ListView` `ListView` l’affichage. `ListView` `Event` `TextView`</span><span class="sxs-lookup"><span data-stu-id="48a27-134">In this section, you will add a `ListView` to the calendar fragment, create a layout for each item in the `ListView`, and create a custom list adapter for the `ListView` that maps the fields of each `Event` to the appropriate `TextView` in the view.</span></span>

1. <span data-ttu-id="48a27-135">Remplacez `TextView` **l’application/res/layout/fragment_calendar.xml** par un `ListView` .</span><span class="sxs-lookup"><span data-stu-id="48a27-135">Replace the `TextView` in **app/res/layout/fragment_calendar.xml** with a `ListView`.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_calendar.xml" highlight="6-11":::

1. <span data-ttu-id="48a27-136">Cliquez avec le bouton droit sur le **dossier application/res/layout** et sélectionnez **Nouveau,** puis fichier **de ressources de disposition.**</span><span class="sxs-lookup"><span data-stu-id="48a27-136">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="48a27-137">Nommez le `event_list_item` fichier, modifiez **l’élément racine** `RelativeLayout` et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="48a27-137">Name the file `event_list_item`, change the **Root element** to `RelativeLayout`, and select **OK**.</span></span>

1. <span data-ttu-id="48a27-138">Ouvrez **event_list_item.xml** fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="48a27-138">Open the **event_list_item.xml** file and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/event_list_item.xml":::

1. <span data-ttu-id="48a27-139">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="48a27-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="48a27-140">Nommez la classe `EventListAdapter` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="48a27-140">Name the class `EventListAdapter` and select **OK**.</span></span>

1. <span data-ttu-id="48a27-141">Ouvrez **le fichier EventListAdapter** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="48a27-141">Open the **EventListAdapter** file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EventListAdapter.java" id="EventListAdapterSnippet":::

1. <span data-ttu-id="48a27-142">Ouvrez **la classe CalendarFragment** et ajoutez la fonction suivante à la classe.</span><span class="sxs-lookup"><span data-stu-id="48a27-142">Open the **CalendarFragment** class and add the following function to the class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="AddEventsToListSnippet":::

1. <span data-ttu-id="48a27-143">Remplacez le code de débogage temporaire de la `success` substitution par `addEventsToList();` .</span><span class="sxs-lookup"><span data-stu-id="48a27-143">Replace the temporary debugging code from the `success` override with `addEventsToList();`.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/CalendarFragment.java" id="SuccessSnippet" highlight="5":::

1. <span data-ttu-id="48a27-144">Exécutez l’application, connectez-vous et appuyez sur **l’élément de** navigation Calendrier.</span><span class="sxs-lookup"><span data-stu-id="48a27-144">Run the app, sign in, and tap the **Calendar** navigation item.</span></span> <span data-ttu-id="48a27-145">Vous devriez voir la liste des événements.</span><span class="sxs-lookup"><span data-stu-id="48a27-145">You should see the list of events.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-list.png)
