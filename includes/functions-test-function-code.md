## <a name="test"></a>関数をテストする

cURL を使用して、Mac または Linux コンピューターで、または Windows で Bash を使用して、デプロイした関数をテストします。 次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

コマンド ラインで使用可能な cURL がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを関数アプリの名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
