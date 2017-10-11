.NET アプリケーションを使用して、 **StackExchange.Redis**キャッシュ クライアントは、キャッシュ クライアント アプリケーションの構成が簡単にできる NuGet パッケージを使用して Visual Studio で構成できます。 

> [!NOTE]
> 詳細については、次を参照してください。、 [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) github ページおよび[StackExchange.Redis キャッシュ クライアント ドキュメント](http://github.com/StackExchange/StackExchange.Redis#documentation)です。
> 
> 

StackExchange.Redis NuGet パッケージを使用して Visual Studio で、クライアント アプリケーションを構成するでプロジェクトを右クリックし**ソリューション エクスプ ローラー**選択**NuGet パッケージの管理**です。 

![NuGet パッケージを管理します。](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

型**StackExchange.Redis**または**StackExchange.Redis.StrongName**検索 テキスト ボックスに、結果から、必要なバージョンを選択し、クリックして**インストール**です。

> [!NOTE]
> 厳密な名前のバージョンを使用する場合、 **StackExchange.Redis**クライアント ライブラリは、選択**StackExchange.Redis.StrongName**; それ以外の場合に選択**StackExchange.Redis**です。
> 
> 

![StackExchange.Redis NuGet パッケージ](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

NuGet パッケージは、ダウンロードし、StackExchange.Redis キャッシュ クライアントと Azure Redis Cache にアクセスするクライアント アプリケーションの必要なアセンブリ参照を追加します。

> [!NOTE]
> パッケージの更新を確認することができます StackExchange.Redis を使用するプロジェクトを構成していない場合、 **NuGet Package Manager**です。 チェックして、更新されたバージョンの StackExchange.Redis NuGet パッケージをインストールする をクリックして**更新**で、 **NuGet Package Manager**ウィンドウです。 StackExchange.Redis NuGet パッケージの更新がある場合は、更新されたバージョンを使用するプロジェクトを更新できます。
> 
> 

クリックして、StackExchange.Redis NuGet パッケージをインストールすることもできます。 **NuGet Package Manager**、 **Package Manager Console**から、**ツール**メニュー、およびから次のコマンドを実行している、**パッケージ マネージャー コンソール**ウィンドウです。
    
```
Install-Package StackExchange.Redis
```
