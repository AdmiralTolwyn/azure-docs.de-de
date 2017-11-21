Erstellen Sie Anmeldeinformationen für die Bereitstellung mit dem Befehl [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) in der Cloud Shell. Bei der FTP- und der lokalen Git-Bereitstellung für eine Web-App ist ein Bereitstellungsbenutzer erforderlich. Der Benutzername und das Kennwort gelten auf der Kontoebene. _Sie unterscheiden sich von den Anmeldeinformationen Ihres Azure-Abonnements._

Ersetzen Sie im folgenden Beispiel *\<username>* und *\<password>* (einschließlich Klammern) durch einen neuen Benutzernamen und ein neues Kennwort. Der Benutzername muss eindeutig sein. Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen, Symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Wenn Sie den Fehler ` 'Conflict'. Details: 409` erhalten, müssen Sie den Benutzernamen ändern. Wenn Sie den Fehler ` 'Bad Request'. Details: 400` erhalten, müssen Sie ein sichereres Kennwort verwenden.

Sie müssen diesen Bereitstellungsbenutzer nur einmal erstellen und können ihn dann für alle Azure-Bereitstellungen verwenden.

> [!NOTE]
> Notieren Sie sich den Benutzernamen und das Kennwort. Sie benötigen sie später für die Bereitstellung der Web-App.
>
>