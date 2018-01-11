## <a name="deploy-uploaded-zip-file"></a>アップロードされた ZIP ファイルをデプロイする

Cloud Shell で、[az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) コマンドを使用して、アップロードされた ZIP ファイルを Web アプリにデプロイします。 必ず *\<app_name>* をお使いの Web アプリの名前に置き換えてください。

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

このコマンドは、ファイルとディレクトリを ZIP ファイルから既定の App Service アプリケーション フォルダー (`\home\site\wwwroot`) にデプロイし、アプリを再起動します。 任意の追加のカスタム ビルド プロセスが構成されている場合、そのプロセスも実行されます。
