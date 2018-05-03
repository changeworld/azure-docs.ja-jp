[az group create](/cli/azure/group?view=azure-cli-latest#az_group_create) コマンドでリソース グループを作成します。

[!INCLUDE [resource group intro text](resource-group.md)]

次の例では、*myResourceGroup* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

利用可能な場所を表示するには、`az appservice list-locations` コマンドを実行します。 一般に、リソースは、近くのリージョンに作成します。
