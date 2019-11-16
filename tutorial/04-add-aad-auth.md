<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="04415-101">Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="04415-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="04415-102">Cela est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="04415-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="04415-103">Pour ce faire, vous allez intégrer la [bibliothèque d’authentification Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l’application.</span><span class="sxs-lookup"><span data-stu-id="04415-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

1. <span data-ttu-id="04415-104">Cliquez avec le bouton droit sur le dossier **res** , sélectionnez **nouveau**, puis **Répertoire de ressources Android**.</span><span class="sxs-lookup"><span data-stu-id="04415-104">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="04415-105">Modifiez le **type** de `raw` ressource et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="04415-105">Change the **Resource type** to `raw` and select **OK**.</span></span>

1. <span data-ttu-id="04415-106">Cliquez avec le bouton droit sur le nouveau dossier **RAW** , sélectionnez **nouveau**, puis **fichier**.</span><span class="sxs-lookup"><span data-stu-id="04415-106">Right-click the new **raw** folder and select **New**, then **File**.</span></span>

1. <span data-ttu-id="04415-107">Nommez le `msal_config.json` fichier, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="04415-107">Name the file `msal_config.json` and select **OK**.</span></span>

1. <span data-ttu-id="04415-108">Ajoutez les éléments suivants au fichier **msal_config. JSON** .</span><span class="sxs-lookup"><span data-stu-id="04415-108">Add the following to the **msal_config.json** file.</span></span>

    ```json
    {
      "client_id" : "YOUR_APP_ID_HERE",
      "redirect_uri" : "msauth://YOUR_PACKAGE_NAME_HERE/callback",
      "broker_redirect_uri_registered": false,
      "account_mode": "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount"
          },
          "default": true
        }
      ]
    }
    ```

    <span data-ttu-id="04415-109">Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de l’application `YOUR_PACKAGE_NAME_HERE` et remplacez par le nom du package de votre projet.</span><span class="sxs-lookup"><span data-stu-id="04415-109">Replace `YOUR_APP_ID_HERE` with the app ID from your app registration, and replace `YOUR_PACKAGE_NAME_HERE` with your project's package name.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="04415-110">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d’exclure le `msal_config.json` fichier du contrôle de code source afin d’éviter une fuite accidentelle de votre ID d’application.</span><span class="sxs-lookup"><span data-stu-id="04415-110">If you're using source control such as git, now would be a good time to exclude the `msal_config.json` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="04415-111">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="04415-111">Implement sign-in</span></span>

<span data-ttu-id="04415-112">Dans cette section, vous allez mettre à jour le manifeste pour permettre à MSAL d’utiliser un navigateur pour authentifier l’utilisateur, enregistrer votre URI de redirection comme étant géré par l’application, créer une classe d’assistance d’authentification et mettre à jour l’application pour se connecter et se déconnecter.</span><span class="sxs-lookup"><span data-stu-id="04415-112">In this section you will update the manifest to allow MSAL to use a browser to authenticate the user, register your redirect URI as being handled by the app, create an authentication helper class, and update the app to sign in and sign out.</span></span>

1. <span data-ttu-id="04415-113">Développez le dossier **app/Manifests** et ouvrez **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="04415-113">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="04415-114">Ajoutez les éléments suivants au- `application` dessus de l’élément.</span><span class="sxs-lookup"><span data-stu-id="04415-114">Add the following elements above the `application` element.</span></span>

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > <span data-ttu-id="04415-115">Ces autorisations sont requises pour que la bibliothèque MSAL authentifie l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="04415-115">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

1. <span data-ttu-id="04415-116">Ajoutez l’élément suivant à l' `application` intérieur de l’élément `YOUR_PACKAGE_NAME_HERE` , en remplaçant la chaîne par le nom de votre package.</span><span class="sxs-lookup"><span data-stu-id="04415-116">Add the following element inside the `application` element, replacing the `YOUR_PACKAGE_NAME_HERE` string with your package name.</span></span>

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

1. <span data-ttu-id="04415-117">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et sélectionnez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="04415-117">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span> <span data-ttu-id="04415-118">Nommez la `AuthenticationHelper` classe et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="04415-118">Name the class `AuthenticationHelper` and select **OK**.</span></span>

1. <span data-ttu-id="04415-119">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="04415-119">Open the new file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.app.Activity;
    import android.content.Context;
    import android.util.Log;
    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IPublicClientApplication;
    import com.microsoft.identity.client.ISingleAccountPublicClientApplication;
    import com.microsoft.identity.client.PublicClientApplication;
    import com.microsoft.identity.client.exception.MsalException;

    // Singleton class - the app only needs a single instance
    // of PublicClientApplication
    public class AuthenticationHelper {
        private static AuthenticationHelper INSTANCE = null;
        private ISingleAccountPublicClientApplication mPCA = null;
        private String[] mScopes = { "User.Read", "Calendars.Read" };

        private AuthenticationHelper(Context ctx) {
            PublicClientApplication.createSingleAccountPublicClientApplication(ctx, R.raw.msal_config,
                new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                    @Override
                    public void onCreated(ISingleAccountPublicClientApplication application) {
                        mPCA = application;
                    }

                    @Override
                    public void onError(MsalException exception) {
                        Log.e("AUTHHELPER", "Error creating MSAL application", exception);
                    }
                });
        }

        public static synchronized AuthenticationHelper getInstance(Context ctx) {
            if (INSTANCE == null) {
                INSTANCE = new AuthenticationHelper(ctx);
            }

            return INSTANCE;
        }

        // Version called from fragments. Does not create an
        // instance if one doesn't exist
        public static synchronized AuthenticationHelper getInstance() {
            if (INSTANCE == null) {
                throw new IllegalStateException(
                    "AuthenticationHelper has not been initialized from MainActivity");
            }

            return INSTANCE;
        }

        public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
            mPCA.signIn(activity, null, mScopes, callback);
        }

        public void acquireTokenSilently(AuthenticationCallback callback) {
            // Get the authority from MSAL config
            String authority = mPCA.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();
            mPCA.acquireTokenSilentAsync(mScopes, authority, callback);
        }

        public void signOut() {
            mPCA.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                @Override
                public void onSignOut() {
                    Log.d("AUTHHELPER", "Signed out");
                }

                @Override
                public void onError(@NonNull MsalException exception) {
                    Log.d("AUTHHELPER", "MSAL error signing out", exception);
                }
            });
        }
    }
    ```

1. <span data-ttu-id="04415-120">Ouvrez **MainActivity** et ajoutez les instructions `import` suivantes.</span><span class="sxs-lookup"><span data-stu-id="04415-120">Open **MainActivity** and add the following `import` statements.</span></span>

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. <span data-ttu-id="04415-121">Ajoutez la propriété de membre suivante à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="04415-121">Add the following member property to the `MainActivity` class.</span></span>

    ```java
    private AuthenticationHelper mAuthHelper = null;
    ```

1. <span data-ttu-id="04415-122">Ajoutez le code suivant à la fin de `onCreate` la fonction.</span><span class="sxs-lookup"><span data-stu-id="04415-122">Add the following to the end of the `onCreate` function.</span></span>

    ```java
    // Get the authentication helper
    mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
    ```

1. <span data-ttu-id="04415-123">Ajoutez les fonctions suivantes à la `MainActivity` classe.</span><span class="sxs-lookup"><span data-stu-id="04415-123">Add the following functions to the `MainActivity` class.</span></span>

    ```java
    // Silently sign in - used if there is already a
    // user account in the MSAL cache
    private void doSilentSignIn() {
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
                    doInteractiveSignIn();

                } else if (exception instanceof MsalClientException) {
                    if (exception.getErrorCode() == "no_current_account") {
                        Log.d("AUTH", "No current account, interactive login required");
                        doInteractiveSignIn();
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

1. <span data-ttu-id="04415-124">Remplacez les fonctions `signIn` et `signOut` existantes par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="04415-124">Replace the existing `signIn` and `signOut` functions with the following.</span></span>

    ```java
    private void signIn() {
        showProgressBar();
        // Attempt silent sign in first
        // if this fails, the callback will handle doing
        // interactive sign in
        doSilentSignIn();
    }

    private void signOut() {
        mAuthHelper.signOut();

        setSignedInState(false);
        openHomeFragment(mUserName);
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="04415-125">Notez que la `signIn` méthode vérifie d’abord s’il existe déjà un compte d’utilisateur dans le cache MSAL.</span><span class="sxs-lookup"><span data-stu-id="04415-125">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="04415-126">Si c’est le cas, il essaie d’actualiser ses jetons de manière silencieuse, sans avoir à inviter l’utilisateur à chaque fois qu’ils lancent l’application.</span><span class="sxs-lookup"><span data-stu-id="04415-126">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

1. <span data-ttu-id="04415-127">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="04415-127">Save your changes and run the app.</span></span>

1. <span data-ttu-id="04415-128">Lorsque vous cliquez sur l’élément de menu **connexion** , un navigateur s’ouvre sur la page de connexion Azure ad.</span><span class="sxs-lookup"><span data-stu-id="04415-128">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="04415-129">Connectez-vous à votre compte.</span><span class="sxs-lookup"><span data-stu-id="04415-129">Sign in with your account.</span></span>

<span data-ttu-id="04415-130">Une fois l’application reprise, vous devriez voir un jeton d’accès imprimé dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="04415-130">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Capture d’écran de la fenêtre logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="04415-132">Obtenir les détails de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="04415-132">Get user details</span></span>

<span data-ttu-id="04415-133">Dans cette section, vous allez créer une classe d’assistance qui contiendra tous les appels à Microsoft Graph et mettre `MainActivity` à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="04415-133">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `MainActivity` class to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="04415-134">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et sélectionnez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="04415-134">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="04415-135">Nommez la `GraphHelper` classe et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="04415-135">Name the class `GraphHelper` and select **OK**.</span></span>

1. <span data-ttu-id="04415-136">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="04415-136">Open the new file and replace its contents with the following.</span></span>

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
            mClient.me().buildRequest().get(callback);
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="04415-137">Examinez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="04415-137">Consider what this code does.</span></span>
    >
    > - <span data-ttu-id="04415-138">Elle implémente l' `IAuthenticationProvider` interface pour insérer le jeton d’accès dans `Authorization` l’en-tête des demandes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="04415-138">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
    > - <span data-ttu-id="04415-139">Il expose une `getUser` fonction pour obtenir les informations de l’utilisateur connecté à partir du `/me` point de terminaison Graph.</span><span class="sxs-lookup"><span data-stu-id="04415-139">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

1. <span data-ttu-id="04415-140">Ajoutez les instructions `import` suivantes en haut du fichier **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="04415-140">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.IGraphServiceClient;
    import com.microsoft.graph.models.extensions.User;
    ```

1. <span data-ttu-id="04415-141">Ajoutez la fonction suivante à la `MainActivity` classe pour générer un `ICallback` pour l’appel Graph.</span><span class="sxs-lookup"><span data-stu-id="04415-141">Add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

    ```java
    private ICallback<User> getUserCallback() {
        return new ICallback<User>() {
            @Override
            public void success(User user) {
                Log.d("AUTH", "User: " + user.displayName);

                mUserName = user.displayName;
                mUserEmail = user.mail == null ? user.userPrincipalName : user.mail;

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });

            }

            @Override
            public void failure(ClientException ex) {
                Log.e("AUTH", "Error getting /me", ex);
                mUserName = "ERROR";
                mUserEmail = "ERROR";

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        hideProgressBar();

                        setSignedInState(true);
                        openHomeFragment(mUserName);
                    }
                });
            }
        };
    }
    ```

1. <span data-ttu-id="04415-142">Supprimez les lignes suivantes qui définissent le nom d’utilisateur et l’adresse de messagerie :</span><span class="sxs-lookup"><span data-stu-id="04415-142">Remove the following lines that set the user name and email:</span></span>

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. <span data-ttu-id="04415-143">Remplacez le `onSuccess` remplacement dans le `AuthenticationCallback` par le suivant.</span><span class="sxs-lookup"><span data-stu-id="04415-143">Replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

    ```java
    @Override
    public void onSuccess(AuthenticationResult authenticationResult) {
        // Log the token for debug purposes
        String accessToken = authenticationResult.getAccessToken();
        Log.d("AUTH", String.format("Access token: %s", accessToken));

        // Get Graph client and get user
        GraphHelper graphHelper = GraphHelper.getInstance();
        graphHelper.getUser(accessToken, getUserCallback());
    }
    ```

<span data-ttu-id="04415-144">Si vous enregistrez vos modifications et exécutez l’application maintenant, une fois connecté, l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse de messagerie de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="04415-144">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
