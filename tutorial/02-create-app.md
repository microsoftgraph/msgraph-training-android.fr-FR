<!-- markdownlint-disable MD002 MD041 -->

Commencez par créer un projet Android Studio.

1. Ouvrez Android Studio, puis **sélectionnez Démarrer un nouveau** projet Android Studio sur l’écran d’accueil.

1. Dans la **boîte de dialogue Créer un** projet, sélectionnez Activité **vide,** puis **sélectionnez Suivant.**

    ![Capture d’écran de la boîte de dialogue Créer un projet dans Android Studio](./images/choose-project.png)

1. Dans la **boîte de** dialogue  Configurer votre projet, définissez le nom sur , assurez-vous que le champ Langue est configuré sur , et assurez-vous que le niveau `Graph Tutorial`  `Java` **d’API** minimum est configuré sur `API 29: Android 10.0 (Q)` . Modifiez le nom **du package et** **l’emplacement d’enregistrer** selon vos besoins. Sélectionnez **Terminer**.

    ![Capture d’écran de la boîte de dialogue Configurer votre projet](./images/configure-project.png)

> [!IMPORTANT]
> Le code et les instructions de ce didacticiel utilisent le nom du package **com.example.graphtutorial**. Si vous utilisez un autre nom de package lors de la création du projet, n’oubliez pas d’utiliser votre nom de package partout où vous voyez cette valeur.

## <a name="install-dependencies"></a>Installer les dépendances

Avant de passer à autre chose, installez des dépendances supplémentaires que vous utiliserez ultérieurement.

