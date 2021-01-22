<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7acdb-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7acdb-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="7acdb-102">Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="7acdb-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="7acdb-103">Pour ce faire, vous allez intégrer la bibliothèque d’authentification [Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7acdb-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="7acdb-104">Cliquez avec le bouton droit **sur le dossier res** et **sélectionnez Nouveau,** puis **Répertoire de ressources Android.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="7acdb-105">Modifiez **le type de ressource** et `raw` sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="7acdb-106">Cliquez avec le bouton droit sur le nouveau **dossier** brut et **sélectionnez Nouveau,** puis **Fichier**.</span><span class="sxs-lookup"><span data-stu-id="7acdb-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="7acdb-107">Nommez le fichier `msal_config.json` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="7acdb-108">Ajoutez ce qui suit au **fichiermsal_config.jssur.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-108">Add the following to the **msal_config.json** file.</span></span>

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. <span data-ttu-id="7acdb-109">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application et par le `com.example.graphtutorial` nom du package de votre projet.</span><span class="sxs-lookup"><span data-stu-id="7acdb-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `com.example.graphtutorial` with your project's package name.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="7acdb-110">Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre `msal_config.json` ID d’application.</span><span class="sxs-lookup"><span data-stu-id="7acdb-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="7acdb-111">Implémentation de la connexion</span><span class="sxs-lookup"><span data-stu-id="7acdb-111">Implement sign-in</span></span>

<span data-ttu-id="7acdb-112">Dans cette section, vous allez mettre à jour le manifeste pour permettre à MSAL d’utiliser un navigateur pour authentifier l’utilisateur, inscrire votre URI de redirection comme étant géré par l’application, créer une classe d’aide à l’authentification et mettre à jour l’application pour se connecter et se dé dévier.</span><span class="sxs-lookup"><span data-stu-id="7acdb-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="7acdb-113">Développez **le dossier application/manifestes** et **ouvrezAndroidManifest.xml**.</span><span class="sxs-lookup"><span data-stu-id="7acdb-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="7acdb-114">Ajoutez les éléments suivants au-dessus de `application` l’élément.</span><span class="sxs-lookup"><span data-stu-id="7acdb-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="7acdb-115">Ces autorisations sont requises pour que la bibliothèque MSAL authentifier l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7acdb-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="7acdb-116">Ajoutez l’élément suivant à l’intérieur `application` de l’élément, en remplaçant la `YOUR_PACKAGE_NAME_HERE` chaîne par le nom de votre package.</span><span class="sxs-lookup"><span data-stu-id="7acdb-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="7acdb-117">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="7acdb-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="7acdb-118">Modifiez le **type** en **interface.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-118">Change the **Kind** to **Interface**.</span></span> <span data-ttu-id="7acdb-119">Nommez l’interface `IAuthenticationHelperCreatedListener` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-119">Name the interface `IAuthenticationHelperCreatedListener` and select **OK**.</span></span>

