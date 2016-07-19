<properties
	pageTitle="Hinzufügen oder Ändern von Azure-Administratorrollen | Microsoft Azure"
	description="Informationen zum Hinzufügen oder Ändern des Co-Administrators, Dienstadministrators und Kontoadministrators in Azure"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="genli"/>

# Hinzufügen oder Ändern von Azure-Administratorrollen

In Microsoft Azure stehen drei verschiedene Administratorrollen zur Verfügung:

| Administratorrolle | Begrenzung | Beschreibung
| ------------- | ------------- |---------------|
|Kontoadministrator (AA) | 1 pro Azure-Konto |Dies ist die Person, die sich für Azure-Abonnements angemeldet oder diese erworben hat und die für den Zugriff auf das [Kontocenter](https://account.windowsazure.com/Home/Index) und zum Ausführen verschiedener Verwaltungsaufgaben berechtigt ist. Dazu gehört z. B. die Möglichkeit zum Erstellen von Abonnements, Kündigen von Abonnements, Ändern der Abrechnung für ein Abonnement und Ändern des Dienstadministrators.
| Dienstadministrator (SA) | 1 pro Azure-Abonnement |Diese Rolle ist zum Verwalten von Diensten im [Azure-Portal](https://portal.azure.com) berechtigt. Standardmäßig ist der Kontoadministrator für ein neues Abonnement gleichzeitig auch der Dienstadministrator.|
|Co-Administrator (CA) im [klassischen Azure-Portal](https://manage.windowsazure.com)|200 pro Abonnement| Diese Rolle verfügt über die gleichen Zugriffsrechte wie der Dienstadministrator, kann jedoch die Zuordnung von Abonnements zu Azure-Verzeichnissen nicht ändern. |

> [AZURE.NOTE] Die rollenbasierte Zugriffssteuerung in Azure Active Directory (RBAC, Role-based Access Control) ermöglicht es, Benutzer mehreren Rollen hinzuzufügen. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Active Directory](./active-directory/role-based-access-control-configure.md).

## Hinzufügen eines Administrators für ein Abonnement

**Azure-Portal**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Hub-Menü die Option **Abonnement** und anschließend *das Abonnement aus, auf das der Administrator zugreifen soll*.

	![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. Wählen Sie auf dem Blatt „Abonnement“ die Optionen **Einstellungen** > **Benutzer** aus.

	![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Wählen Sie auf dem Blatt „Benutzer“ die Optionen **Hinzufügen** > **Rolle auswählen** > **Besitzer** aus.

	![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

	**Hinweis**
	- Die Besitzerrolle verfügt über die gleichen Zugriffsrechte wie der Co-Administrator. Diese Rolle verfügt über kein Zugriffsrecht für das [Azure-Kontocenter](https://account.windowsazure.com/subscriptions).
	- Die Besitzer, die Sie über das [Azure-Portal](https://portal.azure.com) hinzugefügt haben, können im [klassischen Azure-Portal](https://manage.windowsazure.com) keine Dienste verwalten.

5. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie als Besitzer hinzufügen möchten, klicken Sie auf den Benutzer, und klicken Sie dann auf **Auswählen**.

	![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Klassisches Azure-Portal**

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.

2. Wählen Sie im Navigationsbereich die Option **Einstellungen** > **Administratoren** > **Hinzufügen** aus. </br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. Geben Sie die E-Mail-Adresse der Person ein, die Sie als Co-Administrator hinzufügen möchten, und wählen Sie dann das Abonnement aus, auf das der Co-Administrator zugreifen soll.</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Die folgende E-Mail-Adresse kann als Co-Administrator hinzugefügt werden:

* **Microsoft-Konto** (früher Windows Live ID) </br> Sie können ein Microsoft-Konto verwenden, um sich bei allen verbraucherorientierten Microsoft-Produkten und Clouddiensten anzumelden, z. B. Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone und Xbox LIVE.
* **Organisationskonto**</br> Ein Organisationskonto ist ein Konto, das unter Azure Active Directory erstellt wird. Die Adresse eines Organisationskontos ähnelt der folgenden Adresse: Benutzer@&lt;Ihre\_Domäne&gt;.onmicrosoft.com

### Begrenzungen und Einschränkungen

 * Jedes Azure-Abonnement ist einem Azure AD-Verzeichnis zugeordnet (das auch als Standardverzeichnis bezeichnet wird). Um das Standardverzeichnis zu finden, dem das Abonnement zugeordnet ist, wählen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) die Option **Einstellungen** > **Abonnements** aus. Überprüfen Sie dort die Abonnement-ID, und suchen dann das Standardverzeichnis.

 * Wenn Sie mit einem Microsoft-Konto angemeldet sind, können Sie nur andere Microsoft-Konten oder Benutzer innerhalb des Standardverzeichnisses als Co-Administrator hinzufügen.

 * Wenn Sie mit einem Organisationskonto angemeldet sind, können Sie andere Organisationskonten in Ihrer Organisation als Co-Administrator hinzufügen. Beispielsweise kann abby@contoso.com das Konto bob@contoso.com als Dienstadministrator oder Co-Administrator hinzufügen, aber nicht john@notcontoso.com, es sei denn, john@noncontoso.com ist Benutzer im Standardverzeichnis. Mit einem Organisationskonto angemeldete Benutzer können Benutzer mit Microsoft-Konten weiterhin als Dienstadministrator oder Co-Administrator hinzufügen.

 * Nachdem es jetzt möglich ist, sich bei Azure mit einem Organisationskonto anzumelden, ändern sich die folgenden Kontoanforderungen für Dienstadministratoren und Co-Administratoren:

	Anmeldemethode| Microsoft-Konto oder Benutzer im Standardverzeichnis als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in der gleichen Organisation als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in einer anderen Organisation als Co-Administrator oder Dienstadministrator hinzufügen?
	------------- | ------------- |---------------|---------------
	Microsoft Account |Ja|Nein|Nein
	Organisationskonto|Ja|Ja|Nein

## Ändern des Dienstadministrators für ein Abonnement

Nur der Kontoadministrator kann den Dienstadministrator für ein Abonnement ändern.

1. Melden Sie sich mithilfe des Kontoadministrators beim [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) an.

2. Wählen Sie das Abonnement aus, das Sie ändern möchten.

3. Klicken Sie auf der rechten Seite auf **Abonnementdetails bearbeiten**. </br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Geben Sie im Feld **DIENSTADMINISTRATOR** die E-Mail-Adresse des neuen Dienstadministrators ein. </br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Ändern des Kontoadministrators

Informationen zum Übertragen des Besitzes des Azure-Kontos auf ein anderes Konto finden Sie unter [Übertragen eines Azure-Abonnements](billing-subscription-transfer.md).

## Nächste Schritte

* Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](./active-directory/active-directory-understanding-resource-access.md).

* Weitere Informationen zur Beziehung zwischen Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

* Weitere Informationen zu Azure Active Directory und Ihrem Azure-Abonnement finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](./active-directory/active-directory-assign-admin-roles.md).

<!---HONumber=AcomDC_0706_2016-->