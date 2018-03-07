---
title: "Erhöhen der Zugriffsrechte für Mandantenadministratoren – Azure AD | Microsoft-Dokumentation"
description: Dieses Thema beschreibt die integrierten Rollen der rollenbasierten Zugriffssteuerung (RBAC).
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: rolyon
ms.openlocfilehash: dff3a26201507f974d52de3fe6dcb23945cd900f
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2018
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Erhöhen der Rechte als Mandantenadministrator mit der rollenbasierten Zugriffssteuerung

Mit der rollenbasierten Zugriffssteuerung können Mandantenadministratoren vorübergehend erhöhte Zugriffsrechte erhalten, damit sie höhere Berechtigungen als üblich erteilen können. Mandantenadministratoren können ihre Rechte bei Bedarf auf die Rolle des Benutzerzugriffsadministrators erhöhen. Diese Rolle bietet den Mandantenadministratoren die Berechtigung, sich selbst oder anderen Benutzern Rollen im Bereich „/“ zu gewähren.

Dieses Feature ist wichtig, weil es dem Mandantenadministrator die Anzeige aller Abonnements ermöglicht, die in einer Organisation vorhanden sind. Zudem erlaubt es Automatisierungs-Apps – z.B. zur Fakturierung und Überwachung – den Zugriff auf alle Abonnements und die Bereitstellung einer exakten Übersicht über den Organisationsstatus im Hinblick auf Abrechnung oder Asset Management.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Verwenden von elevateAccess für Zugriff von Mandanten mit Azure AD Admin Center

1. Wechseln Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com), und melden Sie sich mit Ihren Anmeldeinformationen an.

2. Wählen Sie **Eigenschaften** aus dem linken Azure AD-Menü.

3. Navigieren Sie zu **Ein globaler Administrator kann Azure-Abonnements verwalten.**, und wählen Sie **Ja** und dann **Speichern**.
    > [!IMPORTANT] 
    > Wenn Sie **Ja** auswählen, wird die Rolle **Benutzerzugriffsadministrator** im Stamm „/“ (Stammbereich) dem Benutzernamen zugewiesen, mit dem Sie derzeit im Portal angemeldet sind. **Dies ermöglicht dem Benutzer, alle anderen Azure-Abonnements anzuzeigen.**
    
    > [!NOTE] 
    > Wenn Sie **Nein** auswählen, wird die Rolle **Benutzerzugriffsadministrator** im Stamm „/“ (Stammbereich) für den Benutzernamen entfernt, mit dem Sie derzeit im Portal angemeldet sind.

> [!TIP] 
> Dies scheint auf den ersten Blick eine globale Eigenschaft für Azure Active Directory zu sein, sie funktioniert jedoch auf Benutzerbasis für den derzeit angemeldeten Benutzer. Wenn Sie in Azure Active Directory über globale Administratorrechte verfügen, können Sie das elevateAccess-Feature für den Benutzernamen aufrufen, mit dem Sie derzeit bei Azure Active Directory Admin Center angemeldet sind.

![Azure AD Admin Center – Eigenschaften – Ein globaler Administrator kann Azure-Abonnements verwalten – Screenshot](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Anzeigen von Rollenzuweisungen im Bereich „/“ mithilfe von PowerShell
Wenn Sie die Zuweisung **Benutzerzugriffsadministrator** im Bereich **/** anzeigen möchten, verwenden Sie das PowerShell-Cmdlet `Get-AzureRmRoleAssignment`.
    
```powershell
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Beispielausgabe:**
```
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    
```

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Löschen der Rollenzuweisung im Bereich „/“ mithilfe von Powershell:
Sie können die Zuweisung mithilfe des folgenden PowerShell-Cmdlets löschen:

```powershell
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Verwenden von elevateAccess zum Erteilen von Zugriff an Mandanten mit der REST-API

Im grundlegenden Vorgang werden die folgenden Schritte verwendet:

1. Rufen Sie mithilfe von REST *elevateAccess* auf. So erhalten Sie die Rolle des Benutzerzugriffsadministrators im Bereich „/“.

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Erstellen Sie eine [Rollenzuweisung](/rest/api/authorization/roleassignments), um eine beliebige Rolle in einem beliebigen Bereich zuzuweisen. Das folgende Beispiel zeigt die Eigenschaften für die Zuweisung der Leserrolle im Bereich „/“:

    ```json
    { 
      "properties": {
        "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
        "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
        "scope": "/"
      },
      "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Solange Sie Benutzerzugriffsadministrator sind, können Sie auch Rollenzuweisungen im Bereich „/“ löschen.

4. Widerrufen Sie Ihre Berechtigungen als Benutzerzugriffsadministrator, bis sie wieder benötigt werden.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Rückgängigmachen der elevateAccess-Aktion mit der REST-API

Beim Aufruf von *elevateAccess* erstellen Sie eine Rollenzuweisung für sich selbst. Um diese Berechtigungen zu widerrufen, müssen Sie die Zuweisung löschen.

1.  Rufen Sie „GET roleDefinitions“ mit „roleName = User Access-Administrator“ auf, um die Namens-GUID der Rolle des Benutzerzugriffsadministrators abzurufen.
    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Speichern Sie die GUID aus dem Parameter *name*, in diesem Fall **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. Sie müssen auch die Rollenzuweisung für den Mandantenadministrator im Mandantenbereich auflisten. Listen Sie alle Zuweisungen im Mandantenbereich für die PrincipalId des TenantAdmin auf, der den Aufruf mit erhöhten Zugriffsrechten vorgenommen hat. Dadurch werden alle Zuweisungen im Mandanten für die ObjectID aufgelistet.

    ```
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Ein Mandantenadministrator sollte nicht über zu viele Zuweisungen verfügen. Wenn die obige Abfrage zu viele Zuweisungen zurückgibt, können Sie auch Abfragen für alle Zuweisungen auf der Mandantenbereichsebene durchführen und dann die Ergebnisse filtern: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
    
        
    2. Mit den oben aufgeführten Aufrufe wird eine Liste von Rollenzuweisungen zurückgegeben. Suchen Sie die Rollenzuweisung, für die der Bereich „/“ ist und die RoleDefinitionId mit der Rollennamen-GUID endet, die Sie in Schritt 1 ermittelt haben, und PrincipalId der ObjectId des Mandantenadministrators entspricht. 
    
    Beispielrollenzuweisung:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Speichern Sie die GUID aus dem Parameter *name* erneut, in diesem Fall **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Verwenden Sie abschließend die hervorgehobene **RoleAssignment-ID** zum Löschen der von Elevate Access hinzugefügten Zuweisung:

    ```
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Verwalten der rollenbasierten Zugriffssteuerung mit REST](role-based-access-control-manage-access-rest.md).

- [Verwalten von Zugriffszuweisungen](role-based-access-control-manage-assignments.md) im Azure-Portal
