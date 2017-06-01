Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。 次のコマンドで、`<app_name>` プレースホルダーを一意のアプリ名に置き換えます。 `<app_name>` は、Web アプリの既定の DNS サイトで使用されます。 `<app_name>` が一意でない場合は、"指定された名前 <app_name> の Web サイトは既に存在します" というわかりやすいエラー メッセージが表示されます。

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

サイト (`http://<app_name>.azurewebsites.net`) を参照して、新しく作成された Web アプリを表示します。

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>ローカル Git デプロイの構成

App Service では、FTP、ローカル Git、GitHub、Visual Studio Team Services、Bitbucket など、Web アプリにコンテンツをデプロイするいくつかの方法がサポートされています。 

このクイック スタートでは、ローカル Git を使用してデプロイします。 つまり、Git コマンドを使用してローカル リポジトリから Azure 内のリポジトリにプッシュすることでデプロイします。 

[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) コマンドを使用して、Web アプリへのローカル Git アクセスを構成します。

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

出力の形式は次のとおりです。

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

表示された URI を保存します。 これは、次の手順で使用します。 `<username>` は、前の手順で作成した[デプロイ ユーザー](#configure-a-deployment-user)です。

## <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <URI from previous step>
```

アプリをデプロイするために、Azure リモートにプッシュします。 前にデプロイ ユーザーを作成したときに作成したパスワードを入力するよう求められます。 Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```azurecli
git push azure master
```

上記のコマンドにより、次の例のような情報が表示されます。

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>アプリの参照


デプロイされたアプリを参照します。

```
http://<app_name>.azurewebsites.net
```

