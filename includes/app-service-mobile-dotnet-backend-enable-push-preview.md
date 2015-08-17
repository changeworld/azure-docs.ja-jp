ポータルからダウンロードされたサーバー プロジェクトでは、プッシュが既に有効になっています。

ASP.NET プロジェクトは、次のように設定されています。

* `Microsoft.Azure.Mobile.Server.Notifications` NuGet パッケージがインストールされている。

* WebApiConfig.cs 内の MobileAppConfiguration オブジェクトで `UseDefaultConfiguration()` メソッドが呼び出されている。これによって、上記の NuGet パッケージが提供する `AddPushNotifications()` 拡張メソッドが呼び出されます。

<!---HONumber=August15_HO6-->