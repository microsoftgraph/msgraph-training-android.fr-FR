<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="053f8-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="053f8-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="053f8-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="053f8-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="053f8-103">Pour ce faire, vous allez intégrer la bibliothèque d’authentification [Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="053f8-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="053f8-104">Cliquez avec le bouton droit **sur le dossier res** et **sélectionnez Nouveau,** puis **Répertoire de ressources Android.**</span><span class="sxs-lookup"><span data-stu-id="053f8-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="053f8-105">Modifiez **le type de ressource** et `raw` sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="053f8-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="053f8-106">Cliquez avec le bouton droit sur le nouveau **dossier** brut et sélectionnez **Nouveau,** puis **Fichier**.</span><span class="sxs-lookup"><span data-stu-id="053f8-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="053f8-107">Nommez le fichier `msal_config.json` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="053f8-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="053f8-108">Ajoutez ce qui suit au **fichiermsal_config.jssur.**</span><span class="sxs-lookup"><span data-stu-id="053f8-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="053f8-109">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application et remplacez-le par le nom du `com.example.graphtutorial` package de votre projet.</span><span class="sxs-lookup"><span data-stu-id="053f8-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="053f8-110">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre `msal_config.json` ID d’application.</span><span class="sxs-lookup"><span data-stu-id="053f8-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="053f8-111">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="053f8-111">Implement sign-in</span></span>

<span data-ttu-id="053f8-112">Dans cette section, vous allez mettre à jour le manifeste pour permettre à MSAL d’utiliser un navigateur pour authentifier l’utilisateur, inscrire votre URI de redirection comme étant géré par l’application, créer une classe d’aide à l’authentification et mettre à jour l’application pour se dé connecter et se dé dévier.</span><span class="sxs-lookup"><span data-stu-id="053f8-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="053f8-113">Développez **le dossier application/manifestes** et ouvrez **AndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="053f8-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="053f8-114">Ajoutez les éléments suivants au-dessus de `application` l’élément.</span><span class="sxs-lookup"><span data-stu-id="053f8-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="053f8-115">Ces autorisations sont requises pour que la bibliothèque MSAL authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="053f8-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="053f8-116">Ajoutez l’élément suivant à l’intérieur `application` de l’élément, en remplaçant la `YOUR_PACKAGE_NAME_HERE` chaîne par le nom de votre package.</span><span class="sxs-lookup"><span data-stu-id="053f8-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

    ```xml
    <!--Intent filter to capture authorization code response from the default browser on the
        device calling back to the app after interactive sign in -->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:scheme="msauth"
                android:host="YOUR_PACKAGE_NAME_HERE"
                android:path="/callback" />
        </intent-filter>
    </activity>
    ```

1. <span data-ttu-id="053f8-117">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="053f8-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="053f8-118">Modifiez le **type** en **interface.**</span><span class="sxs-lookup"><span data-stu-id="053f8-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="053f8-119">Nommez l’interface `IAuthenticationHelperCreatedListener` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="053f8-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="053f8-120">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="053f8-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="053f8-121">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="053f8-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="053f8-122">Nommez la classe `AuthenticationHelper` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="053f8-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="053f8-123">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="053f8-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="053f8-124">Ouvrez **MainActivity** et ajoutez les `import` instructions suivantes.</span><span class="sxs-lookup"><span data-stu-id="053f8-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="053f8-125">Ajoutez la propriété membre suivante à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="053f8-125">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="053f8-126">Ajoutez la fonction suivante à la fin de la fonction `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="053f8-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="053f8-127">Ajoutez les fonctions suivantes à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="053f8-127">Add the following functions to the `MainActivity` class.</span></span>

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAuthHelper.acquireTokenSilently()
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                // Check the type of exception and handle appropriately
                Throwable cause = exception.getCause();
                if (cause instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (shouldAttemptInteractive) doInteractiveSignIn();
                } else if (cause instanceof MsalClientException) {
                    MsalClientException clientException = (MsalClientException)cause;
                    if (clientException.getErrorCode() == "no_current_account" ||
                        clientException.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (shouldAttemptInteractive) doInteractiveSignIn();
                    }
                } else {
                    handleSignInFailure(cause);
                }
                hideProgressBar();
                return null;
            });
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this)
            .thenAccept(authenticationResult -> {
                handleSignInSuccess(authenticationResult);
            })
            .exceptionally(exception -> {
                handleSignInFailure(exception);
                hideProgressBar();
                return null;
            });
    }

    // Handles the authentication result
    private void handleSignInSuccess(IAuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        hideProgressBar();

        setSignedInState(true);
        openHomeFragment(mUserName);
    }

    private void handleSignInFailure(Throwable exception) {
        if (exception instanceof MsalServiceException) {
            // Exception when communicating with the auth server, likely config issue
            Log.e("AUTH", "Service error authenticating", exception);
        } else if (exception instanceof MsalClientException) {
            // Exception inside MSAL, more info inside MsalError.java
            Log.e("AUTH", "Client error authenticating", exception);
        } else {
            Log.e("AUTH", "Unhandled exception authenticating", exception);
        }
    }
    ```

1. <span data-ttu-id="053f8-128">Remplacez les fonctions `signIn` `signOut` existantes et existantes par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="053f8-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="053f8-129">Notez que `signIn` la méthode effectue une connectez-vous silencieuse (via `doSilentSignIn` ).</span><span class="sxs-lookup"><span data-stu-id="053f8-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="053f8-130">Le rappel de cette méthode se connecte de manière interactive en cas d’échec du mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="053f8-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="053f8-131">Cela évite d’avoir à inviter l’utilisateur chaque fois qu’il lance l’application.</span><span class="sxs-lookup"><span data-stu-id="053f8-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="053f8-132">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="053f8-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="053f8-133">Lorsque vous appuyez sur **l’élément de** menu Connexion, un navigateur s’ouvre sur la page de connexion Azure AD.</span><span class="sxs-lookup"><span data-stu-id="053f8-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="053f8-134">Connectez-vous à votre compte.</span><span class="sxs-lookup"><span data-stu-id="053f8-134">Sign in with your account.</span></span>

<span data-ttu-id="053f8-135">Une fois l’application reprise, vous devez voir un jeton d’accès imprimé dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="053f8-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Capture d’écran de la fenêtre Logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="053f8-137">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="053f8-137">Get user details</span></span>

<span data-ttu-id="053f8-138">Dans cette section, vous allez créer une classe d’aide pour contenir tous les appels à Microsoft Graph et mettre à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur `MainActivity` connecté.</span><span class="sxs-lookup"><span data-stu-id="053f8-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="053f8-139">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="053f8-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="053f8-140">Nommez la classe `GraphHelper` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="053f8-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="053f8-141">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="053f8-141">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.GraphServiceClient;

    import java.util.concurrent.CompletableFuture;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private GraphServiceClient mClient = null;

        private GraphHelper() {
            AuthenticationHelper authProvider = AuthenticationHelper.getInstance();

            mClient = GraphServiceClient.builder()
                .authenticationProvider(authProvider).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        public CompletableFuture<User> getUser() {
            // GET /me (logged in user)
            return mClient.me().buildRequest()
                .select("displayName,mail,mailboxSettings,userPrincipalName")
                .getAsync();
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="053f8-142">Considérez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="053f8-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="053f8-143">Il expose une fonction pour obtenir les informations de l’utilisateur connecté à partir du point de terminaison `getUser` `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="053f8-143">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>
    >   - <span data-ttu-id="053f8-144">Il utilise `.select` pour demander uniquement les propriétés de l’utilisateur dont l’application a besoin.</span><span class="sxs-lookup"><span data-stu-id="053f8-144">It uses `.select` to request only the properties of the user that the application needs.</span></span>

1. <span data-ttu-id="053f8-145">Supprimez les lignes suivantes qui définissent le nom d’utilisateur et le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="053f8-145">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Lynne Robbins";
    mUserEmail = "lynner@contoso.com";
    mUserTimeZone = "Pacific Standard Time";
    ```

1. <span data-ttu-id="053f8-146">Remplacez la fonction `handleSignInSuccess` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="053f8-146">Replace the existing `handleSignInSuccess` function with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="HandleSignInSuccessSnippet":::

1. <span data-ttu-id="053f8-147">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="053f8-147">Save your changes and run the app.</span></span> <span data-ttu-id="053f8-148">Une fois que l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse e-mail de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="053f8-148">After sign-in the UI is updated with the user's display name and email address.</span></span>
