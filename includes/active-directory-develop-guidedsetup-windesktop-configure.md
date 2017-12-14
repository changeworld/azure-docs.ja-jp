
## <a name="create-an-application-express"></a>アプリケーションの作成 (簡易)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します。
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>ソリューションへのアプリケーション登録情報の追加 (上級)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. ガイド付きセットアップのオプションがオフになっていることを確認します。
4. [`Add Platform`] \(プラットフォームの追加) をクリックしてから [`Native Application`] \(ネイティブ アプリケーション) を選択し、[保存] をクリックします。
5. アプリケーション ID の GUID をコピーし、Visual Studio に戻って `App.xaml.cs` を開き、`your_client_id_here` を登録したアプリケーション ID に置き換えます。

```csharp
private static string ClientId = "your_application_id_here";
```