- `com.google.android.material:material` pour rendre [l’affichage de navigation](https://material.io/develop/android/components/navigation-view/) disponible pour l’application.
- [Bibliothèque d’authentification Microsoft (MSAL) pour Android pour gérer](https://github.com/AzureAD/microsoft-authentication-library-for-android) l’authentification Azure AD et la gestion des jetons.
- [SDK Microsoft Graph pour Java](https://github.com/microsoftgraph/msgraph-sdk-java) pour effectuer des appels à Microsoft Graph.

1. Développez **Gradle Scripts,** puis **ouvrez build.gradle (Module : Graph_Tutorial.app).**

1. Ajoutez les lignes suivantes à l’intérieur de `dependencies` la valeur.

    :::code language="gradle" source="../demo/GraphTutorial/app/build.gradle" id="DependenciesSnippet":::

1. Ajoutez `packagingOptions` une valeur à l’intérieur de la valeur dans `android` **build.gradle (Module : Graph_Tutorial.app).**

    ```Gradle
    packagingOptions {
        pickFirst 'META-INF/*'
    }
    ```

1. Ajoutez le référentiel Azure Maven pour la bibliothèque MicrosoftDeviceSDK, une dépendance de MSAL. Ouvrez **build.gradle (Project: Graph_Tutorial)**. Ajoutez ce qui suit à `repositories` la valeur à l’intérieur de la `allprojects` valeur.

    ```Gradle
    maven {
        url 'https://pkgs.dev.azure.com/MicrosoftDeviceSDK/DuoSDK-Public/_packaging/Duo-SDK-Feed/maven/v1'
    }
    ```

1. Enregistrez vos modifications. Dans le menu **Fichier,** **sélectionnez Synchroniser le projet avec des fichiers Gradle.**

## <a name="design-the-app"></a>Concevoir l’application

L’application utilise un bac de navigation pour naviguer entre les différents affichages. Dans cette étape, vous allez mettre à jour l’activité pour utiliser une disposition de caisse de navigation et ajouter des fragments pour les vues.

### <a name="create-a-navigation-drawer"></a>Créer un caisse de navigation

Dans cette section, vous allez créer des icônes pour le menu de navigation de l’application, créer un menu pour l’application et mettre à jour le thème et la disposition de l’application pour qu’ils soient compatibles avec un bac de navigation.

#### <a name="create-icons"></a>Créer des icônes

1. Cliquez avec le bouton droit **sur le dossier app/res/drawable** et **sélectionnez Nouveau,** puis **Vector Asset**.

1. Cliquez sur le bouton d’icône en haut du **ClipArt.**

1. Dans la **fenêtre Sélectionner une** icône, tapez dans la barre de recherche, puis sélectionnez l’icône Accueil et `home` **sélectionnez OK.** 

1. Changez **le nom** en `ic_menu_home` .

    ![Capture d’écran de la fenêtre Configurer les ressources vectorielles](./images/create-icon.png)

1. Sélectionnez **Suivant,** puis **Terminer.**

1. Répétez l’étape précédente pour créer quatre autres icônes.

    - Name: `ic_menu_calendar` , Icon: `event`
    - Name: `ic_menu_add_event` , Icon: `add box`
    - Name: `ic_menu_signout` , Icon: `exit to app`
    - Name: `ic_menu_signin` , Icon: `person add`

#### <a name="create-the-menu"></a>Créer le menu

1. Cliquez avec le bouton droit **sur le dossier res** et **sélectionnez Nouveau,** puis **Répertoire de ressources Android.**

1. Modifiez **le type de ressource** et `menu` sélectionnez **OK.**

1. Cliquez avec le bouton droit sur le nouveau **dossier de menu** et **sélectionnez Nouveau,** puis **fichier de ressources menu.**

1. Nommez le fichier `drawer_menu` et sélectionnez **OK.**

1. Lorsque le fichier s’ouvre, sélectionnez l’onglet **Code** pour afficher le code XML, puis remplacez tout le contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/menu/drawer_menu.xml":::

#### <a name="update-application-theme-and-layout"></a>Mettre à jour le thème et la disposition de l’application

1. Ouvrez **le fichier app/res/values/themes.xml** et ajoutez les lignes suivantes à l’intérieur de l’élément. `style`

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. Ouvrez **le fichier app/res/values-night/themes.xml** et ajoutez les lignes suivantes à l’intérieur de l’élément. `style`

    ```xml
    <item name="windowActionBar">false</item>
    <item name="windowNoTitle">true</item>
    ```

1. Cliquez avec le bouton droit **sur le dossier application/res/layout.**

1. Sélectionnez **Nouveau,** puis **Fichier de ressources de disposition.**

1. Nommez le fichier `nav_header` et modifiez **l’élément racine** `LinearLayout` en , puis sélectionnez **OK**.

1. Ouvrez le **nav_header.xml** et sélectionnez **l’onglet Code.** Remplacez tout le contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/nav_header.xml":::

1. Ouvrez **le fichier app/res/layout/activity_main.xml** et mettez à jour la disposition en A en remplaçant le XML existant `DrawerLayout` par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/activity_main.xml":::

1. Ouvrez **app/res/values/strings.xml** et ajoutez les éléments suivants à l’intérieur de l’élément. `resources`

    ```xml
    <string name="navigation_drawer_open">Open navigation drawer</string>
    <string name="navigation_drawer_close">Close navigation drawer</string>
    ```

1. Ouvrez **le fichier app/java/com.example/graphtutorial/MainActivity** et remplacez tout le contenu par ce qui suit.

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
        private static final String SAVED_IS_SIGNED_IN = "isSignedIn";
        private static final String SAVED_USER_NAME = "userName";
        private static final String SAVED_USER_EMAIL = "userEmail";
        private static final String SAVED_USER_TIMEZONE = "userTimeZone";

        private DrawerLayout mDrawer;
        private NavigationView mNavigationView;
        private View mHeaderView;
        private boolean mIsSignedIn = false;
        private String mUserName = null;
        private String mUserEmail = null;
        private String mUserTimeZone = null;

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

            if (savedInstanceState == null) {
                // Load the home fragment by default on startup
                openHomeFragment(mUserName);
            } else {
                // Restore state
                mIsSignedIn = savedInstanceState.getBoolean(SAVED_IS_SIGNED_IN);
                mUserName = savedInstanceState.getString(SAVED_USER_NAME);
                mUserEmail = savedInstanceState.getString(SAVED_USER_EMAIL);
                mUserTimeZone = savedInstanceState.getString(SAVED_USER_TIMEZONE);
                setSignedInState(mIsSignedIn);
            }
        }

        @Override
        protected void onSaveInstanceState(@NonNull Bundle outState) {
            super.onSaveInstanceState(outState);
            outState.putBoolean(SAVED_IS_SIGNED_IN, mIsSignedIn);
            outState.putString(SAVED_USER_NAME, mUserName);
            outState.putString(SAVED_USER_EMAIL, mUserEmail);
            outState.putString(SAVED_USER_TIMEZONE, mUserTimeZone);
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

            mNavigationView.getMenu().clear();
            mNavigationView.inflateMenu(R.menu.drawer_menu);

            Menu menu = mNavigationView.getMenu();

            // Hide/show the Sign in, Calendar, and Sign Out buttons
            if (isSignedIn) {
                menu.removeItem(R.id.nav_signin);
            } else {
                menu.removeItem(R.id.nav_home);
                menu.removeItem(R.id.nav_calendar);
                menu.removeItem(R.id.nav_create_event);
                menu.removeItem(R.id.nav_signout);
            }

            // Set the user name and email in the nav drawer
            TextView userName = mHeaderView.findViewById(R.id.user_name);
            TextView userEmail = mHeaderView.findViewById(R.id.user_email);

            if (isSignedIn) {
                // For testing
                mUserName = "Lynne Robbins";
                mUserEmail = "lynner@contoso.com";
                mUserTimeZone = "Pacific Standard Time";

                userName.setText(mUserName);
                userEmail.setText(mUserEmail);
            } else {
                mUserName = null;
                mUserEmail = null;
                mUserTimeZone = null;

                userName.setText("Please sign in");
                userEmail.setText("");
            }
        }
    }
    ```

### <a name="add-fragments"></a>Ajouter des fragments

Dans cette section, vous allez créer des fragments pour les affichages d’accueil et de calendrier.

1. Cliquez avec le bouton droit sur le **dossier application/res/layout** et sélectionnez **Nouveau,** puis fichier **de ressources de disposition.**

1. Nommez le fichier `fragment_home` et modifiez **l’élément racine** `RelativeLayout` en , puis sélectionnez **OK**.

1. Ouvrez **fragment_home.xml** fichier et remplacez son contenu par ce qui suit.

    :::code language="xml" source="../demo/GraphTutorial/app/src/main/res/layout/fragment_home.xml":::

1. Cliquez avec le bouton droit sur le **dossier application/res/layout** et sélectionnez **Nouveau,** puis fichier **de ressources de disposition.**

1. Nommez le fichier `fragment_calendar` et modifiez **l’élément racine** `RelativeLayout` en , puis sélectionnez **OK**.

1. Ouvrez **fragment_calendar.xml** fichier et remplacez son contenu par ce qui suit.

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

1. Cliquez avec le bouton droit sur le **dossier application/res/layout** et sélectionnez **Nouveau,** puis fichier **de ressources de disposition.**

1. Nommez le fichier `fragment_new_event` et modifiez **l’élément racine** `RelativeLayout` en , puis sélectionnez **OK**.

1. Ouvrez **fragment_new_event.xml** fichier et remplacez son contenu par ce qui suit.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="New Event"
            android:textSize="30sp" />

    </RelativeLayout>
    ```

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**

1. Nommez la `HomeFragment` classe, puis sélectionnez **OK.**

1. Ouvrez **le fichier HomeFragment** et remplacez son contenu par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/HomeFragment.java" id="HomeSnippet":::

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**

1. Nommez la `CalendarFragment` classe, puis sélectionnez **OK.**

1. Ouvrez **le fichier CalendarFragment** et remplacez son contenu par ce qui suit.

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
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public CalendarFragment() {}

        public static CalendarFragment createInstance(String timeZone) {
            CalendarFragment fragment = new CalendarFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_calendar, container, false);
        }
    }
    ```

1. Cliquez avec le bouton droit sur le dossier **app/java/com.example.graphtutorial** et sélectionnez **Nouveau,** **puis Java classe .**

1. Nommez la `NewEventFragment` classe, puis sélectionnez **OK.**

1. Ouvrez **le fichier NewEventFragment** et remplacez son contenu par ce qui suit.

    ```java
    package com.example.graphtutorial;

    import android.os.Bundle;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.Fragment;

    public class NewEventFragment extends Fragment {
        private static final String TIME_ZONE = "timeZone";

        private String mTimeZone;

        public NewEventFragment() {}

        public static NewEventFragment createInstance(String timeZone) {
            NewEventFragment fragment = new NewEventFragment();

            // Add the provided time zone to the fragment's arguments
            Bundle args = new Bundle();
            args.putString(TIME_ZONE, timeZone);
            fragment.setArguments(args);
            return fragment;
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (getArguments() != null) {
                mTimeZone = getArguments().getString(TIME_ZONE);
            }
        }

        @Nullable
        @Override
        public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
            return inflater.inflate(R.layout.fragment_new_event, container, false);
        }
    }
    ```

1. Ouvrez **le fichier MainActivity.java** et ajoutez les fonctions suivantes à la classe.

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
    private void openCalendarFragment(String timeZone) {
        CalendarFragment fragment = CalendarFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_calendar);
    }

    // Load the "New Event" fragment
    private void openNewEventFragment(String timeZone) {
        NewEventFragment fragment = NewEventFragment.createInstance(timeZone);
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.fragment_container, fragment)
                .commit();
        mNavigationView.setCheckedItem(R.id.nav_create_event);
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

1. Remplacez la fonction `onNavigationItemSelected` existante par ce qui suit.

    :::code language="java" source="../demo/GraphTutorial/app/src/main/java/com/example/graphtutorial/MainActivity.java" id="OnNavItemSelectedSnippet":::

1. Enregistrez toutes vos modifications.

1. Dans **le** menu Exécuter, **sélectionnez Exécuter « application**».

Le menu de l’application doit fonctionner pour naviguer entre  les deux fragments et changer lorsque vous appuyez sur les boutons Se connectez ou **se connectez.**

![Capture d’écran de l’application](./images/app-screens.png)
