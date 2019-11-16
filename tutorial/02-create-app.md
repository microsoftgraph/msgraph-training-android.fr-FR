<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="74f51-101">Commencez par créer un nouveau projet Android Studio.</span><span class="sxs-lookup"><span data-stu-id="74f51-101">Begin by creating a new Android Studio project.</span></span>

1. <span data-ttu-id="74f51-102">Ouvrez Android Studio, puis sélectionnez **Démarrer un nouveau projet Android Studio** dans l’écran d’accueil.</span><span class="sxs-lookup"><span data-stu-id="74f51-102">Open Android Studio, and select **Start a new Android Studio project** on the welcome screen.</span></span>

1. <span data-ttu-id="74f51-103">Dans la boîte de dialogue **créer un nouveau projet** , sélectionnez **activité vide**, puis **suivant**.</span><span class="sxs-lookup"><span data-stu-id="74f51-103">In the **Create New Project** dialog, select **Empty Activity**, then select **Next**.</span></span>

    ![Capture d’écran de la boîte de dialogue créer un nouveau projet dans Android Studio](./images/choose-project.png)

1. <span data-ttu-id="74f51-105">Dans la boîte de dialogue **configurer votre projet** , \*\*\*\* définissez le `Graph Tutorial`nom sur, vérifiez que le champ **langue** `Java`est défini sur et assurez-vous que le `API 29: Android 10.0 (Q)`niveau d' **API minimal** est défini sur.</span><span class="sxs-lookup"><span data-stu-id="74f51-105">In the **Configure your project** dialog, set the **Name** to `Graph Tutorial`, ensure the **Language** field is set to `Java`, and ensure the **Minimum API level** is set to `API 29: Android 10.0 (Q)`.</span></span> <span data-ttu-id="74f51-106">Modifiez le **nom du package** et **Enregistrez** -le si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="74f51-106">Modify the **Package name** and **Save location** as needed.</span></span> <span data-ttu-id="74f51-107">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="74f51-107">Select **Finish**.</span></span>

    ![Capture d’écran de la boîte de dialogue Configurer votre projet](./images/configure-project.png)

> [!IMPORTANT]
> <span data-ttu-id="74f51-109">Le code et les instructions de ce didacticiel utilisent le nom de package **com. example. graphtutorial**.</span><span class="sxs-lookup"><span data-stu-id="74f51-109">The code and instructions in this tutorial use the package name **com.example.graphtutorial**.</span></span> <span data-ttu-id="74f51-110">Si vous utilisez un autre nom de package lors de la création du projet, veillez à utiliser le nom de votre package où cette valeur est affichée.</span><span class="sxs-lookup"><span data-stu-id="74f51-110">If you use a different package name when creating the project, be sure to use your package name wherever you see this value.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="74f51-111">Installer les dépendances</span><span class="sxs-lookup"><span data-stu-id="74f51-111">Install dependencies</span></span>

