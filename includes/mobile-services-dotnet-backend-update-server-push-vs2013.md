この手順では、アプリケーションにプッシュ通知を送信する新しいカスタム [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) を作成します。これと同じコードを [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) またはバックエンド サービスの他の場所に実装することができます。

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトの Controllers フォルダーを右クリックし、**[追加]** を展開して **[新しいスキャフォールディング項目]** をクリックします。

	[スキャフォールディングの追加] ダイアログ ボックスが表示されます。

2. **[Azure Mobile Services]** を展開し、**[Azure Mobile Services カスタム コントローラー]** をクリックします。**[追加]** をクリックし、`NotifyAllUsersController` で **[コントローラー名]** を指定した後、**[追加]** をもう一度クリックします。

	![Web API の [スキャフォールディングの追加] ダイアログ](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

	**NotifyAllUsersController** という名前の新しい空のコントローラー クラスが作成されます。

3. 新しい NotifyAllUsersController.cs プロジェクト ファイルに、次の **using** ステートメントを追加します。

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. コントローラーで POST メソッドを実装する次のコードを追加します。

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
				// that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

	>[AZURE.NOTE]この POST メソッドは、いずれかの安全ではないアプリケーション キーを持つクライアントによって呼び出されます。エンドポイントをセキュリティで保護するには、認証を求めるメソッドまたはクラスに属性 `[AuthorizeLevel(AuthorizationLevel.User)]` を適用します。

<!---HONumber=July15_HO4-->