## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie Funktionen-Apps, Datenbanken und Speicherkonten bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens `myResourceGroup` erstellt.  
Falls Sie nicht Cloud Shell verwenden, melden Sie sich erst mithilfe von `az login` an.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer Region in Ihrer Nähe. Um alle unterstützten Standorte für App Service-Pläne anzuzeigen, führen Sie den Befehl [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) aus.