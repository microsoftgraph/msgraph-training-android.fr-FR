<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l’application de l’exercice précédent pour prendre en charge l’authentification avec Azure AD. Cette étape est nécessaire pour obtenir le jeton d’accès OAuth nécessaire pour appeler Microsoft Graph. Pour ce faire, vous allez intégrer la bibliothèque d’authentification [Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l’application.

1. Cliquez avec le bouton droit **sur le dossier res** et **sélectionnez Nouveau,** puis **Répertoire de ressources Android.**

1. Modifiez **le type de ressource** et `raw` sélectionnez **OK.**

1. Cliquez avec le bouton droit sur le nouveau **dossier** brut et **sélectionnez Nouveau,** puis **Fichier**.

1. Nommez le fichier `msal_config.json` et sélectionnez **OK.**

1. Ajoutez ce qui suit au **fichiermsal_config.jssur.**

    :::code language="json" source="../demo/GraphTutorial/msal_config.json.example":::

1. Remplacez `YOUR_APP_ID_HERE` par l’ID d’application de l’inscription de votre application et par le `com.example.graphtutorial` nom du package de votre projet.

    > [!IMPORTANT]
    > Si vous utilisez un contrôle source tel que Git, il est temps d’exclure le fichier du contrôle source afin d’éviter toute fuite accidentelle de votre `msal_config.json` ID d’application.

## <a name="implement-sign-in"></a>Implémentation de la connexion

Dans cette section, vous allez mettre à jour le manifeste pour permettre à MSAL d’utiliser un navigateur pour authentifier l’utilisateur, inscrire votre URI de redirection comme étant géré par l’application, créer une classe d’aide à l’authentification et mettre à jour l’application pour se connecter et se dé dévier.

1. Développez **le dossier application/manifestes** et **ouvrezAndroidManifest.xml**. Ajoutez les éléments suivants au-dessus de `application` l’élément.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

    > [!NOTE]
    > Ces autorisations sont requises pour que la bibliothèque MSAL authentifier l’utilisateur.

1. Ajoutez l’élément suivant à l’intérieur `application` de l’élément, en remplaçant la `YOUR_PACKAGE_NAME_HERE` chaîne par le nom de votre package.

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

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .** Modifiez le **type** en **interface.** Nommez l’interface `IAuthenticationHelperCreatedListener` et sélectionnez **OK.**

1. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/IAuthenticationHelperCreatedListener.java" id="ListenerSnippet":::

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .** Nommez la classe `AuthenticationHelper` et sélectionnez **OK.**

1. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/AuthenticationHelper.java" id="AuthHelperSnippet":::

1. Ouvrez **MainActivity** et ajoutez les `import` instructions suivantes.

    ```java
    import android.util.Log;

    import com.microsoft.identity.client.AuthenticationCallback;
    import com.microsoft.identity.client.IAuthenticationResult;
    import com.microsoft.identity.client.exception.MsalClientException;
    import com.microsoft.identity.client.exception.MsalException;
    import com.microsoft.identity.client.exception.MsalServiceException;
    import com.microsoft.identity.client.exception.MsalUiRequiredException;
    ```

1. Ajoutez les propriétés de membre suivantes à la `MainActivity` classe.

    ```java
    private AuthenticationHelper mAuthHelper = null;
    private boolean mAttemptInteractiveSignIn = false;
    ```

1. Ajoutez la fonction suivante à la fin de la fonction `onCreate`.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="InitialLoginSnippet":::

1. Ajoutez les fonctions suivantes à la `MainActivity` classe.

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

1. Remplacez les fonctions `signIn` `signOut` existantes et existantes par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="SignInAndOutSnippet":::

    > [!NOTE]
    > Notez que `signIn` la méthode effectue une connectez-vous silencieuse (via `doSilentSignIn` ). Le rappel de cette méthode se connecte de manière interactive en cas d’échec du mode silencieux. Cela évite d’avoir à inviter l’utilisateur chaque fois qu’il lance l’application.

1. Enregistrez vos modifications et exécutez l’application.

1. Lorsque vous appuyez sur **l’élément de** menu Connexion, un navigateur s’ouvre sur la page de connexion Azure AD. Connectez-vous à votre compte.

Une fois l’application reprise, vous devez voir un jeton d’accès imprimé dans le journal de débogage dans Android Studio.

![Capture d’écran de la fenêtre Logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtenir les détails de l’utilisateur

Dans cette section, vous allez créer une classe d’aide pour contenir tous les appels à Microsoft Graph et mettre à jour la classe pour utiliser cette nouvelle classe afin d’obtenir l’utilisateur `MainActivity` connecté.

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .** Nommez la classe `GraphHelper` et sélectionnez **OK.**

1. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

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
    > Prenez en compte ce que fait ce code.
    >
    > - Il implémente `IAuthenticationProvider` l’interface pour insérer le jeton d’accès dans `Authorization` l’en-tête des demandes HTTP sortantes.
    > - Il expose une fonction pour obtenir les informations de l’utilisateur connecté à partir du point de terminaison `getUser` `/me` Graph.

1. Ajoutez les `import` instructions suivantes en haut du **fichier MainActivity.**

    ```java
    import com.microsoft.graph.concurrency.ICallback;
    import com.microsoft.graph.core.ClientException;
    import com.microsoft.graph.models.extensions.User;
    ```

1. Ajoutez la fonction suivante à la `MainActivity` classe pour générer un appel `ICallback` Graph.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="GetUserCallbackSnippet":::

1. Supprimez les lignes suivantes qui définissent le nom d’utilisateur et le courrier électronique :

    ```java
    // For testing
    mUserName = "Megan Bowen";
    mUserEmail = "meganb@contoso.com";
    ```

1. Remplacez `onSuccess` le remplacement dans l’exemple `AuthenticationCallback` suivant.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnSuccessSnippet":::

1. Enregistrez vos modifications et exécutez l’application. Une fois que l’interface utilisateur est mise à jour avec le nom d’affichage et l’adresse e-mail de l’utilisateur.
