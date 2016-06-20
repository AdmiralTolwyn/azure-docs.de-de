<properties 
	pageTitle="Erste Schritte mit Azure Active Directory und verbundenen Visual Studio-Diensten (MVC-Projekte) | Microsoft Azure" 
	description="Erfahren Sie etwas über die ersten Schritte mit Azure Active Directory in MVC-Projekten nach dem Herstellen einer Verbindung oder dem Erstellen eines Azure AD mithilfe von verbundenen Visual Studio-Diensten." 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="tarcher"/>

# Erste Schritte mit Azure Active Directory und verbundenen Visual Studio-Diensten (MVC-Projekte)

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-active-directory-dotnet-getting-started.md)
> - [Was ist passiert?](vs-active-directory-dotnet-what-happened.md)
 
##Erfordern von Authentifizierung für den Zugriff auf Controller 

Alle Controller in Ihrem Projekt wurden mit dem Attribut **Authorize** versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf diese Controller authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller. Wenn Sie die Berechtigungen präziser festlegen möchten, wenden Sie das Attribut auf jede Methode an, die Autorisierung erfordert, anstatt es auf die Controllerklasse anzuwenden.
 
##Hinzufügen von SignIn-/SignOut-Steuerelementen 

Wenn Sie Ihrer Ansicht SignIn-/SignOut-Steuerelemente hinzufügen möchten, können Sie die Teilansicht \_LoginPartial.cshtml zum Hinzufügen der Funktion zu einer Ihrer Ansichten verwenden. Das folgende Beispiel zeigt die Funktion, die der Standardansicht \_Layout.cshtml hinzugefügt wurde. (Beachten Sie das letzte Element in div mit der Klasse navbar-collapse):

<pre>
    &lt;!DOCTYPE html> 
     &lt;html> 
     &lt;head> 
         &lt;meta charset="utf-8" /> 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        &lt;title>@ViewBag.Title - My ASP.NET Application&lt;/title> 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head> 
    &lt;body> 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"> 
            &lt;div class="container"> 
                &lt;div class="navbar-header"> 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                    &lt;/button> 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div> 
                &lt;div class="navbar-collapse collapse"> 
                    &lt;ul class="nav navbar-nav"> 
                        &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li> 
                    &lt;/ul> 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div> 
            &lt;/div> 
        &lt;/div> 
        &lt;div class="container body-content"> 
            @RenderBody() 
            &lt;hr /> 
            &lt;footer> 
                &lt;p>&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p> 
            &lt;/footer> 
        &lt;/div> 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body> 
    &lt;/html>
</pre>

[Weitere Informationen zu Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0608_2016-->