<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="9af07-101">Ouvrez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio** dans l'écran d'accueil.</span><span class="sxs-lookup"><span data-stu-id="9af07-101">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span> <span data-ttu-id="9af07-102">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **activité vide**, puis **suivant**.</span><span class="sxs-lookup"><span data-stu-id="9af07-102">In the **Create New Project** dialog, select **Empty Activity**, then choose **Next**.</span></span>

![Capture d'écran de la boîte de dialogue créer un nouveau projet dans Android Studio](./images/choose-project.png)

<span data-ttu-id="9af07-104">Dans la boîte de dialogue **configurer votre projet** , \*\*\*\* définissez le `Graph Tutorial`nom sur, vérifiez que le champ **langue** `Java`est défini sur et assurez-vous que le `API 27: Android 8.1 (Oreo)`niveau d' **API minimal** est défini sur.</span><span class="sxs-lookup"><span data-stu-id="9af07-104">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 27: Android 8.1 (Oreo)`.</span></span> <span data-ttu-id="9af07-105">Modifiez le **nom du package** et **Enregistrez** -le si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9af07-105">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="9af07-106">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="9af07-106">Select **Finish**.</span></span>

![Capture d'écran de la boîte de dialogue Configurer votre projet](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="9af07-108">Assurez-vous d'entrer exactement le même nom pour le projet spécifié dans les instructions de l'atelier.</span><span class="sxs-lookup"><span data-stu-id="9af07-108">Ensure that you enter the exact same name for the project that is specified in these lab instructions.</span></span> <span data-ttu-id="9af07-109">Le nom du projet devient partie intégrante de l'espace de noms dans le code.</span><span class="sxs-lookup"><span data-stu-id="9af07-109">The project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="9af07-110">Le code à l'intérieur de ces instructions dépend de l'espace de noms correspondant au nom de projet spécifié dans ces instructions.</span><span class="sxs-lookup"><span data-stu-id="9af07-110">The code inside these instructions depends on the namespace matching the project name specified in these instructions.</span></span> <span data-ttu-id="9af07-111">Si vous utilisez un nom de projet différent, le code n'est pas compilé, sauf si vous ajustez tous les espaces de noms pour qu'ils correspondent au nom de projet que vous entrez lors de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="9af07-111">If you use a different project name the code will not compile unless you adjust all the namespaces to match the project name you enter when you create the project.</span></span>

<span data-ttu-id="9af07-112">Avant de poursuivre, installez des dépendances supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="9af07-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="9af07-113">`com.android.support:design`pour que les dispositions de tiroir de navigation soient disponibles pour l'application.</span><span class="sxs-lookup"><span data-stu-id="9af07-113">`com.android.support:design` to make the navigation drawer layouts available to the app.</span></span>
- <span data-ttu-id="9af07-114">[Bibliothèque d'authentification Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) afin de gérer l'authentification Azure ad et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="9af07-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="9af07-115">[Kit de développement logiciel (SDK) Microsoft Graph pour Java](https://github.com/microsoftgraph/msgraph-sdk-java) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="9af07-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="9af07-116">Développez **scripts Gradle**, puis ouvrez le fichier **Build. Gradle (module: App)** .</span><span class="sxs-lookup"><span data-stu-id="9af07-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span> <span data-ttu-id="9af07-117">Ajoutez les lignes suivantes à l' `dependencies` intérieur de la valeur.</span><span class="sxs-lookup"><span data-stu-id="9af07-117">Add the following lines inside the `dependencies` value.</span></span>

```Gradle
implementation 'com.android.support:design:28.0.0'
implementation 'com.microsoft.graph:microsoft-graph:1.1.+'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

> [!NOTE]
> <span data-ttu-id="9af07-118">Si vous utilisez une version de kit de développement logiciel (SDK) différente `28.0.0` , veillez à modifier la `com.android.support:appcompat-v7` pour qu'elle corresponde à la version de la dépendance déjà présente dans **Build. gradle**.</span><span class="sxs-lookup"><span data-stu-id="9af07-118">If you are using a different SDK version, make sure to change the `28.0.0` to match the version of the `com.android.support:appcompat-v7` dependency already present in **build.gradle**.</span></span>

<span data-ttu-id="9af07-119">Ajoutez un `packagingOptions` à l' `android` intérieur de la valeur dans le fichier **Build. gradle (module: App)** .</span><span class="sxs-lookup"><span data-stu-id="9af07-119">Add a `packagingOptions` inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

```Gradle
packagingOptions {
    pickFirst 'META-INF/jersey-module-version'
}
```

<span data-ttu-id="9af07-120">Enregistrez vos modifications.</span><span class="sxs-lookup"><span data-stu-id="9af07-120">Save your changes.</span></span> <span data-ttu-id="9af07-121">Dans le menu **fichier** , sélectionnez **synchroniser le projet avec les fichiers Gradle**.</span><span class="sxs-lookup"><span data-stu-id="9af07-121">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="9af07-122">Concevoir l'application</span><span class="sxs-lookup"><span data-stu-id="9af07-122">Design the app</span></span>

<span data-ttu-id="9af07-123">L'application utilise un [tiroir de navigation](https://developer.android.com/training/implementing-navigation/nav-drawer) pour naviguer entre les différentes vues.</span><span class="sxs-lookup"><span data-stu-id="9af07-123">The application will use a [navigation drawer](https://developer.android.com/training/implementing-navigation/nav-drawer) to navigate between different views.</span></span> <span data-ttu-id="9af07-124">Dans cette étape, vous allez mettre à jour l'activité pour utiliser une disposition de tiroir de navigation et ajouter des fragments pour les vues.</span><span class="sxs-lookup"><span data-stu-id="9af07-124">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="9af07-125">Créer un tiroir de navigation</span><span class="sxs-lookup"><span data-stu-id="9af07-125">Create a navigation drawer</span></span>

<span data-ttu-id="9af07-126">Commencez par créer des icônes pour le menu de navigation de l'application.</span><span class="sxs-lookup"><span data-stu-id="9af07-126">Start by creating icons for the app's navigation menu.</span></span> <span data-ttu-id="9af07-127">Cliquez avec le bouton droit sur le dossier **app/res/Drawable** et sélectionnez **nouveau**, puis **élément vectoriel**.</span><span class="sxs-lookup"><span data-stu-id="9af07-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span> <span data-ttu-id="9af07-128">Cliquez sur le bouton de l'icône en regard de image **clipart**.</span><span class="sxs-lookup"><span data-stu-id="9af07-128">Click the icon button next to **Clip Art**.</span></span> <span data-ttu-id="9af07-129">Dans la **fenêtre Sélectionner une icône** , `home` saisissez dans la barre de recherche, puis sélectionnez l'icône **Accueil** et cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and choose **OK**.</span></span> <span data-ttu-id="9af07-130">Remplacez le **nom** par `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="9af07-130">Change the **Name** to `ic_menu_home`.</span></span>

![Capture d'écran de la fenêtre Configurer une ressource vectorielle](./images/create-icon.png)

<span data-ttu-id="9af07-132">Choisissez **suivant**, puis **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="9af07-132">Choose **Next**, then **Finish**.</span></span> <span data-ttu-id="9af07-133">Répétez cette étape pour créer deux autres icônes.</span><span class="sxs-lookup"><span data-stu-id="9af07-133">Repeat this step to create two more icons.</span></span>

- <span data-ttu-id="9af07-134">Nom: `ic_menu_calendar`, icône:`event`</span><span class="sxs-lookup"><span data-stu-id="9af07-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
- <span data-ttu-id="9af07-135">Nom: `ic_menu_signout`, icône:`exit to app`</span><span class="sxs-lookup"><span data-stu-id="9af07-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
- <span data-ttu-id="9af07-136">Nom: `ic_menu_signin`, icône:`person add`</span><span class="sxs-lookup"><span data-stu-id="9af07-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

<span data-ttu-id="9af07-137">Ensuite, créez un menu pour l'application.</span><span class="sxs-lookup"><span data-stu-id="9af07-137">Next, create a menu for the application.</span></span> <span data-ttu-id="9af07-138">Cliquez avec le bouton droit sur le dossier **res** , sélectionnez **nouveau**, puis **Répertoire de ressources Android**.</span><span class="sxs-lookup"><span data-stu-id="9af07-138">Right-click the **res** folder and choose **New**, then **Android Resource Directory**.</span></span> <span data-ttu-id="9af07-139">Remplacez le **type** de ressource `menu` par et cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-139">Change the **Resource type** to `menu` and choose **OK**.</span></span>

<span data-ttu-id="9af07-140">Cliquez avec le bouton droit sur le nouveau dossier de **menu** , sélectionnez **nouveau**, puis **fichier de ressources de menu**.</span><span class="sxs-lookup"><span data-stu-id="9af07-140">Right-click the new **menu** folder and choose **New**, then **Menu resource file**.</span></span> <span data-ttu-id="9af07-141">Nommez le `drawer_menu` fichier, puis cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-141">Name the file `drawer_menu` and choose **OK**.</span></span> <span data-ttu-id="9af07-142">Lorsque le fichier s'ouvre, sélectionnez l'onglet **texte** pour afficher le code XML, puis remplacez l'intégralité du contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-142">When the file opens, choose the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    tools:showIn="navigation_view">

    <group android:checkableBehavior="single">
        <item
            android:id="@+id/nav_home"
            android:icon="@drawable/ic_menu_home"
            android:title="Home" />

        <item
            android:id="@+id/nav_calendar"
            android:icon="@drawable/ic_menu_calendar"
            android:title="Calendar" />
    </group>

    <item android:title="Account">
        <menu>
            <item
                android:id="@+id/nav_signin"
                android:icon="@drawable/ic_menu_signin"
                android:title="Sign In" />

            <item
                android:id="@+id/nav_signout"
                android:icon="@drawable/ic_menu_signout"
                android:title="Sign Out" />
        </menu>
    </item>

</menu>
```

<span data-ttu-id="9af07-143">Maintenant, mettez à jour le thème de l'application pour qu'il soit compatible avec un tiroir de navigation.</span><span class="sxs-lookup"><span data-stu-id="9af07-143">Now update the application's theme to be compatible with a navigation drawer.</span></span> <span data-ttu-id="9af07-144">Ouvrez le fichier **app/res/values/styles. xml** .</span><span class="sxs-lookup"><span data-stu-id="9af07-144">Open the **app/res/values/styles.xml** file.</span></span> <span data-ttu-id="9af07-145">Remplacez `Theme.AppCompat.Light.DarkActionBar` par `Theme.AppCompat.Light.NoActionBar`.</span><span class="sxs-lookup"><span data-stu-id="9af07-145">Replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span> <span data-ttu-id="9af07-146">Ajoutez ensuite les lignes suivantes à l' `style` intérieur de l'élément.</span><span class="sxs-lookup"><span data-stu-id="9af07-146">Then add the following lines inside the `style` element.</span></span>

```xml
<item name="windowActionBar">false</item>
<item name="windowNoTitle">true</item>
<item name="android:statusBarColor">@android:color/transparent</item>
```

<span data-ttu-id="9af07-147">Ensuite, créez un en-tête pour le menu.</span><span class="sxs-lookup"><span data-stu-id="9af07-147">Next, create a header for the menu.</span></span> <span data-ttu-id="9af07-148">Cliquez avec le bouton droit sur le dossier **app/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="9af07-148">Right-click the **app/res/layout** folder.</span></span> <span data-ttu-id="9af07-149">Sélectionnez **nouveau**, puis **fichier de ressources de mise en page**.</span><span class="sxs-lookup"><span data-stu-id="9af07-149">Choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="9af07-150">Nommez le `nav_header` fichier et remplacez l' **élément racine** par `LinearLayout`.</span><span class="sxs-lookup"><span data-stu-id="9af07-150">Name the file `nav_header` and change the **Root element** to `LinearLayout`.</span></span> <span data-ttu-id="9af07-151">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-151">Choose **OK**.</span></span>

<span data-ttu-id="9af07-152">Ouvrez le fichier **nav_header. xml** et sélectionnez l'onglet **texte** . Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-152">Open the **nav_header.xml** file and choose the **Text** tab. Replace the entire contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="176dp"
    android:background="@color/colorPrimary"
    android:gravity="bottom"
    android:orientation="vertical"
    android:padding="16dp"
    android:theme="@style/ThemeOverlay.AppCompat.Dark">

    <ImageView
        android:id="@+id/user_profile_pic"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher" />

    <TextView
        android:id="@+id/user_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingTop="8dp"
        android:text="Test User"
        android:textAppearance="@style/TextAppearance.AppCompat.Body1" />

    <TextView
        android:id="@+id/user_email"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="test@contoso.com" />

</LinearLayout>
```

<span data-ttu-id="9af07-153">À présent, ouvrez le fichier **app/res/layout/activity_main. xml** .</span><span class="sxs-lookup"><span data-stu-id="9af07-153">Now, open the **app/res/layout/activity_main.xml** file.</span></span> <span data-ttu-id="9af07-154">Mettez à jour la mise `DrawerLayout` en page en remplaçant le code XML existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-154">Update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context=".MainActivity"
    tools:openDrawer="start">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <ProgressBar
            android:id="@+id/progressbar"
            android:layout_width="75dp"
            android:layout_height="75dp"
            android:layout_centerInParent="true"
            android:visibility="gone"/>

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="@color/colorPrimary"
            android:elevation="4dp"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" />

        <FrameLayout
            android:id="@+id/fragment_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_below="@+id/toolbar" />
    </RelativeLayout>

    <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/nav_header"
        app:menu="@menu/drawer_menu" />

</android.support.v4.widget.DrawerLayout>
```

<span data-ttu-id="9af07-155">Ensuite, ouvrez **app/res/values/Strings. xml**.</span><span class="sxs-lookup"><span data-stu-id="9af07-155">Next, open **app/res/values/strings.xml**.</span></span> <span data-ttu-id="9af07-156">Ajoutez les éléments suivants à l' `resources` intérieur de l'élément.</span><span class="sxs-lookup"><span data-stu-id="9af07-156">Add the following elements inside the `resources` element.</span></span>

```xml
<string name="navigation_drawer_open">Open navigation drawer</string>
<string name="navigation_drawer_close">Close navigation drawer</string>
```

<span data-ttu-id="9af07-157">Enfin, ouvrez le fichier **app/Java/com. example/graphtutorial/MainActivity** .</span><span class="sxs-lookup"><span data-stu-id="9af07-157">Finally, open the **app/java/com.example/graphtutorial/MainActivity** file.</span></span> <span data-ttu-id="9af07-158">Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-158">Replace the entire contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.support.annotation.NonNull;
import android.support.design.widget.NavigationView;
import android.support.v4.view.GravityCompat;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.support.v7.widget.Toolbar;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity implements NavigationView.OnNavigationItemSelectedListener {
    private DrawerLayout mDrawer;
    private NavigationView mNavigationView;
    private View mHeaderView;
    private boolean mIsSignedIn = false;
    private String mUserName = null;
    private String mUserEmail = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Set the toolbar
        Toolbar toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        mDrawer = findViewById(R.id.drawer_layout);

        // Add the hamburger menu icon
        ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(this, mDrawer, toolbar,
                R.string.navigation_drawer_open, R.string.navigation_drawer_close);
        mDrawer.addDrawerListener(toggle);
        toggle.syncState();

        mNavigationView = findViewById(R.id.nav_view);

        // Set user name and email
        mHeaderView = mNavigationView.getHeaderView(0);
        setSignedInState(mIsSignedIn);

        // Listen for item select events on menu
        mNavigationView.setNavigationItemSelectedListener(this);
    }

    @Override
    public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
        // TEMPORARY
        return false;
    }

    @Override
    public void onBackPressed() {
        if (mDrawer.isDrawerOpen(GravityCompat.START)) {
            mDrawer.closeDrawer(GravityCompat.START);
        } else {
            super.onBackPressed();
        }
    }

    public void showProgressBar()
    {
        FrameLayout container = findViewById(R.id.fragment_container);
        ProgressBar progressBar = findViewById(R.id.progressbar);
        container.setVisibility(View.GONE);
        progressBar.setVisibility(View.VISIBLE);
    }

    public void hideProgressBar()
    {
        FrameLayout container = findViewById(R.id.fragment_container);
        ProgressBar progressBar = findViewById(R.id.progressbar);
        progressBar.setVisibility(View.GONE);
        container.setVisibility(View.VISIBLE);
    }

    // Update the menu and get the user's name and email
    private void setSignedInState(boolean isSignedIn) {
        mIsSignedIn = isSignedIn;

        Menu menu = mNavigationView.getMenu();

        // Hide/show the Sign in, Calendar, and Sign Out buttons
        menu.findItem(R.id.nav_signin).setVisible(!isSignedIn);
        menu.findItem(R.id.nav_calendar).setVisible(isSignedIn);
        menu.findItem(R.id.nav_signout).setVisible(isSignedIn);

        // Set the user name and email in the nav drawer
        TextView userName = mHeaderView.findViewById(R.id.user_name);
        TextView userEmail = mHeaderView.findViewById(R.id.user_email);

        if (isSignedIn) {
            // For testing
            mUserName = "Megan Bowen";
            mUserEmail = "meganb@contoso.com";

            userName.setText(mUserName);
            userEmail.setText(mUserEmail);
        } else {
            mUserName = null;
            mUserEmail = null;

            userName.setText("Please sign in");
            userEmail.setText("");
        }
    }
}
```

### <a name="add-fragments"></a><span data-ttu-id="9af07-159">Ajouter des fragments</span><span class="sxs-lookup"><span data-stu-id="9af07-159">Add fragments</span></span>

<span data-ttu-id="9af07-160">Cliquez avec le bouton droit sur le dossier **app/res/layout** , sélectionnez Nouveau, puis fichier **de ressources de** **mise en page**.</span><span class="sxs-lookup"><span data-stu-id="9af07-160">Right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="9af07-161">Nommez le `fragment_home` fichier et remplacez l' **élément racine** par `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="9af07-161">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="9af07-162">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-162">Choose **OK**.</span></span>

<span data-ttu-id="9af07-163">Ouvrez le fichier **fragment_home. xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-163">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:text="Welcome!"
            android:textSize="30sp" />

        <TextView
            android:id="@+id/home_page_username"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:paddingTop="8dp"
            android:text="Please sign in"
            android:textSize="20sp" />
    </LinearLayout>

</RelativeLayout>
```

<span data-ttu-id="9af07-164">Ensuite, cliquez avec le bouton droit sur le dossier **app/res/layout** , sélectionnez Nouveau, puis fichier **de ressources de** **mise en page**.</span><span class="sxs-lookup"><span data-stu-id="9af07-164">Next, right-click the **app/res/layout** folder and choose **New**, then **Layout resource file**.</span></span> <span data-ttu-id="9af07-165">Nommez le `fragment_calendar` fichier et remplacez l' **élément racine** par `RelativeLayout`.</span><span class="sxs-lookup"><span data-stu-id="9af07-165">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`.</span></span> <span data-ttu-id="9af07-166">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-166">Choose **OK**.</span></span>

<span data-ttu-id="9af07-167">Ouvrez le fichier **fragment_calendar. xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-167">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="Calendar"
        android:textSize="30sp" />

</RelativeLayout>
```

<span data-ttu-id="9af07-168">À présent, cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et choisissez **New**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="9af07-168">Now, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="9af07-169">Nommez la `HomeFragment` classe et définissez la superclasse `android.support.v4.app.Fragment`sur. \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9af07-169">Name the class `HomeFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="9af07-170">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-170">Choose **OK**.</span></span> <span data-ttu-id="9af07-171">Ouvrez le fichier **HomeFragment** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-171">Open the **HomeFragment** file and replace its contents with the following.</span></span>

```java
package com.example.graphtutorial;

import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.annotation.Nullable;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

public class HomeFragment extends Fragment {
    private static final String USER_NAME = "userName";

    private String mUserName;

    public HomeFragment() {

    }

    public static HomeFragment createInstance(String userName) {
        HomeFragment fragment = new HomeFragment();

        // Add the provided username to the fragment's arguments
        Bundle args = new Bundle();
        args.putString(USER_NAME, userName);
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mUserName = getArguments().getString(USER_NAME);
        }
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View homeView = inflater.inflate(R.layout.fragment_home, container, false);

        // If there is a username, replace the "Please sign in" with the username
        if (mUserName != null) {
            TextView userName = homeView.findViewById(R.id.home_page_username);
            userName.setText(mUserName);
        }

        return homeView;
    }
}
```

<span data-ttu-id="9af07-172">Ensuite, cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** , puis choisissez **New**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="9af07-172">Next, right-click the **app/java/com.example.graphtutorial** folder and choose **New**, then **Java Class**.</span></span> <span data-ttu-id="9af07-173">Nommez la `CalendarFragment` classe et définissez la superclasse `android.support.v4.app.Fragment`sur. \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9af07-173">Name the class `CalendarFragment` and set the **Superclass** to `android.support.v4.app.Fragment`.</span></span> <span data-ttu-id="9af07-174">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="9af07-174">Choose **OK**.</span></span>

<span data-ttu-id="9af07-175">Ouvrez le fichier **CalendarFragment** et ajoutez la fonction suivante à la `CalendarFragment` classe.</span><span class="sxs-lookup"><span data-stu-id="9af07-175">Open the **CalendarFragment** file and add the following function to the `CalendarFragment` class.</span></span>

```java
@Nullable
@Override
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
    return inflater.inflate(R.layout.fragment_calendar, container, false);
}
```

<span data-ttu-id="9af07-176">À présent que les fragments sont implémentés, `MainActivity` mettez à jour la `onNavigationItemSelected` classe pour gérer l'événement et utiliser les fragments.</span><span class="sxs-lookup"><span data-stu-id="9af07-176">Now that the fragments are implemented, update the `MainActivity` class to handle the `onNavigationItemSelected` event and use the fragments.</span></span> <span data-ttu-id="9af07-177">Tout d'abord, ajoutez les fonctions suivantes à la classe.</span><span class="sxs-lookup"><span data-stu-id="9af07-177">First, add the following functions to the class.</span></span>

```java
// Load the "Home" fragment
public void openHomeFragment(String userName) {
    HomeFragment fragment = HomeFragment.createInstance(userName);
    getSupportFragmentManager().beginTransaction()
            .replace(R.id.fragment_container, fragment)
            .commit();
    mNavigationView.setCheckedItem(R.id.nav_home);
}

// Load the "Calendar" fragment
private void openCalendarFragment() {
    getSupportFragmentManager().beginTransaction()
            .replace(R.id.fragment_container, new CalendarFragment())
            .commit();
    mNavigationView.setCheckedItem(R.id.nav_calendar);
}

private void signIn() {
    setSignedInState(true);
    openHomeFragment(mUserName);
}

private void signOut() {
    setSignedInState(false);
    openHomeFragment(mUserName);
}
```

<span data-ttu-id="9af07-178">Ensuite, remplacez la fonction `onNavigationItemSelected` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="9af07-178">Next, replace the existing `onNavigationItemSelected` function with the following.</span></span>

```java
@Override
public boolean onNavigationItemSelected(@NonNull MenuItem menuItem) {
    // Load the fragment that corresponds to the selected item
    switch (menuItem.getItemId()) {
        case R.id.nav_home:
            openHomeFragment(mUserName);
            break;
        case R.id.nav_calendar:
            openCalendarFragment();
            break;
        case R.id.nav_signin:
            signIn();
            break;
        case R.id.nav_signout:
            signOut();
            break;
    }

    mDrawer.closeDrawer(GravityCompat.START);

    return true;
}
```

<span data-ttu-id="9af07-179">Enfin, ajoutez le code suivant à la fin de `onCreate` la fonction pour charger le fragment d'accueil au démarrage de l'application.</span><span class="sxs-lookup"><span data-stu-id="9af07-179">Finally, add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

```java
// Load the home fragment by default on startup
if (savedInstanceState == null) {
    openHomeFragment(mUserName);
}
```

<span data-ttu-id="9af07-180">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="9af07-180">Save all of your changes.</span></span> <span data-ttu-id="9af07-181">Dans le menu **exécuter** , choisissez **exécuter «application»**.</span><span class="sxs-lookup"><span data-stu-id="9af07-181">On the **Run** menu, choose **Run 'app'**.</span></span> <span data-ttu-id="9af07-182">Le menu de l'application doit fonctionner pour naviguer entre les deux fragments et changer lorsque vous appuyez sur les boutons **se connecter** ou **se déconnecter** .</span><span class="sxs-lookup"><span data-stu-id="9af07-182">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Capture d'écran de l'application](./images/welcome-page.png)