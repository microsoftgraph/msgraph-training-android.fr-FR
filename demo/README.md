# <a name="how-to-run-the-completed-project"></a>Comment exécuter le projet terminé

## <a name="prerequisites"></a>Conditions préalables

Pour exécuter le projet terminé dans ce dossier, vous devez :

- [Android Studio installé](https://developer.android.com/studio/) sur votre ordinateur de développement. (**Remarque : ce** didacticiel a été écrit avec Android Studio version 4.1.3 et le SDK Android 10.0. Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.)
- Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte scolaire ou scolaire Microsoft.

Si vous n’avez pas de compte Microsoft, deux options s’offrent à vous pour obtenir un compte gratuit :

- Vous pouvez [vous inscrire à un nouveau compte Microsoft personnel.](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)
- Vous pouvez vous inscrire au programme pour les développeurs [Office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement Office 365 gratuit.

## <a name="register-an-application-with-the-azure-portal"></a>Inscrire une application avec le portail Azure

1. Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.

    ![Une capture d’écran des inscriptions d’applications ](../tutorial/images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `Android Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Sous **URI** de redirection, définissez la liste de listes sur **Client public/natif (bureau mobile &)** et définissez la valeur sur , en remplaçant par le nom du package de `msauth://YOUR_PACKAGE_NAME/callback` votre `YOUR_PACKAGE_NAME` projet.

    ![Capture d’écran de la page Inscrire une application](../tutorial/images/aad-register-an-app.png)

1. Sélectionner **Inscription**. Dans la page **didacticiel Android Graph,** copiez la valeur de l’ID **d’application (client)** et enregistrez-la. Vous en aurez besoin à l’étape suivante.

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](../tutorial/images/aad-application-id.png)

## <a name="configure-the-sample"></a>Configurer l’exemple

1. Renommez `msal_config.json.example` le fichier et `msal_config.json` déplacez-le dans `GraphTutorial/app/src/main/res/raw` le répertoire.
1. Modifiez `msal_config.json` le fichier et a apporter les modifications suivantes.
    1. Remplacez `YOUR_APP_ID_HERE` par **l’ID d’application** que vous avez obtenu à partir du portail Azure.
    1. Remplacez `com.example.graphtutorial` par le nom de votre package.

## <a name="run-the-sample"></a>Exécution de l’exemple

Dans Android Studio, **sélectionnez Exécuter « application** » **dans** le menu Exécuter.
