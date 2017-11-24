## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

このコマンドの実行には、少し時間がかかる場合があります。