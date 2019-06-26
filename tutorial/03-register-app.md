<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4c7eb-101">Dans cet exercice, vous allez créer une application native Azure AD à l’aide du centre d’administration Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="4c7eb-102">Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory ](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="4c7eb-103">Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="4c7eb-104">Capture d’écran des inscriptions d’application</span><span class="sxs-lookup"><span data-stu-id="4c7eb-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="4c7eb-105">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-105">Select **New registration**.</span></span> <span data-ttu-id="4c7eb-106">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="4c7eb-107">Définissez le **Nom** sur `Android Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-107">Set **Name** to `Android Graph Tutorial`.</span></span>
    - <span data-ttu-id="4c7eb-108">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="4c7eb-109">Laissez **Redirect URI** vide.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-109">Leave **Redirect URI** empty.</span></span>

    ![Capture d’écran de la page inscrire une application](./images/aad-register-an-app.png)

1. <span data-ttu-id="4c7eb-111">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-111">Select **Register**.</span></span> <span data-ttu-id="4c7eb-112">Sur la page **didacticiel Graph Android** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-112">On the **Android Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Capture d’écran de l’ID d’application de la nouvelle inscription de l’application](./images/aad-application-id.png)

1. <span data-ttu-id="4c7eb-114">Sélectionnez le lien **Ajouter un URI de redirection** .</span><span class="sxs-lookup"><span data-stu-id="4c7eb-114">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="4c7eb-115">Sur la page **URI** de redirection, recherchez la section **URI de redirection suggérée pour les clients publics (mobile, bureau)** .</span><span class="sxs-lookup"><span data-stu-id="4c7eb-115">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="4c7eb-116">Sélectionnez l’URI qui commence par `msal` et copiez-le, puis sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-116">Select the URI that begins with `msal` and copy it, then select **Save**.</span></span> <span data-ttu-id="4c7eb-117">Enregistrez l’URI de redirection copié, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="4c7eb-117">Save the copied redirect URI, you will need it in the next step.</span></span>

    ![Capture d’écran de la page des URI de redirection](./images/aad-redirect-uris.png)
