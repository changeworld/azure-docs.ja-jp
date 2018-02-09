## <a name="rest"></a>REST API を使用して展開する 
 
[展開サービス REST API](https://github.com/projectkudu/kudu/wiki/REST-API) を使って、.zip ファイルを Azure 内のアプリに展開できます。 POST 要求を https://<アプリ名>.scm.azurewebsites.net/api/zipdeploy に送信するだけです。 POST 要求のメッセージの本文に .zip ファイルを含める必要があります。 アプリの展開資格情報は、HTTP 基本認証を使って要求で提供します。 詳しくは、[.zip のプッシュ展開](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)に関するページをご覧ください。 

次の例では、cURL ツールを使って、.zip ファイルを含む要求を送信します。 cURL は、Mac または Linux コンピューターのターミナルから、または Windows で Bash を使って、実行できます。 `<zip_file_path>` プレースホルダーは、プロジェクトの .zip ファイルの場所へのパスに置き換えます。 また、`<app_name>` は、アプリの一意名に置き換えます。

`<deployment_user>` プレースホルダーは、展開資格情報のユーザー名に置き換えます。 cURL によって要求されたら、パスワードを入力します。 アプリの展開資格情報を設定する方法については、「[ユーザー レベルの資格情報の設定とリセット](../articles/app-service/app-service-deployment-credentials.md#userscope)」をご覧ください。   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

この要求により、アップロードされた .zip ファイルからのプッシュ展開がトリガーされます。 次の cURL の例で示すように、https://<アプリ名>.scm.azurewebsites.net/api/deployments エンドポイントを使うことにより、現在および過去の展開を確認できます。 やはり、`<app_name>` はアプリの名前に置き換え、`<deployment_user>` は展開資格情報のユーザー名に置き換えます。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```