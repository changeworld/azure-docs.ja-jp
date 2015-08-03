ポータルからダウンロードされたサーバー プロジェクトでは、認証が既に有効になっています。

ASP.NET プロジェクトは、次のように設定されています。

* `Microsoft.Azure.Mobile.Server.Authentication` NuGet パッケージがインストールされている。

* WebApiConfig.cs 内の MobileAppConfiguration オブジェクトで `UseDefaultConfiguration()` メソッドが呼び出されている。これによって、上記の NuGet パッケージが提供する `AddAppServiceAuthentication()` 拡張メソッドが呼び出されます。さらに OWIN の起動中に `app.UseAppServiceAuthentication()` を呼び出すことで、認証に必要な OWIN ミドルウェアが登録されます。

<!---HONumber=July15_HO4-->