# <a name="completed-module-add-azure-ad-authentication"></a>Module terminé : ajouter l’authentification Azure AD

La version du projet dans ce répertoire reflète l’exécution du didacticiel via l' [authentification Azure ad](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=3). Si vous utilisez cette version du projet, vous devez effectuer le reste du didacticiel en commençant par obtenir les [données du calendrier](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=4).

> **Remarque :** Nous partons du principe que vous avez déjà inscrit une application dans le portail Azure, comme indiqué dans [enregistrer l’application dans le portail](https://docs.microsoft.com/graph/tutorials/android?tutorial-step=2). Vous devez configurer cette version de l’exemple comme suit :
>
> 1. Renommez `./GraphTutorial/msal_config.json.example` le fichier `msal_config.json`.
> 1. Déplacez le `msal_config.json` fichier dans le `./GraphTutorial/app/src/main/res/raw` répertoire.
> 1. Modifiez le `msal_config.json` fichier et effectuez les modifications suivantes.
>     1. Remplacez `YOUR_APP_ID_HERE` par l' **ID d’application** que vous avez obtenu à partir du portail Azure.