1. <span data-ttu-id="7acdb-120">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7acdb-120">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. <span data-ttu-id="7acdb-121">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="7acdb-121">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="7acdb-122">Nommez la classe `AuthenticationHelper` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-122">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="7acdb-123">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7acdb-123">Open the new file and replace its contents with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. <span data-ttu-id="7acdb-124">Ouvrez **MainActivity** et ajoutez les `import` instructions suivantes.</span><span class="sxs-lookup"><span data-stu-id="7acdb-124">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="7acdb-125">Ajoutez les propriétés de membre suivantes à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="7acdb-125">Add the following member properties to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. <span data-ttu-id="7acdb-126">Ajoutez la fonction suivante à la fin de la fonction `onCreate`.</span><span class="sxs-lookup"><span data-stu-id="7acdb-126">Add the following code to the end of the `onCreate` function.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. <span data-ttu-id="7acdb-127">Ajoutez les fonctions suivantes à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="7acdb-127">Add the following functions to the `MainActivity` class.</span></span>

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn(boolean shouldAttemptInteractive) {
        mAttemptInteractiveSignIn = shouldAttemptInteractive;
        mAuthHelper.acquireTokenSilently(getAuthCallback());
    }

    // Prompt the user to sign in
    private void doInteractiveSignIn() {
        mAuthHelper.acquireTokenInteractively(this, getAuthCallback());
    }

    // Handles the authentication result
    public AuthenticationCallback getAuthCallback() {
        return new AuthenticationCallback() {

            @Override
            public void onSuccess(IAuthenticationResult authenticationResult) {
                // Log the token for debug purposes
                String accessToken = authenticationResult.getAccessToken();
                Log.d("AUTH", String.format("Access token: %s", accessToken));

                hideProgressBar();

                setSignedInState(true);
                openHomeFragment(mUserName);
            }

            @Override
            public void onError(MsalException exception) {
                // Check the type of exception and handle appropriately
                if (exception instanceof MsalUiRequiredException) {
                    Log.d("AUTH", "Interactive login required");
                    if (mAttemptInteractiveSignIn) {
                        doInteractiveSignIn();
                    }
                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account" ||
                        exception.getErrorCode() == "no_account_found") {
                        Log.d("AUTH", "No current account, interactive login required");
                        if (mAttemptInteractiveSignIn) {
                            doInteractiveSignIn();
                        }
                    } else {
                        // Exception inside MSAL, more info inside MsalError.java
                        Log.e("AUTH", "Client error authenticating", exception);
                    }
                } else if (exception instanceof MsalServiceException) {
                    // Exception when communicating with the auth server, likely config issue
                    Log.e("AUTH", "Service error authenticating", exception);
                }
                hideProgressBar();
            }

            @Override
            public void onCancel() {
                // User canceled the authentication
                Log.d("AUTH", "Authentication canceled");
                hideProgressBar();
            }
        };
    }
    ```

1. <span data-ttu-id="7acdb-128">Remplacez les fonctions `signIn` `signOut` existantes et existantes par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7acdb-128">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > <span data-ttu-id="7acdb-129">Notez que `signIn` la méthode effectue une connectez-vous silencieuse (via `doSilentSignIn` ).</span><span class="sxs-lookup"><span data-stu-id="7acdb-129">Notice that the `signIn` method does a silent sign-in (via `doSilentSignIn`).</span></span> <span data-ttu-id="7acdb-130">Le rappel de cette méthode se connecte de manière interactive en cas d’échec du mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="7acdb-130">The callback for this method will do an interactive sign-in if the silent one fails.</span></span> <span data-ttu-id="7acdb-131">Cela évite d’avoir à inviter l’utilisateur chaque fois qu’il lance l’application.</span><span class="sxs-lookup"><span data-stu-id="7acdb-131">This avoids having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="7acdb-132">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="7acdb-132">Save your changes and run the app.</span></span>

1. <span data-ttu-id="7acdb-133">Lorsque vous appuyez sur **l’élément de** menu Connexion, un navigateur s’ouvre sur la page de connexion Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7acdb-133">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="7acdb-134">Connectez-vous à votre compte.</span><span class="sxs-lookup"><span data-stu-id="7acdb-134">Sign in with your account.</span></span>

<span data-ttu-id="7acdb-135">Une fois l’application reprise, vous devez voir un jeton d’accès imprimé dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="7acdb-135">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Capture d’écran de la fenêtre Logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="7acdb-137">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="7acdb-137">Get user details</span></span>

<span data-ttu-id="7acdb-138">Dans cette section, vous allez créer une classe d’aide pour contenir tous les appels à Microsoft Graph et mettre à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur `MainActivity` connecté.</span><span class="sxs-lookup"><span data-stu-id="7acdb-138">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="7acdb-139">Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**</span><span class="sxs-lookup"><span data-stu-id="7acdb-139">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="7acdb-140">Nommez la classe `GraphHelper` et sélectionnez **OK.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-140">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="7acdb-141">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7acdb-141">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import com.microsoft.graph.authentication.IAuthenticationProvider;
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.http.IHttpRequest;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    import com.microsoft.graph.requests.extensions.GraphServiceClient;

    // Singleton class - the app only needs a single instance
    // of the Graph client
    public class GraphHelper implements IAuthenticationProvider {
        private static GraphHelper INSTANCE = null;
        private IGraphServiceClient mClient = null;
        private String mAccessToken = null;

        private GraphHelper() {
            mClient = GraphServiceClient.builder()
                    .authenticationProvider(this).buildClient();
        }

        public static synchronized GraphHelper getInstance() {
            if (INSTANCE == null) {
                INSTANCE = new GraphHelper();
            }

            return INSTANCE;
        }

        // Part of the Graph IAuthenticationProvider interface
        // This method is called before sending the HTTP request
        @Override
        public void authenticateRequest(IHttpRequest request) {
            // Add the access token in the Authorization header
            request.addHeader("Authorization", "Bearer " + mAccessToken);
        }

        public void getUser(String accessToken, ICallback<User> callback) {
            mAccessToken = accessToken;

            // GET /me (logged in user)
            mClient.me().buildRequest()
                    .select("displayName,mail,mailboxSettings,userPrincipalName")
                    .get(callback);
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="7acdb-142">Prenez en compte ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="7acdb-142">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="7acdb-143">Il implémente `IAuthenticationProvider` l’interface pour insérer le jeton d’accès dans `Authorization` l’en-tête des demandes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="7acdb-143">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="7acdb-144">Il expose une fonction pour obtenir les informations de l’utilisateur connecté à partir du point de terminaison `getUser` `/me` Graph.</span><span class="sxs-lookup"><span data-stu-id="7acdb-144">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="7acdb-145">Ajoutez les `import` instructions suivantes en haut du **fichier MainActivity.**</span><span class="sxs-lookup"><span data-stu-id="7acdb-145">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="7acdb-146">Ajoutez la fonction suivante à la `MainActivity` classe pour générer un appel `ICallback` Graph.</span><span class="sxs-lookup"><span data-stu-id="7acdb-146">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. <span data-ttu-id="7acdb-147">Supprimez les lignes suivantes qui définissent le nom d’utilisateur et le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="7acdb-147">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="7acdb-148">Remplacez `onSuccess` le remplacement dans l’exemple `AuthenticationCallback` suivant.</span><span class="sxs-lookup"><span data-stu-id="7acdb-148">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. <span data-ttu-id="7acdb-149">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="7acdb-149">Save your changes and run the app.</span></span> <span data-ttu-id="7acdb-150">Une fois que l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse e-mail de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7acdb-150">After sign-in the UI is updated with the user's display name and email address.</span></span>
