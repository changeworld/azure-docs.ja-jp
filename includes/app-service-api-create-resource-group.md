持つリソース グループを作成、 [az グループ作成](/cli/azure/group#create)コマンド。

[!INCLUDE [resource group intro text](resource-group.md)]

次の例は、という名前のリソース グループを作成*myResourceGroup*で、 *westeurope*場所。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

使用可能な場所を表示するには、実行、`az appservice list-locations`コマンド。 一般に、リソースを作成するには、近くにある地域にします。
