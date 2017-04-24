## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトが実行された後で、次のコマンドを使用して、リソース グループ、Azure Redis Cache インスタンス、およびリソース グループ内のすべての関連リソースを削除できます。

```azurecli
az group delete --name contosoGroup
```