[az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) コマンドを使って、API アプリにローカルの Git デプロイを構成します。   

App Service では、FTP、ローカル Git、GitHub、Visual Studio Team Services、Bitbucket など、Web アプリにコンテンツをデプロイするいくつかの方法がサポートされています。 このクイック スタートでは、ローカル Git を使用してデプロイします。 つまり、Git コマンドを使用してローカル リポジトリから Azure 内のリポジトリにプッシュすることでデプロイします。  

以下のスクリプトを使って、コードをプッシュするときに使用するアカウント レベルのデプロイ資格情報を設定し、ユーザー名とパスワードの固有値が必ず含まれるようにします。   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