<span data-ttu-id="74f51-112">Avant de poursuivre, installez des dépendances supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="74f51-112">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="74f51-113">`com.google.android.material:material`pour mettre le [mode navigation](https://material.io/develop/android/components/navigation-view/) à la disposition de l’application.</span><span class="sxs-lookup"><span data-stu-id="74f51-113">`com.google.android.material:material` to make the [navigation view](https://material.io/develop/android/components/navigation-view/) available to the app.</span></span>
- <span data-ttu-id="74f51-114">[Bibliothèque d’authentification Microsoft (MSAL) pour Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) afin de gérer l’authentification Azure ad et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="74f51-114">[Microsoft Authentication Library (MSAL) for Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="74f51-115">[Kit de développement logiciel (SDK) Microsoft Graph pour Java](https://github.com/microsoftgraph/msgraph-sdk-java) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="74f51-115">[Microsoft Graph SDK for Java](https://github.com/microsoftgraph/msgraph-sdk-java) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="74f51-116">Développez **scripts Gradle**, puis ouvrez le fichier **Build. Gradle (module : App)** .</span><span class="sxs-lookup"><span data-stu-id="74f51-116">Expand **Gradle Scripts**, then open the **build.gradle (Module: app)** file.</span></span>

1. <span data-ttu-id="74f51-117">Ajoutez les lignes suivantes à l' `dependencies` intérieur de la valeur.</span><span class="sxs-lookup"><span data-stu-id="74f51-117">Add the following lines inside the `dependencies` value.</span></span>

    ```Gradle
    implementation 'com.google.android.material:material:1.0.0'
    implementation 'com.microsoft.identity.client:msal:1.0.0'
    implementation 'com.microsoft.graph:microsoft-graph:1.6.0'
    ```

1. <span data-ttu-id="74f51-118">Ajoutez une `packagingOptions` valeur à l' `android` intérieur de la valeur dans le fichier **Build. gradle (module : App)** .</span><span class="sxs-lookup"><span data-stu-id="74f51-118">Add a `packagingOptions` value inside the `android` value in the **build.gradle (Module: app)** file.</span></span>

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/jersey-module-version'
    }
    ```

1. <span data-ttu-id="74f51-119">Enregistrez vos modifications.</span><span class="sxs-lookup"><span data-stu-id="74f51-119">Save your changes.</span></span> <span data-ttu-id="74f51-120">Dans le menu **fichier** , sélectionnez **synchroniser le projet avec les fichiers Gradle**.</span><span class="sxs-lookup"><span data-stu-id="74f51-120">On the **File** menu, select **Sync Project with Gradle Files**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="74f51-121">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="74f51-121">Design the app</span></span>

<span data-ttu-id="74f51-122">L’application utilise un tiroir de navigation pour naviguer entre les différentes vues.</span><span class="sxs-lookup"><span data-stu-id="74f51-122">The application will use a navigation drawer to navigate between different views.</span></span> <span data-ttu-id="74f51-123">Dans cette étape, vous allez mettre à jour l’activité pour utiliser une disposition de tiroir de navigation et ajouter des fragments pour les vues.</span><span class="sxs-lookup"><span data-stu-id="74f51-123">In this step you will update the activity to use a navigation drawer layout, and add fragments for the views.</span></span>

### <a name="create-a-navigation-drawer"></a><span data-ttu-id="74f51-124">Créer un tiroir de navigation</span><span class="sxs-lookup"><span data-stu-id="74f51-124">Create a navigation drawer</span></span>

<span data-ttu-id="74f51-125">Dans cette section, vous allez créer des icônes pour le menu de navigation de l’application, créer un menu pour l’application et mettre à jour le thème et la disposition de l’application pour qu’elle soit compatible avec un tiroir de navigation.</span><span class="sxs-lookup"><span data-stu-id="74f51-125">In this section you will create icons for the app's navigation menu, create a menu for the application, and update the application's theme and layout to be compatible with a navigation drawer.</span></span>

#### <a name="create-icons"></a><span data-ttu-id="74f51-126">Créer des icônes</span><span class="sxs-lookup"><span data-stu-id="74f51-126">Create icons</span></span>

1. <span data-ttu-id="74f51-127">Cliquez avec le bouton droit sur le dossier **app/res/Drawable** et sélectionnez **nouveau**, puis **élément vectoriel**.</span><span class="sxs-lookup"><span data-stu-id="74f51-127">Right-click the **app/res/drawable** folder and select **New**, then **Vector Asset**.</span></span>

1. <span data-ttu-id="74f51-128">Cliquez sur le bouton de l’icône en regard de image **clipart**.</span><span class="sxs-lookup"><span data-stu-id="74f51-128">Click the icon button next to **Clip Art**.</span></span>

1. <span data-ttu-id="74f51-129">Dans la **fenêtre Sélectionner une icône** , `home` saisissez dans la barre de recherche, puis sélectionnez l’icône **Accueil** et cliquez sur **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-129">In the **Select Icon** window, type `home` in the search bar, then select the **Home** icon and select **OK**.</span></span>

1. <span data-ttu-id="74f51-130">Remplacez le **nom** par `ic_menu_home`.</span><span class="sxs-lookup"><span data-stu-id="74f51-130">Change the **Name** to `ic_menu_home`.</span></span>

    ![Capture d’écran de la fenêtre Configurer une ressource vectorielle](./images/create-icon.png)

1. <span data-ttu-id="74f51-132">Sélectionnez **suivant**, puis **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="74f51-132">Select **Next**, then **Finish**.</span></span>

1. <span data-ttu-id="74f51-133">Répétez l’étape précédente pour créer trois icônes supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="74f51-133">Repeat the previous step to create three more icons.</span></span>

    - <span data-ttu-id="74f51-134">Nom : `ic_menu_calendar`, icône :`event`</span><span class="sxs-lookup"><span data-stu-id="74f51-134">Name: `ic_menu_calendar`, Icon: `event`</span></span>
    - <span data-ttu-id="74f51-135">Nom : `ic_menu_signout`, icône :`exit to app`</span><span class="sxs-lookup"><span data-stu-id="74f51-135">Name: `ic_menu_signout`, Icon: `exit to app`</span></span>
    - <span data-ttu-id="74f51-136">Nom : `ic_menu_signin`, icône :`person add`</span><span class="sxs-lookup"><span data-stu-id="74f51-136">Name: `ic_menu_signin`, Icon: `person add`</span></span>

#### <a name="create-the-menu"></a><span data-ttu-id="74f51-137">Créer le menu</span><span class="sxs-lookup"><span data-stu-id="74f51-137">Create the menu</span></span>

1. <span data-ttu-id="74f51-138">Cliquez avec le bouton droit sur le dossier **res** , sélectionnez **nouveau**, puis **Répertoire de ressources Android**.</span><span class="sxs-lookup"><span data-stu-id="74f51-138">Right-click the **res** folder and select **New**, then **Android Resource Directory**.</span></span>

1. <span data-ttu-id="74f51-139">Modifiez le **type** de `menu` ressource et sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-139">Change the **Resource type** to `menu` and select **OK**.</span></span>

1. <span data-ttu-id="74f51-140">Cliquez avec le bouton droit sur le nouveau dossier de **menu** , sélectionnez **nouveau**, puis **fichier de ressources de menu**.</span><span class="sxs-lookup"><span data-stu-id="74f51-140">Right-click the new **menu** folder and select **New**, then **Menu resource file**.</span></span>

1. <span data-ttu-id="74f51-141">Nommez le `drawer_menu` fichier, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-141">Name the file `drawer_menu` and select **OK**.</span></span>

1. <span data-ttu-id="74f51-142">Lorsque le fichier s’ouvre, sélectionnez l’onglet **texte** pour afficher le code XML, puis remplacez l’intégralité du contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-142">When the file opens, select the **Text** tab to view the XML, then replace the entire contents with the following.</span></span>

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

#### <a name="update-application-theme-and-layout"></a><span data-ttu-id="74f51-143">Mettre à jour le thème et la disposition de l’application</span><span class="sxs-lookup"><span data-stu-id="74f51-143">Update application theme and layout</span></span>

1. <span data-ttu-id="74f51-144">Ouvrez le fichier **app/res/values/styles. xml** et `Theme.AppCompat.Light.DarkActionBar` remplacez `Theme.AppCompat.Light.NoActionBar`par.</span><span class="sxs-lookup"><span data-stu-id="74f51-144">Open the **app/res/values/styles.xml** file and replace `Theme.AppCompat.Light.DarkActionBar` with `Theme.AppCompat.Light.NoActionBar`.</span></span>

1. <span data-ttu-id="74f51-145">Ajoutez les lignes suivantes à l' `style` intérieur de l’élément.</span><span class="sxs-lookup"><span data-stu-id="74f51-145">Add the following lines inside the `style` element.</span></span>

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>
    ```

1. <span data-ttu-id="74f51-146">Cliquez avec le bouton droit sur le dossier **app/res/layout** .</span><span class="sxs-lookup"><span data-stu-id="74f51-146">Right-click the **app/res/layout** folder.</span></span>

1. <span data-ttu-id="74f51-147">Sélectionnez **nouveau**, puis **fichier de ressources de mise en page**.</span><span class="sxs-lookup"><span data-stu-id="74f51-147">Select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="74f51-148">Nommez le `nav_header` fichier et remplacez l' **élément racine** par `LinearLayout`, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-148">Name the file `nav_header` and change the **Root element** to `LinearLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="74f51-149">Ouvrez le fichier **nav_header. xml** et sélectionnez l’onglet **texte** . Remplacez tout le contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-149">Open the **nav_header.xml** file and select the **Text** tab. Replace the entire contents with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
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

1. <span data-ttu-id="74f51-150">Ouvrez le fichier **app/res/layout/activity_main. xml** et mettez à jour la mise `DrawerLayout` en page en remplaçant le code XML existant par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-150">Open the **app/res/layout/activity_main.xml** file and update the layout to a `DrawerLayout` by replacing the existing XML with the following.</span></span>

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
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

            <androidx.appcompat.widget.Toolbar
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

        <com.google.android.material.navigation.NavigationView
            android:id="@+id/nav_view"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_header"
            app:menu="@menu/drawer_menu" />

    </androidx.drawerlayout.widget.DrawerLayout>
    ```

1. <span data-ttu-id="74f51-151">Ouvrez **app/res/values/Strings. xml** et ajoutez les éléments suivants `resources` à l’intérieur de l’élément.</span><span class="sxs-lookup"><span data-stu-id="74f51-151">Open **app/res/values/strings.xml** and add the following elements inside the `resources` element.</span></span>

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. <span data-ttu-id="74f51-152">Ouvrez le fichier **app/Java/com. example/graphtutorial/MainActivity** et remplacez l’intégralité du contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-152">Open the **app/java/com.example/graphtutorial/MainActivity** file and replace the entire contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.FrameLayout;
    import android.widget.ProgressBar;
    import android.widget.TextView;
    import androidx.annotation.NonNull;
    import androidx.appcompat.app.ActionBarDrawerToggle;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.appcompat.widget.Toolbar;
    import androidx.core.view.GravityCompat;
    import androidx.drawerlayout.widget.DrawerLayout;
    import com.google.android.material.navigation.NavigationView;

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

### <a name="add-fragments"></a><span data-ttu-id="74f51-153">Ajouter des fragments</span><span class="sxs-lookup"><span data-stu-id="74f51-153">Add fragments</span></span>

<span data-ttu-id="74f51-154">Dans cette section, vous allez créer des fragments pour les affichages Accueil et calendrier.</span><span class="sxs-lookup"><span data-stu-id="74f51-154">In this section you will create fragments for the home and calendar views.</span></span>

1. <span data-ttu-id="74f51-155">Cliquez avec le bouton droit sur le dossier **app/res/layout** , puis sélectionnez **nouveau**, puis **fichier de ressources de mise en page**.</span><span class="sxs-lookup"><span data-stu-id="74f51-155">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="74f51-156">Nommez le `fragment_home` fichier et remplacez l' **élément racine** par `RelativeLayout`, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-156">Name the file `fragment_home` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="74f51-157">Ouvrez le fichier **fragment_home. xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-157">Open the **fragment_home.xml** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="74f51-158">Cliquez avec le bouton droit sur le dossier **app/res/layout** , puis sélectionnez **nouveau**, puis **fichier de ressources de mise en page**.</span><span class="sxs-lookup"><span data-stu-id="74f51-158">Right-click the **app/res/layout** folder and select **New**, then **Layout resource file**.</span></span>

1. <span data-ttu-id="74f51-159">Nommez le `fragment_calendar` fichier et remplacez l' **élément racine** par `RelativeLayout`, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-159">Name the file `fragment_calendar` and change the **Root element** to `RelativeLayout`, then select **OK**.</span></span>

1. <span data-ttu-id="74f51-160">Ouvrez le fichier **fragment_calendar. xml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-160">Open the **fragment_calendar.xml** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="74f51-161">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et sélectionnez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="74f51-161">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="74f51-162">Nommez la `HomeFragment` classe et définissez la **superclasse** sur `androidx.fragment.app.Fragment`, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-162">Name the class `HomeFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="74f51-163">Ouvrez le fichier **HomeFragment** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-163">Open the **HomeFragment** file and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="74f51-164">Cliquez avec le bouton droit sur le dossier **app/Java/com. example. graphtutorial** et sélectionnez **nouveau**, puis **classe Java**.</span><span class="sxs-lookup"><span data-stu-id="74f51-164">Right-click the **app/java/com.example.graphtutorial** folder and select **New**, then **Java Class**.</span></span>

1. <span data-ttu-id="74f51-165">Nommez la `CalendarFragment` classe et définissez la **superclasse** sur `androidx.fragment.app.Fragment`, puis sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="74f51-165">Name the class `CalendarFragment` and set the **Superclass** to `androidx.fragment.app.Fragment`, then select **OK**.</span></span>

1. <span data-ttu-id="74f51-166">Ouvrez le fichier **CalendarFragment** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-166">Open the **CalendarFragment** file and replace its contents with the following.</span></span>

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class CalendarFragment extends Fragment {

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. <span data-ttu-id="74f51-167">Ouvrez le fichier **MainActivity. Java** et ajoutez les fonctions suivantes à la classe.</span><span class="sxs-lookup"><span data-stu-id="74f51-167">Open the **MainActivity.java** file and add the the following functions to the class.</span></span>

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

1. <span data-ttu-id="74f51-168">Remplacez la fonction `onNavigationItemSelected` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="74f51-168">Replace the existing `onNavigationItemSelected` function with the following.</span></span>

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

1. <span data-ttu-id="74f51-169">Ajoutez le code suivant à la fin de `onCreate` la fonction pour charger le fragment de domicile au démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="74f51-169">Add the following at the end of the `onCreate` function to load the home fragment when the app starts.</span></span>

    ```java
    // Load the home fragment by default on startup
    if (savedInstanceState == null) {
        openHomeFragment(mUserName);
    }
    ```

1. <span data-ttu-id="74f51-170">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="74f51-170">Save all of your changes.</span></span>

1. <span data-ttu-id="74f51-171">Dans le menu **exécuter** , sélectionnez **exécuter « application »**.</span><span class="sxs-lookup"><span data-stu-id="74f51-171">On the **Run** menu, select **Run 'app'**.</span></span>

<span data-ttu-id="74f51-172">Le menu de l’application doit fonctionner pour naviguer entre les deux fragments et changer lorsque vous appuyez sur les boutons **se connecter** ou **se déconnecter** .</span><span class="sxs-lookup"><span data-stu-id="74f51-172">The app's menu should work to navigate between the two fragments and change when you tap the **Sign in** or **Sign out** buttons.</span></span>

![Capture d’écran de l’application](./images/app-screens.png)
