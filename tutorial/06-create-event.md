<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7f79e-101">Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7f79e-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="7f79e-102">Ouvrez **GraphHelper** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="7f79e-102">Open **GraphHelper** and add the following `import` statements to the top of the file.</span></span>

    ```java
    import com.microsoft.graph.models.Attendee;
    import com.microsoft.graph.models.DateTimeTimeZone;
    import com.microsoft.graph.models.EmailAddress;
    import com.microsoft.graph.models.ItemBody;
    import com.microsoft.graph.models.AttendeeType;
    import com.microsoft.graph.models.BodyType;
    ```

1. <span data-ttu-id="7f79e-103">Ajoutez la fonction suivante à `GraphHelper` la classe pour créer un événement.</span><span class="sxs-lookup"><span data-stu-id="7f79e-103">Add the following function to the `GraphHelper` class to create a new event.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/GraphHelper.java" id="CreateEventSnippet":::

## <a name="update-new-event-fragment"></a><span data-ttu-id="7f79e-104">Mettre à jour un nouveau fragment d’événement</span><span class="sxs-lookup"><span data-stu-id="7f79e-104">Update new event fragment</span></span>

1. <span data-ttu-id="7f79e-105">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="7f79e-105">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="7f79e-106">Nommez la classe `EditTextDateTimePicker` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7f79e-106">Name the class `EditTextDateTimePicker` and select **OK**.</span></span>

1. <span data-ttu-id="7f79e-107">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7f79e-107">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/EditTextDateTimePicker.java" id="DateTimePickerSnippet":::

    <span data-ttu-id="7f79e-108">Cette classe encapsule un contrôle, affichant un s picker de date et d’heure lorsque l’utilisateur l’appuye, et mettant à jour la valeur avec la date et l’heure `EditText` de sélection.</span><span class="sxs-lookup"><span data-stu-id="7f79e-108">This class wraps an `EditText` control, showing a date and time picker when the user taps it, and updating the value with the date and time picked.</span></span>

1. <span data-ttu-id="7f79e-109">Ouvrez **app/res/layout/fragment_new_event.xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7f79e-109">Open **app/res/layout/fragment_new_event.xml** and replace its contents with the following.</span></span>

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_new_event.xml":::

1. <span data-ttu-id="7f79e-110">Ouvrez **NewEventFragment** et ajoutez les `import` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="7f79e-110">Open **NewEventFragment** and add the following `import` statements at the top of the file.</span></span>

    ```java
    import android.util.Log;
    import android.widget.Button;
    import com.google.android.material.snackbar.BaseTransientBottomBar;
    import com.google.android.material.snackbar.Snackbar;
    import com.google.android.material.textfield.TextInputLayout;
    import java.time.ZoneId;
    import java.time.ZonedDateTime;
    ```

1. <span data-ttu-id="7f79e-111">Ajoutez les membres suivants à la `NewEventFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="7f79e-111">Add the following members to the `NewEventFragment` class.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="InputsSnippet":::

1. <span data-ttu-id="7f79e-112">Ajoutez les fonctions suivantes pour afficher et masquer une barre de progression.</span><span class="sxs-lookup"><span data-stu-id="7f79e-112">Add the following functions to show and hide a progress bar.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="ProgressBarSnippet":::

1. <span data-ttu-id="7f79e-113">Ajoutez les fonctions suivantes pour obtenir les valeurs des contrôles d’entrée et appeler la `GraphHelper.createEvent` fonction.</span><span class="sxs-lookup"><span data-stu-id="7f79e-113">Add the following functions to get the values from the input controls and call the `GraphHelper.createEvent` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="CreateEventSnippet":::

1. <span data-ttu-id="7f79e-114">Remplacez `onCreateView` l’existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7f79e-114">Replace the existing `onCreateView` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/NewEventFragment.java" id="OnCreateViewSnippet":::

1. <span data-ttu-id="7f79e-115">Enregistrez vos modifications, puis redémarrez l’application.</span><span class="sxs-lookup"><span data-stu-id="7f79e-115">Save your changes and restart the app.</span></span> <span data-ttu-id="7f79e-116">Sélectionnez **l’élément de** menu Nouvel événement, remplissez le formulaire, puis sélectionnez **CRÉER.**</span><span class="sxs-lookup"><span data-stu-id="7f79e-116">Select the **New Event** menu item, fill in the form, and select **CREATE**.</span></span>

    ![Capture d’écran du formulaire de création d’événement dans l’application](images/create-event.png)
