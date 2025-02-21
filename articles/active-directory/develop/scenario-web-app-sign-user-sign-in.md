---
title: Application web qui connecte des utilisateurs (connexion) - Plateforme d’identités Microsoft
description: Apprenez à générer une application web qui connecte des utilisateurs (connexion).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5c45005d6a54765458b463acb12c21a1f3b6d0c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336761"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Application web qui connecte des utilisateurs - connexion et déconnexion

Découvrez comment ajouter la connexion au code de votre application web qui connecte des utilisateurs, puis comment permettre à ces derniers de se déconnecter.

## <a name="sign-in"></a>Connexion

La connexion est assurée par deux parties :

- Le bouton de connexion dans la page HTML
- L’action de connexion dans le code-behind du contrôleur

### <a name="sign-in-button"></a>Bouton de connexion

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET Core, le bouton de connexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche qu’en l’absence de compte authentifié (c’est-à-dire lorsque l’utilisateur ne s’est pas encore connecté ou quand il s’est déconnecté).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET MVC, le bouton Déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche que quand il y a un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans notre guide de démarrage rapide Java, le bouton de déconnexion se trouve dans le fichier [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html).

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans le guide de démarrage rapide Python, il n’y a aucun bouton de connexion. L’utilisateur est automatiquement invité à se connecter par le code-behind quand il atteint la racine de l’application web. Consultez [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>Action `Login` du contrôleur

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET, l’activation du bouton de **connexion** de l’application web déclenche l’action `SignIn` sur le contrôleur `AccountController`. Dans les versions précédentes des modèles ASP.NET Core, le contrôleur `Account` était intégré avec l’application web, mais ce n’est plus le cas, car il fait désormais partie du framework ASP.NET Core.

Le code pour `AccountController` est disponible dans le référentiel ASP.NET Core à partir de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Le contrôle de compte demande un effort à l’utilisateur en le redirigeant vers le point de terminaison de la plateforme d’identités Microsoft. Pour plus d’informations, consultez la méthode [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) fournie dans le cadre d’ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, la déconnexion est déclenchée par la méthode `SignOut()` sur un contrôleur (par exemple, [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Cette méthode ne fait pas partie de l’infrastructure ASP.NET (contrairement à ASP.NET Core). Elle effectue les actions suivantes :

- Envoi d’une demande de connexion OpenId après avoir proposé un URI de redirection

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans Java, la déconnexion est gérée en appelant directement le point de terminaison de déconnexion de la plateforme d’identités Microsoft et en fournissant la valeur post_logout_redirect_uri. Pour plus d’informations, consultez [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Contrairement à d’autres plateformes, MSAL.Python prend soin de permettre à l’utilisateur de se connecter à partir de la page de connexion. Consultez [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

Avec la méthode _build_msal_app() définie dans [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) comme suit :

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Une fois que l’utilisateur est connecté à votre application, vous devez leur donner la possibilité de se déconnecter.

## <a name="sign-out"></a>Se déconnecter

La déconnexion d’une application web ne consiste pas seulement à supprimer les informations sur le compte connecté de l’état de l’application web.
L’application web doit également rediriger l’utilisateur vers le point de terminaison `logout` de la plateforme d’identités Microsoft pour suivre la procédure de déconnexion. Lorsque votre application web redirige l’utilisateur vers le point de terminaison `logout`, ce dernier efface la session de l’utilisateur dans le navigateur. Si l’application n’accède pas au point de terminaison `logout`, l’utilisateur peut se réauthentifier auprès de votre application sans saisir de nouveau ses informations d’identification, puisqu’il dispose d’une session de connexion unique valide avec le point de terminaison de la plateforme d’identités Microsoft.

Pour en savoir plus, consultez la section [Envoi d’une demande de déconnexion](v2-protocols-oidc.md#send-a-sign-out-request) dans la documentation conceptuelle de la [plateforme d’identités Microsoft et du protocole OpenID Connect](v2-protocols-oidc.md).

### <a name="application-registration"></a>Inscription de l’application

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Lors de l’inscription de l’application, vous devez avoir inscrit un **URI post-déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44321/signout-oidc` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**. Pour plus d’informations, consultez la [procédure d’inscription de l’application web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Lors de l’inscription de l’application, vous devez avoir inscrit un **URI post-déconnexion**. Dans notre didacticiel, vous avez inscrit `https://localhost:44308/Account/EndSession` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**. Pour plus d’informations, voir [Inscrire l’application web](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="javatabjava"></a>[Java](#tab/java)

Lors de l’inscription de l’application, vous inscrivez un **URI Post-déconnexion**. Dans notre didacticiel, vous avez inscrit `http://localhost:8080/msal4jsample/` dans le champ **URL de déconnexion** de la section **Paramètres avancés** sur la page **Authentification**.

# <a name="pythontabpython"></a>[Python](#tab/python)

Au cours de l’inscription de l’application, vous n’avez pas besoin d’inscrire d’URL de déconnexion supplémentaire. L’application est rappelée sur son URL principale.

---

### <a name="sign-out-button"></a>Bouton Déconnexion

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET Core, le bouton Déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche que quand il y a un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET MVC, le bouton Déconnexion est exposé dans `Views\Shared\_LoginPartial.cshtml` et ne s’affiche que quand il y a un compte authentifié (c’est-à-dire lorsque l’utilisateur s’est précédemment connecté).

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans notre guide de démarrage rapide Java, le bouton Déconnexion se trouve dans le fichier main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans le guide de démarrage rapide Python, le bouton de déconnexion se trouve dans le fichier [templates/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10).

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>Action `Signout` du contrôleur

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dans ASP.NET, le bouton **Déconnexion** de l’application web déclenche l’action `SignOut` sur le contrôleur `AccountController`. Dans les versions précédentes des modèles ASP.NET Core, le contrôleur `Account` était intégré avec l’application web, mais ce n’est plus le cas, car il fait désormais partie du framework ASP.NET Core.

Le code pour `AccountController` est disponible dans le référentiel ASP.NET Core à partir de [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Le contrôle de compte a plusieurs fonctions :

- Définir un URI de redirection OpenID sur `/Account/SignedOut` afin que le contrôleur soit rappelé quand Azure AD a effectué la déconnexion
- Appeler `Signout()`, ce qui permet à l’intergiciel OpenIdConnect de contacter le point de terminaison `logout` de la plateforme d’identités Microsoft qui :

  - efface le cookie de session du navigateur, et
  - appelle finalement l’**URL de déconnexion** qui, par défaut, affiche la page de vue de déconnexion [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) également fournie dans le cadre d’ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, la déconnexion est déclenchée par la méthode `SignOut()` sur un contrôleur (par exemple, [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Cette méthode ne fait pas partie de l’infrastructure ASP.NET (contrairement à ASP.NET Core). Elle effectue les actions suivantes :

- Elle envoie une demande de déconnexion OpenId.
- Elle efface le cache.
- Elle effectue une redirection vers la page souhaitée.

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans Java, la déconnexion est gérée en appelant directement le point de terminaison de déconnexion de la plateforme d’identités Microsoft et en fournissant la valeur post_logout_redirect_uri. Pour plus d’informations, consultez [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Le code qui déconnecte l’utilisateur se trouve dans [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out user and its token cache from session
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interception de l’appel à au point de terminaison `logout`

L’URI Post-déconnexion permet aux applications de participer à la déconnexion globale.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

L’intergiciel OpenIdConnect ASP.NET Core permet à votre application d’intercepter l’appel au point de terminaison `logout` de la plateforme d’identités Microsoft en fournissant un événement OpenIdConnect nommé `OnRedirectToIdentityProviderForSignOut`. Pour obtenir un exemple illustrant comment s’abonner à cet événement (pour effacer le cache de jeton), voir [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dans ASP.NET, vous déléguez à l’intergiciel (middleware) l’exécution de la déconnexion, en effaçant le cookie de session :

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans notre guide de démarrage rapide Java, l’URI de redirection post-déconnexion affiche uniquement la page index.html. 

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans notre guide de démarrage rapide Python, l’URI de redirection post-déconnexion affiche uniquement la page index.html.

---

## <a name="protocol"></a>Protocol

Pour en savoir plus sur la déconnexion, voir la documentation du protocole, disponible dans [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-sign-user-production.md)
