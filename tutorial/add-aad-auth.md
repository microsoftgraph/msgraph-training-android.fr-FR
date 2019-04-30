<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD. Cela est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph. Dans cette étape, vous allez intégrer la [bibliothèque d'authentification Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l'application.

Cliquez avec le bouton droit sur le dossier **app/res/values** et choisissez **nouveau**, puis fichier de **ressources de valeurs**. Nommez le `oauth_strings` fichier, puis cliquez sur **OK**. Ajoutez les valeurs suivantes à l' `resources` élément.

```xml
<string name="oauth_app_id">YOUR_APP_ID_HERE</string>
<string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
<string-array name="oauth_scopes">
    <item>User.Read</item>
    <item>Calendars.Read</item>
</string-array>
```

> [!IMPORTANT]
> Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d'exclure le `oauth_strings.xml` fichier du contrôle de code source afin d'éviter une fuite accidentelle de votre ID d'application.

## <a name="implement-sign-in"></a>Mettre en œuvre la connexion

Développez le dossier **app/Manifests** et ouvrez **AndroidManifest. xml**. Ajoutez les éléments suivants au- `application` dessus de l'élément.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Ces autorisations sont requises pour que la bibliothèque MSAL authentifie l'utilisateur.

À présent, ajoutez l'élément suivant `application` à l'intérieur de l'élément.

```xml
<activity android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />

        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <data
            android:host="auth"
            android:scheme="@string/oauth_redirect_uri" />
    </intent-filter>
</activity>
```

Cela permet à MSAL d'utiliser un navigateur pour authentifier l'utilisateur et d'enregistrer votre URI de redirection comme étant géré par l'application.

### <a name="implement-an-authentication-helper"></a>Implémenter une application d'assistance d'authentification

Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **nouveau**, puis **classe Java**. Nommez la `AuthenticationHelper` classe et cliquez sur **OK**. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

```java
package com.example.graphtutorial;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;

import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.IAccount;
import com.microsoft.identity.client.PublicClientApplication;

// Singleton class - the app only needs a single instance
// of PublicClientApplication
public class AuthenticationHelper {
    private static AuthenticationHelper INSTANCE = null;
    private PublicClientApplication mPCA = null;
    private String[] mScopes;

    private AuthenticationHelper(Context ctx) {
        String appId = ctx.getResources().getString(R.string.oauth_app_id);
        mScopes = ctx.getResources().getStringArray(R.array.oauth_scopes);
        mPCA = new PublicClientApplication(ctx, appId);
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

    public boolean hasAccount() {
        return !mPCA.getAccounts().isEmpty();
    }

    public void handleRedirect(int requestCode, int resultCode, Intent data) {
        mPCA.handleInteractiveRequestRedirect(requestCode, resultCode, data);
    }

    public void acquireTokenInteractively(Activity activity, AuthenticationCallback callback) {
        mPCA.acquireToken(activity, mScopes, callback);
    }

    public void acquireTokenSilently(AuthenticationCallback callback) {
        mPCA.acquireTokenSilentAsync(mScopes, mPCA.getAccounts().get(0), callback);
    }

    public void signOut() {
        for (IAccount account : mPCA.getAccounts()) {
            mPCA.removeAccount(account);
        }
    }
}
```

À présent `MainActivity` , mettez à jour pour utiliser cette nouvelle classe. Ouvrez **MainActivity** et ajoutez les instructions `import` suivantes.

```java
import android.content.Intent;
import android.support.annotation.Nullable;
import android.util.Log;

import com.microsoft.identity.client.AuthenticationCallback;
import com.microsoft.identity.client.AuthenticationResult;
import com.microsoft.identity.client.exception.MsalClientException;
import com.microsoft.identity.client.exception.MsalException;
import com.microsoft.identity.client.exception.MsalServiceException;
import com.microsoft.identity.client.exception.MsalUiRequiredException;
```

Ensuite, ajoutez la propriété de membre suivante à `MainActivity` la classe.

```java
private AuthenticationHelper mAuthHelper = null;
```

Mise `onCreate` à jour `mAuthHelper`à définir. Ajoutez le code suivant à la fin de `onCreate` la fonction.

```java
// Get the authentication helper
mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
```

Ajoutez une substitution pour gérer `onActivityResult` les réponses d'authentification.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    mAuthHelper.handleRedirect(requestCode, resultCode, data);
}
```

À présent, ajoutez les fonctions suivantes à `MainActivity` la classe.

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
        public void onSuccess(AuthenticationResult authenticationResult) {
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
                // Exception inside MSAL, more info inside MsalError.java
                Log.e("AUTH", "Client error authenticating", exception);
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

Enfin, remplacez les fonctions `signIn` et `signOut` existantes par les éléments suivants.

```java
private void signIn() {
    showProgressBar();
    if (mAuthHelper.hasAccount()) {
        doSilentSignIn();
    } else {
        doInteractiveSignIn();
    }
}

private void signOut() {
    mAuthHelper.signOut();

    setSignedInState(false);
    openHomeFragment(mUserName);
}
```

Notez que la `signIn` méthode vérifie d'abord s'il existe déjà un compte d'utilisateur dans le cache MSAL. Si c'est le cas, il essaie d'actualiser ses jetons de manière silencieuse, sans avoir à inviter l'utilisateur à chaque fois qu'ils lancent l'application.

Enregistrez vos modifications et exécutez l’application. Lorsque vous cliquez sur l'élément de menu **connexion** , un navigateur s'ouvre sur la page de connexion Azure ad. Connectez-vous avec votre compte. Une fois l'application reprise, vous devriez voir un jeton d'accès imprimé dans le journal de débogage dans Android Studio.

![Capture d'écran de la fenêtre logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a>Obtenir les détails de l'utilisateur

Commencez par créer une classe d'assistance qui contiendra tous les appels à Microsoft Graph. Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **nouveau**, puis **classe Java**. Nommez la `GraphHelper` classe et cliquez sur **OK**. Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.

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

Notez ce que fait ce code.

- Elle implémente l' `IAuthenticationProvider` interface pour insérer le jeton d'accès dans `Authorization` l'en-tête des demandes http sortantes.
- Il expose une `getUser` fonction pour obtenir les informations de l'utilisateur connecté à partir du `/me` point de terminaison Graph.

À présent, `MainActivity` mettez à jour la classe pour utiliser cette nouvelle classe afin d'obtenir l'utilisateur connecté. Ajoutez les instructions `import` suivantes en haut du fichier **MainActivity** .

```java
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.IGraphServiceClient;
import com.microsoft.graph.models.extensions.User;
```

Ensuite, ajoutez la fonction suivante à la `MainActivity` classe pour générer un `ICallback` pour l'appel Graph.

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

Supprimez les lignes suivantes qui définissent le nom d'utilisateur et l'adresse de messagerie:

```java
// For testing
mUserName = "Megan Bowen";
mUserEmail = "meganb@contoso.com";
```

Enfin, remplacez le `onSuccess` remplacement dans le `AuthenticationCallback` par le suivant.

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

Si vous enregistrez vos modifications et exécutez l'application maintenant, une fois connecté, l'interface utilisateur est mise à jour avec le nom d'affichage et l'adresse de messagerie de l'utilisateur.
