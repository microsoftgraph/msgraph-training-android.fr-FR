# <a name="how-to-run-the-completed-project"></a>Exécution du projet terminé

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants :

- [Android Studio](https://developer.android.com/studio/) installé sur votre ordinateur de développement. (**Remarque :** ce didacticiel a été écrit avec Android Studio version 3.3.1 avec le 1.8.0 JRE et le kit de développement logiciel (SDK) Android 9,0. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.)
- Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.

Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :

- Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.

## <a name="register-an-application-with-the-azure-portal"></a>Enregistrer une application avec le portail Azure

1. Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory ](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications** sous **gérer**.

    ![Capture d’écran des inscriptions d’application ](../../tutorial/images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `Android Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Laissez **Redirect URI** vide.

    ![Capture d’écran de la page inscrire une application](../../tutorial/images/aad-register-an-app.png)

1. Sélectionnez **Enregistrer**. Sur la page **Xamarin Graph Tutorial** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.

    ![Capture d’écran de l’ID d’application de la nouvelle inscription de l’application](../../tutorial/images/aad-application-id.png)

1. Sélectionnez le lien **Ajouter un URI de redirection** . Sur la page **URI de redirection** , recherchez la section **URI de redirection suggérée pour les clients publics (mobile, bureau)** . Sélectionnez l’URI qui commence par `msal` et copiez-le, puis sélectionnez **Enregistrer**. Enregistrez l’URI de redirection copié, vous en aurez besoin à l’étape suivante.

    ![Capture d’écran de la page des URI de redirection](../../tutorial/images/aad-redirect-uris.png)

## <a name="configure-the-sample"></a>Configurer l’exemple

1. Renommez `oauth_strings.xml.example` le fichier `oauth_strings.xml` et déplacez-le dans le `GraphTutorial/app/src/main/res/values` répertoire.
1. Modifiez le `oauth_strings.xml` fichier et effectuez les modifications suivantes.
    1. Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail Azure.

## <a name="run-the-sample"></a>Exécution de l’exemple

Dans Android Studio, sélectionnez **exécuter « application »** dans le menu **exécuter** .
