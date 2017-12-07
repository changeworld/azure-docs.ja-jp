## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az_group_create) でリソース グループを作成します。 Azure リソース グループとは、Function App、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、`myResourceGroup` という名前のリソース グループを作成します。  
Cloud Shell を使用していない場合は、まず `az login` でサインインします。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 App Service プランがサポートされているすべての場所を表示するには、[az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) コマンドを実行します。