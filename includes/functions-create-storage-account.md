## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Functions は、関数に関する状態その他の情報を維持するために Azure Storage の汎用のアカウントを使用します。 [az storage account create](/cli/azure/storage/account#create) コマンドを使用して作成したリソース グループ内に汎用のストレージ アカウントを作成します。

次のコマンドでは、`<storage_name>` プレースホルダーをグローバルに一意なストレージ アカウント名で置き換えます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

ストレージ アカウントの作成後に、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```