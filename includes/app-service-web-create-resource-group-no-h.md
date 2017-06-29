[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。

[!INCLUDE [resource group intro text](resource-group.md)]

次の例では、*myResourceGroup* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 Azure Web Apps がサポートされているすべての場所を表示するには、`az appservice list-locations` コマンドを実行します。 