ポータルからダウンロードしたサーバー プロジェクトでは、プッシュ通知が既に有効になっています。

ASP.NET プロジェクトでは、次のことを確認できます。

* `Microsoft.Azure.Mobile.Server.Notifications` NuGet パッケージがインストールされている。

* WebApiConfig.cs 内の MobileAppConfiguration オブジェクトで `UseDefaultConfiguration()` メソッドが呼び出されている。これによって、上記の NuGet パッケージが提供する `AddPushNotifications()` 拡張メソッドが呼び出されます。

<!---HONumber=August15_HO8-->