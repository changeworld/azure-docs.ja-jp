<properties 
	pageTitle="Azure App Service での基幹業務 Web アプリの作成" 
	description="このガイドでは、Azure App Service Web Apps を使用してイントラネットの基幹業務アプリケーションを作成する方法 (技術概要) について説明します。これには、認証方式、Service Bus リレー、監視も含まれます。" 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>



# Azure App Service での基幹業務 Web アプリの作成

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps は、基幹業務アプリケーションに最適な選択肢です。こうしたアプリケーションは、イントラネット アプリケーションで、社内業務用にセキュリティで保護する必要があります。通常は社内ディレクトリやオンプレミス データとサービスの一部へのアクセス、またはその統合に対する認証が必要になります。

基幹業務アプリケーションを App Service Web Apps に移行すると、次のような大きなメリットがあります。

-  年間業績評価を処理するアプリケーションなど、動的なワークロードをスケールアップ/スケールダウンできます。考査期間になると (特に大規模な会社では) トラフィックが急増します。Azure では負荷が増大する考査期間中にインスタンスを増やし、それ以外の期間は元に戻すことができるので、コスト節約につながります。 
-  インフラストラクチャの購入と管理よりもアプリケーションの開発に集中できます。
-  従業員やパートナーが任意の場所からアプリケーションを使用する際のサポートが強化されます。アプリケーションを使用する際、ユーザーは社内ネットワークに接続する必要がありません。IT スタッフは複雑なリバース プロキシの設定から解放されます。社内アプリケーションへのアクセスを保護する手段として、いくつかの認証方式があります。

以下は、App Service Web Apps で実行されている、基幹業務アプリケーションの例です。技術的な投資を最小限に抑えて、Web Apps をその他のサービスを構成することにより実現可能なことを図解しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
このガイドで取り上げるのは基幹業務アプリケーションで必要となる最も一般的な分野やタスクですが、Azure App Service Web Apps には特殊なニーズに対応できるその他の機能も備わっています。これらの機能については、[グローバル Web プレゼンス](web-sites-global-web-presence-solution-overview.md)および[デジタル マーケティング キャンペーン](web-sites-digital-marketing-application-solution-overview.md)のガイドを参照してください。

## 既存のアセットの表示

さまざまな言語やフレームワークから既存の web アセットを App Service Web Apps に表示させます。

既存のアセットは .NET、PHP、Java、Node.js、Pythonに関係なく App Service Web Apps で実行できます。使い慣れた [FTP] ツールやソース管理システムを使って、それらを Web Apps に移動することができます。Web Apps は、[Visual Studio]、[Visual Studio Team Services]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) などの一般的なソース管理オプションからの直接発行をサポートしています。

## アセットのセキュリティ保護

暗号化によってアセットを保護し、企業ユーザー (オンサイトやリモート) を認証して、ユーザーのアセットの使用を承認します。

[HTTPS] を使用して、内部アセットを盗聴者から保護します。***.azurewebsites.net** ドメイン名には SSL 証明書が既に付属しており、カスタム ドメインを使用する場合は、SSL 証明書を App Service Web Apps に取り込むことができます。各 SSL 証明書には月単位の料金 (時間割り計算) が関連付けられています。詳細については、[App Service の料金の詳細]をご覧ください。

企業ディレクトリに対して[ユーザーを認証]します。App Service Web Apps では、Active Directory フェデレーション サービス (AD FS) などのオンプレミスの ID プロバイダーや、企業の Active Directory のデプロイで同期済みの Azure Active Directory テナントでユーザーを認証できます。ユーザーは、オンサイトでも外出先でもシングル サインオンで Web Apps の Web プロパティにアクセスできます。Office 365、Microsoft Intune などの既存のサービスでは、既に Azure Active Directory が使われています。[Easy Auth (簡単認証)] により、Web アプリで同じ Azure Active Directory テナントを使用した認証を簡単に有効にすることができます。

ユーザーが Web プロパティを使用する際に、[ユーザーを承認]します。最小限の追加コードで、たとえば `[Authorize]` 装飾を使用して、同じオンプレミスの ASP.NET コーディング パターンを App Service Web Apps で使用できます。オンプレミスで管理するアプリケーションのように、細かくアクセス制御できる柔軟性はそのままになります。

## オンプレミスのリソースへの接続 ##

