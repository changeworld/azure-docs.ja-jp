<properties 
   pageTitle="Cache ASP.NET セッション状態プロバイダー"
   description="Azure Redis Cache を使用して ASP.NET セッション状態を格納する方法を説明します。"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="12/17/2015"
   ms.author="sdanie" />

# Azure Redis Cache の ASP.NET セッション状態プロバイダー

Azure Redis Cache には、セッション状態プロバイダーが用意されています。セッション状態プロバイダーを使用すると、セッション状態を、メモリ内や SQL Server データベースにではなく、キャッシュに格納することができます。キャッシュ セッション状態プロバイダーを使用するには、まず対象のキャッシュを構成し、Redis Cache Session State NuGet パッケージを使用して、キャッシュに必要な構成を ASP.NET アプリケーションに対して行います。

ユーザー セッションの状態をなんらかの形で格納しないのは、実際のクラウド アプリケーションでは実用的でない場合が多いですが、方法によっては、パフォーマンスとスケーラビリティに与える影響が大きくなります。状態を格納する必要がある場合は、状態の量を少なくし、Cookie に格納することをお勧めします。この方法を利用できない場合は、ASP.NET セッション状態と分散型メモリ内キャッシュのプロバイダーを使用することを次にお勧めします。パフォーマンスとスケーラビリティの観点から最もお勧めできないのが、データベースを利用したセッション状態プロバイダーを使用する方法です。このトピックでは、Azure Redis Cache の ASP.NET セッション状態プロバイダーを使用する方法について説明します。その他のセッション状態のオプションについては、[ASP.NET セッション状態のオプション](#aspnet-session-state-options)に関するトピックを参照してください。

## ASP.NET セッション状態をキャッシュに格納する

Visual Studio で Redis Cache Session State NuGet パッケージを使用してクライアント アプリケーションを構成するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

![Azure Redis Cache Manage NuGet パッケージ](./media/cache-asp.net-session-state-provider/IC729541.png)

[オンライン検索] ボックスに「**RedisSessionStateProvider**」と入力し、結果の中からそのプロバイダーを選択して、[インストール] をクリックします。

>[AZURE.IMPORTANT]Premium レベルでクラスター機能を使用する場合は、[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 以降を使用する必要があります。そうしないと、例外がスローされます。これは重大な変更です。詳細については、「[v2.0.0 の重大な変更の詳細](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)」を参照してください。

![Azure Redis Cache セッション状態プロバイダー](./media/cache-asp.net-session-state-provider/IC751730.png)

Redis セッション状態プロバイダー NuGet パッケージは、StackExchange.Redis.StrongName パッケージに依存します。StackExchange.Redis.StrongName パッケージは、プロジェクト内に存在しなければインストールされます。厳密な名前を持つ StackExchange.Redis.StrongName パッケージ以外に、厳密な名前を持たない StackExchange.Redis バージョンもあります。厳密な名前を持たない StackExchange.Redis バージョンをプロジェクトで使用している場合は、Redis セッション状態プロバイダー NuGet パッケージのインストールの前か後にこのバージョンをアンインストールする必要があります。アンインストールしなかった場合は、名前の競合がプロジェクトで発生します。これらのパッケージの詳細については、[.NET キャッシュ クライアントの構成](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)に関するトピックを参照してください。

NuGet パッケージによって、必要なアセンブリ参照がダウンロードされて追加されます。さらに、web.config ファイルには、ASP.NET アプリケーションが Redis Cache Session 状態プロバイダーを使用するために必要な構成を記述した以下のセクションが追加されます。

    <sessionStatemode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
		<add name="MySessionStateStore"
     	  	host = "127.0.0.1" [String]
    		port = "" [number]
    		accessKey = "" [String]
    		ssl = "false" [true|false]
    		throwOnError = "true" [true|false]
    		retryTimeoutInMilliseconds = "0" [number]
    		databaseId = "0" [number]
    		applicationName = "" [String]
    		connectionTimeoutInMilliseconds = "5000" [number]
    		operationTimeoutInMilliseconds = "5000" [number]
		/>
        -->
		<add name="MySessionStateStore"type="Microsoft.Web.Redis.RedisSessionStateProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
    </sessionState>

コメント化されたセクションには、属性の例と各属性のサンプル設定が記述されています。

属性の構成には、Microsoft Azure ポータルのキャッシュ ブレードの値を使用してください。その他の値は適宜構成します。キャッシュ プロパティにアクセスする方法については、[Redis キャッシュ設定の構成](cache-configure.md#configure-redis-cache-settings)に関するトピックを参照してください。

-	**host**: キャッシュ エンドポイントを指定します。
-	**port**: SSL の設定に応じて、非 SSL ポートまたは SSL ポートを使用します。
-	**accessKey**: キャッシュのプライマリ キーまたはセカンダリ キーを使用します。
-	**ssl**: キャッシュとクライアント間の通信を SSL で保護する場合は true、保護しない場合は false を指定します。必ず適切なポートを指定してください。
	-	既定では、新しいキャッシュに対して非 SSL ポートは無効になっています。SSL ポートを使用するには、この設定に true を指定します。非 SSL ポートの有効化の詳細については、「[Azure Redis Cache の構成方法](cache-configure.md)」トピックの「[アクセス ポート](cache-configure.md#access-ports)」セクションを参照してください。
-	**throwOnError**: 失敗時に例外がスローされるようにする場合は true、操作の失敗時にエラー メッセージが表示されないようにする場合は false を指定します。静的 Microsoft.Web.Redis.RedisSessionStateProvider.LastException プロパティをチェックすることでエラーを確認できます。既定値は true です。
-	**retryTimeoutInMilliseconds**: 失敗した操作がこの時間に再試行されます。ミリ秒単位で指定します。最初は 20 ミリ秒後に再試行され、その後 retryTimeoutInMilliseconds の時間が経過するまで 1 秒ごとに再試行されます。この時間を過ぎるとすぐに、操作が最後に 1 回再試行されます。操作が失敗した場合、throwOnError 設定に応じて、例外がスローされて呼び出し元に戻ります。既定値は 0 です。これは再試行されないことを意味します。
-	**databaseId**: キャッシュ出力データに使用するデータベースを指定します。指定されていない場合は、既定値の 0 が使用されます。
-	**applicationName**: キーが `{<Application Name>_<Session ID>}_Data` として Redis に格納されます。これによって、複数のアプリケーションで同じキーを共有できます。このパラメーターは省略可能です。指定されていない場合は、既定値が使用されます。
-	**connectionTimeoutInMilliseconds**: この設定によって、StackExchange.Redis クライアントの connectTimeout 設定を上書きすることができます。指定されていない場合は、connectTimeout 設定の既定値である 5000 が使用されます。詳細については、[StackExchange.Redis 構成モデル](http://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。
-	**operationTimeoutInMilliseconds**: この設定によって、StackExchange.Redis クライアントの syncTimeout 設定を上書きすることができます。指定されていない場合は、syncTimeout 設定の既定値である 1000 が使用されます。詳細については、[StackExchange.Redis 構成モデル](http://go.microsoft.com/fwlink/?LinkId=398705)に関するページを参照してください。
							
これらのプロパティの詳細については、[Redis の ASP.NET セッション状態プロバイダーの発表](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)に関する元のブログ投稿を参照してください。

web.config の標準の InProc セッション状態プロバイダー セクションを忘れずにコメント アウトしてください。

    <!-- <sessionState mode="InProc" 
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider" 
                  type="System.Web.Providers.DefaultSessionStateProvider, 
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral, 
                        PublicKeyToken=31bf3856ad364e35" 
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

これらの手順を実行すると、アプリケーションが Redis Cache セッション状態プロバイダーを使用するように構成されます。アプリケーションでセッション状態を使用すると、状態が Azure Redis Cache インスタンスに格納されます。

>[AZURE.NOTE]既定のメモリ内の ASP.NET セッション状態プロバイダーに格納できるデータと異なり、キャッシュに格納されるデータは、シリアル化可能である必要があります。Redis のセッション状態プロバイダーを使用するときは、セッション状態に格納されるデータ型がシリアル化可能であることを確認してください。

## ASP.NET セッション状態のオプション

- メモリ内のセッション状態プロバイダー: このプロバイダーでは、メモリ内にセッション状態が格納されます。このプロバイダーを使用する利点は、単純で高速なことです。ただし、メモリ内プロバイダーを使用する場合、このプロバイダーは分散型でないため、Web Apps は拡張できません。

- SQL Server のセッション状態プロバイダー: このプロバイダーでは、SQL Server 内にセッション状態が格納されます。永続的なストレージにセッション状態を保持するには、このプロバイダーを使用する必要があります。Web アプリは拡張できますが、セッションに SQL Server を使用すると、Web アプリのパフォーマンスに影響を与えます。

- Redis Cache セッション状態プロバイダーなどの分散型メモリ内セッション状態プロバイダー: このプロバイダーを使用すると、両方の長所を活用できます。Web アプリで、単純かつ高速で、スケーラブルなセッション状態プロバイダーを使用できます。ただし、このプロバイダーではセッション状態がキャッシュに格納されるため、一時的なネットワーク障害など、分散型メモリ内キャッシュとの通信時に関するすべての要素をアプリで考慮する必要があります。キャッシュの使用に関するベスト プラクティスについては、Microsoft のパターンとプラクティスの[Azure クラウド アプリケーションの設計と実装のガイダンス](https://github.com/mspnp/azure-guidance)に関するページの[キャッシュのガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Caching.md)のセクションを参照してください。

セッション状態とその他のベスト プラクティスの詳細については、[Web 開発に関するベスト プラクティス (Azure を使用した実際のクラウド アプリケーションの構築)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices) に関するページを参照してください。

## 次のステップ

「[Azure Redis Cache の ASP.NET 出力キャッシュ プロバイダー](cache-asp.net-output-cache-provider.md)」を参照してください。

<!---HONumber=AcomDC_1223_2015-->