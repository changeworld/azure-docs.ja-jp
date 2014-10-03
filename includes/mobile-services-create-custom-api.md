1.  [Azure の管理ポータル][]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][]

2.  **[API]** タブをクリックし、**[カスタム API の作成]** をクリックします。

    ![][1]

    **[新しいカスタム API の作成]** ダイアログ ボックスが表示されます。

3.  **[API 名]** に「*completeall*」と入力して、チェック ボタンをクリックします。

    ![][2]

    これは新しい API を作成します。

    > [WACOM.NOTE] 既定のアクセス許可が設定されるため、アプリケーションのユーザーすべてがこのカスタム API を呼び出すことができます。ただし、アプリケーション キーはセキュリティの保護がない状態で配布または保存されるため、安全な資格情報として扱うことはできません。このため、データの変更またはモバイル サービスに影響する操作を、認証されたユーザーのみのアクセスに制限することを考慮する必要があります。

4.  API テーブル内の新しい **completeall** エントリをクリックします。

    ![][3]

5.  **[スクリプト]** タブをクリックし、既存のコードを次のコードに置き換えて、**[保存]** をクリックします。

        exports.post = function(request, response) {
            var mssql = request.service.mssql;
            var sql = "UPDATE todoitem SET complete = 1 " + 
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
            mssql.query(sql, {
                success: function(results) {            
                    if(results.length == 1)                         
                        response.send(200, results[0]);         
                }
            })
        };

    このコードは [mssql オブジェクト][]を使用し、**todoitem** テーブルに直接アクセスしてすべての項目の完了フラグを設定します。**exports.post** 関数を使用するため、クライアントは POST 要求を送信して操作を実行します。変更された行数は整数値としてクライアントに返されます。

> [WACOM.NOTE]
> カスタム API の関数に指定されている[要求][]と[応答][]オブジェクトが、[Express.js ライブラリ][]で実行されます。詳細については、「[カスタム API][]」を参照してください。

次に、quickstart アプリケーションを変更し、新しいボタンと、非同期的に新しいカスタム API を呼び出すコードを追加します。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

  [Azure の管理ポータル]: https://manage.windowsazure.com/
  []: ./media/mobile-services-create-custom-api/mobile-services-selection.png
  [1]: ./media/mobile-services-create-custom-api/mobile-custom-api-create.png
  [2]: ./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png
  [3]: ./media/mobile-services-create-custom-api/mobile-custom-api-select2.png
  [mssql オブジェクト]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
  [要求]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554218.aspx
  [応答]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303373.aspx
  [Express.js ライブラリ]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [カスタム API]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
