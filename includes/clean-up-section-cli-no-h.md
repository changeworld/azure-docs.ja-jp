前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると思わない場合は、リソース グループを削除してリソースを削除できます。
 
1. 次のコマンドを実行して、保存する必要があるリソースがリソース グループに含まれていないことを確認します。

  ```azurecli
  az group show --name myResourceGroup
  ```

2. 表示されたリソースをすべて削除する場合は、次のコマンドを実行します。
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
