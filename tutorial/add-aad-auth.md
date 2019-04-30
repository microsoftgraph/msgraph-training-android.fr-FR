<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="00759-101">Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="00759-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="00759-102">Cela est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="00759-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="00759-103">Dans cette étape, vous allez intégrer la [bibliothèque d'authentification Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) dans l'application.</span><span class="sxs-lookup"><span data-stu-id="00759-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) into the application.</span></span>

<span data-ttu-id="00759-104">Cliquez avec le bouton droit sur le dossier **app/res/values** et choisissez **nouveau**, puis fichier de **ressources de valeurs**.</span><span class="sxs-lookup"><span data-stu-id="00759-104">Right-click the **app/res/values** folder and choose **New**, then **Values resource file**.</span></span> <span data-ttu-id="00759-105">Nommez le `oauth_strings` fichier, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="00759-105">Name the file `oauth_strings` and choose **OK**.</span></span> <span data-ttu-id="00759-106">Ajoutez les valeurs suivantes à l' `resources` élément.</span><span class="sxs-lookup"><span data-stu-id="00759-106">Add the following values to the `resources` element.</span></span>

```xml
<string name="oauth_app_id">YOUR_APP_ID_HERE</string>
<string name="oauth_redirect_uri">msalYOUR_APP_ID_HERE</string>
<string-array name="oauth_scopes">
    <item>User.Read</item>
    <item>Calendars.Read</item>
</string-array>
```

> [!IMPORTANT]
> <span data-ttu-id="00759-107">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d'exclure le `oauth_strings.xml` fichier du contrôle de code source afin d'éviter une fuite accidentelle de votre ID d'application.</span><span class="sxs-lookup"><span data-stu-id="00759-107">If you're using source control such as git, now would be a good time to exclude the `oauth_strings.xml` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="00759-108">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="00759-108">Implement sign-in</span></span>

<span data-ttu-id="00759-109">Développez le dossier **app/Manifests** et ouvrez **AndroidManifest. xml**.</span><span class="sxs-lookup"><span data-stu-id="00759-109">Expand the **app/manifests** folder and open **AndroidManifest.xml**.</span></span> <span data-ttu-id="00759-110">Ajoutez les éléments suivants au- `application` dessus de l'élément.</span><span class="sxs-lookup"><span data-stu-id="00759-110">Add the following elements above the `application` element.</span></span>

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

<span data-ttu-id="00759-111">Ces autorisations sont requises pour que la bibliothèque MSAL authentifie l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="00759-111">These permissions are required in order for the MSAL library to authenticate the user.</span></span>

<span data-ttu-id="00759-112">À présent, ajoutez l'élément suivant `application` à l'intérieur de l'élément.</span><span class="sxs-lookup"><span data-stu-id="00759-112">Now add the following element inside the `application` element.</span></span>

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

<span data-ttu-id="00759-113">Cela permet à MSAL d'utiliser un navigateur pour authentifier l'utilisateur et d'enregistrer votre URI de redirection comme étant géré par l'application.</span><span class="sxs-lookup"><span data-stu-id="00759-113">This allows MSAL to use a browser to authenticate the user, and registers your redirect URI as being handled by the app.</span></span>

### <a name="implement-an-authentication-helper"></a><span data-ttu-id="00759-114">Implémenter une application d'assistance d'authentification</span><span class="sxs-lookup"><span data-stu-id="00759-114">Implement an authentication helper</span></span>

<span data-ttu-id="00759-115">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="00759-115">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="00759-116">Nommez la `AuthenticationHelper` classe et cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="00759-116">Name the class `AuthenticationHelper` and choose **OK**.</span></span> <span data-ttu-id="00759-117">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="00759-117">Open the new file and replace its contents with the following.</span></span>

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

<span data-ttu-id="00759-118">À présent `MainActivity` , mettez à jour pour utiliser cette nouvelle classe.</span><span class="sxs-lookup"><span data-stu-id="00759-118">Now update `MainActivity` to use this new class.</span></span> <span data-ttu-id="00759-119">Ouvrez **MainActivity** et ajoutez les instructions `import` suivantes.</span><span class="sxs-lookup"><span data-stu-id="00759-119">Open **MainActivity** and add the following `import` statements.</span></span>

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

<span data-ttu-id="00759-120">Ensuite, ajoutez la propriété de membre suivante à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="00759-120">Next, add the following member property to the `MainActivity` class.</span></span>

```java
private AuthenticationHelper mAuthHelper = null;
```

<span data-ttu-id="00759-121">Mise `onCreate` à jour `mAuthHelper`à définir.</span><span class="sxs-lookup"><span data-stu-id="00759-121">Update `onCreate` to set `mAuthHelper`.</span></span> <span data-ttu-id="00759-122">Ajoutez le code suivant à la fin de `onCreate` la fonction.</span><span class="sxs-lookup"><span data-stu-id="00759-122">Add the following to the end of the `onCreate` function.</span></span>

```java
// Get the authentication helper
mAuthHelper = AuthenticationHelper.getInstance(getApplicationContext());
```

<span data-ttu-id="00759-123">Ajoutez une substitution pour gérer `onActivityResult` les réponses d'authentification.</span><span class="sxs-lookup"><span data-stu-id="00759-123">Add an override for `onActivityResult` to handle authentication responses.</span></span>

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    mAuthHelper.handleRedirect(requestCode, resultCode, data);
}
```

<span data-ttu-id="00759-124">À présent, ajoutez les fonctions suivantes à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="00759-124">Now, add the following functions to the `MainActivity` class.</span></span>

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

<span data-ttu-id="00759-125">Enfin, remplacez les fonctions `signIn` et `signOut` existantes par les éléments suivants.</span><span class="sxs-lookup"><span data-stu-id="00759-125">Finally, replace the existing `signIn` and `signOut` functions with the following.</span></span>

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

<span data-ttu-id="00759-126">Notez que la `signIn` méthode vérifie d'abord s'il existe déjà un compte d'utilisateur dans le cache MSAL.</span><span class="sxs-lookup"><span data-stu-id="00759-126">Notice that the `signIn` method first checks if there is a user account already in the MSAL cache.</span></span> <span data-ttu-id="00759-127">Si c'est le cas, il essaie d'actualiser ses jetons de manière silencieuse, sans avoir à inviter l'utilisateur à chaque fois qu'ils lancent l'application.</span><span class="sxs-lookup"><span data-stu-id="00759-127">If there is, it attempts to refresh its tokens silently, avoiding having to prompt the user every time they launch the app.</span></span>

<span data-ttu-id="00759-128">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="00759-128">Save your changes and run the app.</span></span> <span data-ttu-id="00759-129">Lorsque vous cliquez sur l'élément de menu **connexion** , un navigateur s'ouvre sur la page de connexion Azure ad.</span><span class="sxs-lookup"><span data-stu-id="00759-129">When you tap the **Sign in** menu item, a browser opens to the Azure AD login page.</span></span> <span data-ttu-id="00759-130">Connectez-vous avec votre compte.</span><span class="sxs-lookup"><span data-stu-id="00759-130">Sign in with your account.</span></span> <span data-ttu-id="00759-131">Une fois l'application reprise, vous devriez voir un jeton d'accès imprimé dans le journal de débogage dans Android Studio.</span><span class="sxs-lookup"><span data-stu-id="00759-131">Once the app resumes, you should see an access token printed in the debug log in Android Studio.</span></span>

![Capture d'écran de la fenêtre logcat dans Android Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="00759-133">Obtenir les détails de l'utilisateur</span><span class="sxs-lookup"><span data-stu-id="00759-133">Get user details</span></span>

<span data-ttu-id="00759-134">Commencez par créer une classe d'assistance qui contiendra tous les appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="00759-134">Start by creating a helper class to hold all of the calls to Microsoft Graph.</span></span> <span data-ttu-id="00759-135">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="00759-135">Right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="00759-136">Nommez la `GraphHelper` classe et cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="00759-136">Name the class `GraphHelper` and choose **OK**.</span></span> <span data-ttu-id="00759-137">Ouvrez le nouveau fichier et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="00759-137">Open the new file and replace its contents with the following.</span></span>

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

<span data-ttu-id="00759-138">Notez ce que fait ce code.</span><span class="sxs-lookup"><span data-stu-id="00759-138">Note what this code does.</span></span>

- <span data-ttu-id="00759-139">Elle implémente l' `IAuthenticationProvider` interface pour insérer le jeton d'accès dans `Authorization` l'en-tête des demandes http sortantes.</span><span class="sxs-lookup"><span data-stu-id="00759-139">It implements the `IAuthenticationProvider` interface to insert the access token in the `Authorization` header on outgoing HTTP requests.</span></span>
- <span data-ttu-id="00759-140">Il expose une `getUser` fonction pour obtenir les informations de l'utilisateur connecté à partir du `/me` point de terminaison Graph.</span><span class="sxs-lookup"><span data-stu-id="00759-140">It exposes a `getUser` function to get the logged-in user's information from the `/me` Graph endpoint.</span></span>

<span data-ttu-id="00759-141">À présent, `MainActivity` mettez à jour la classe pour utiliser cette nouvelle classe afin d'obtenir l'utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="00759-141">Now update the `MainActivity` class to use this new class to get the logged-in user.</span></span> <span data-ttu-id="00759-142">Ajoutez les instructions `import` suivantes en haut du fichier **MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="00759-142">Add the following `import` statements to the top of the **MainActivity** file.</span></span>

```java
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.models.extensions.IGraphServiceClient;
import com.microsoft.graph.models.extensions.User;
```

<span data-ttu-id="00759-143">Ensuite, ajoutez la fonction suivante à la `MainActivity` classe pour générer un `ICallback` pour l'appel Graph.</span><span class="sxs-lookup"><span data-stu-id="00759-143">Next, add the following function to the `MainActivity` class to generate an `ICallback` for the Graph call.</span></span>

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

<span data-ttu-id="00759-144">Supprimez les lignes suivantes qui définissent le nom d'utilisateur et l'adresse de messagerie:</span><span class="sxs-lookup"><span data-stu-id="00759-144">Remove the following lines that set the user name and email:</span></span>

```java
// For testing
mUserName = "Megan Bowen";
mUserEmail = "meganb@contoso.com";
```

<span data-ttu-id="00759-145">Enfin, remplacez le `onSuccess` remplacement dans le `AuthenticationCallback` par le suivant.</span><span class="sxs-lookup"><span data-stu-id="00759-145">Finally, replace the `onSuccess` override in the `AuthenticationCallback` with the following.</span></span>

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

<span data-ttu-id="00759-146">Si vous enregistrez vos modifications et exécutez l'application maintenant, une fois connecté, l'interface utilisateur est mise à jour avec le nom d'affichage et l'adresse de messagerie de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="00759-146">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
