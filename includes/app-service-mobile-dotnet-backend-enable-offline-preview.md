ポータルからダウンロードされたサーバー プロジェクトでは、データ機能が既に有効になっています。

ASP.NET プロジェクトは、次のように設定されています。

* `Microsoft.Azure.Mobile.Server.Tables` と `Microsoft.Azure.Mobile.Server.Entity` NuGet パッケージがインストールされている。

* WebApiConfig.cs 内の MobileAppConfiguration オブジェクトで `UseDefaultConfiguration()` メソッドが呼び出されている。これによって、上記の NuGet パッケージが提供する `AddTablesWithEntityFramework()` 拡張メソッドが呼び出されます。

<!---HONumber=July15_HO4-->