パフォーマンス用のクラウドやコンプライアンス用のオンプレミスに関わらず、Web アプリのデータやリソースに接続します。Azure でのデータの保存方法の詳細については、[Azure のトラスト センター]をご覧ください。

[Azure SQL Database] や [MySQL] など、Azure のさまざまなデータベース バックエンドの中から選択して Web アプリのニーズに対応できます。Azure でデータを安全に保存することで、地理的にデータを Web アプリの近くに置き、パフォーマンスを最適化させます。

ただし、ビジネスによってはデータをオンプレミスで保管する必要がある場合があります。App Service Web Apps では、データベース バックエンドなどのオンプレミスのリソースへの[ハイブリッド接続]を簡単に設定できます。オンプレミスの接続の管理を統合する場合は、サイト間 VPN を使用する 1 つの [Azure Virtual Network] に多数の Web アプリを統合できます。その後、Web アプリがオンプレミスであるかのようにオンプレミス リソースにアクセスできるようになります。

## 最適化

自動スケールで自動的にスケールし、Azure Redis Cache でキャッシュして、Web ジョブでタスクをバックグラウンドで実行し、Azure Traffic Manager で高可用性を維持することで、基幹業務アプリケーションを最適にできます。

App Service Web Apps の[スケールアップ/スケールアウト]機能は、ワークロードのサイズに関係なく、基幹業務アプリケーションのニーズを満たします。Web アプリは [Azure ポータル]から手動で、[サービス管理 API] や [PowerShell スクリプト]を通じてプログラムで、そして自動スケール機能によって自動でスケールアウトできます。**標準**階層では、自動スケールにより、CPU 使用率に基づいて Web アプリを自動的にスケールアウトできます。ベスト プラクティスについては、[Troy Hunt] の「[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]」をご覧ください。

[Azure Redis Cache] で Web アプリの応答性を高めます。バックエンド データベースのデータのキャッシュと [ASP.NET のセッション状態]や[出力キャッシュ]などのその他のことに使用します。

[Azure Traffic Manager] を使用して、Web アプリの高可用性を維持します。Traffic Manager では、**フェールオーバー** を使用して、プライマリ サイトに問題がある場合に、トラフィックをセカンダリ サイトに自動的にルーティングします。

## 監視と分析

Azure やサードパーティー製のツールで Web アプリのパフォーマンスを最新の状態に維持します。重要な Web アプリのイベントでアラートを受信します。Application Insight や HDInsight からの Web ログ分析でユーザー情報を簡単に取得します。

[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の Web アプリのブレードで、Web アプリの現在のパフォーマンス メトリックとリソースのクォータの[概要]を取得します。可用性、パフォーマンス、使用状況など、アプリケーションの全体像を把握するために、迅速かつ強力なトラブルシューティング、診断、使用状況の分析を提供する [Azure Application Insights] を使用します。または、Web アプリのデータの詳細な監視を行う、[New Relic] などのサードパーティー製ツールを使用します。

**標準**階層では、アプリの応答性を監視し、アプリが応答しなくなったときに電子メール通知を受信します。詳細については、「[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]」を参照してください。

## その他のリソース

- [App Service Web Apps のドキュメント](/services/app-service/web/)
- [Azure App Service Web Apps の学習マップ](/documentation/learning-paths/appservice-webapps/)
- [Azure Web ブログ](/blog/topics/web/)

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]



[Azure App Service]: /services/app-service/web/

[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Team Services]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service の料金の詳細]: /pricing/details/app-service/#ssl-connections
[ユーザーを認証]: web-sites-authentication-authorization.md
[Easy Auth (簡単認証)]: /blog/2014/11/13/azure-websites-authentication-authorization/
[ユーザーを承認]: web-sites-authentication-authorization.md

[Azure のトラスト センター]: /support/trust-center/
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[ハイブリッド接続]: web-sites-hybrid-connection-get-started.md
[Azure Virtual Network]: web-sites-integrate-with-vnet.md

[スケールアップ/スケールアウト]: web-sites-scale.md
[Azure ポータル]: http://portal.azure.com/
[サービス管理 API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET のセッション状態]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[出力キャッシュ]: https://msdn.microsoft.com/library/azure/dn798898.aspx
[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx

[概要]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

 

<!---HONumber=AcomDC_0302_2016-->