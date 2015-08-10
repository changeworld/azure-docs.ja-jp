<properties 
	pageTitle="Azure App Service Web アプリでデジタル マーケティング キャンペーンを作成する" 
	description="このガイドでは、Azure App Service Web アプリ を使用してデジタル マーケティング キャンペーンを作成する方法 (技術概要) について説明します。デプロイメント、ソーシャル メディアの統合、スケールの戦略、監視も取り上げます。" 
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
	ms.date="07/06/2015" 
	ms.author="cephalin"/>

# Azure App Service Web アプリでデジタル マーケティング キャンペーンを作成する
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリは、デジタル マーケティング キャンペーンに最適な選択です。デジタル マーケティング キャンペーンは通常、有効期間が短く、短期間のマーケティング目標を促進するためのものです。この場合、主に 2 つのシナリオが考えられます。1 つは、サードパーティのマーケティング会社が顧客のためにキャンペーンを作成し、販促期間中はそのマーケティング会社がキャンペーンを管理するシナリオです。もう 1 つは、デジタル マーケティング キャンペーンをマーケティング会社が作成した後、そのリソースの所有権を顧客へ譲渡するシナリオです。顧客はその後、独自にデジタル マーケティング キャンペーンを実行・管理します。両方のシナリオにとって最適です。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

以下は、App Service Web アプリを使用したマルチ チャンネルのグローバル デジタル マーケティング キャンペーンの例です。技術的な投資を最小限に抑えて、App Service Web アプリとその他のサービスを構成することにより実現可能なことを図解しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]このガイドでは、Azure App Service Web アプリでデジタル マーケティング キャンペーンを実行するために調整されている最も一般的な領域とタスクの一部を紹介します。ただし、App Service Web アプリで実装できる一般的な解決方法はほかにもあります。これらのソリューションについては、[グローバル Web プレゼンス](web-sites-global-web-presence-solution-overview.md)と[基幹業務アプリケーション](web-sites-business-application-solution-overview.md)のガイドをご覧ください。

## 最初から作成するか、既存のアセットを使用する

ギャラリーで人気の CMS から新しい Web アプリをすばやく作成するか、さまざまな言語やフレームワークから既存の Web アセットを App Service Web アプリに取り込みます。

Azure Marketplace では、[Orchard]、[Umbraco]、[Drupal]、[WordPress] など、一般的な Web サイト コンテンツ管理システム (CMS) からのテンプレートを提供しています。お気に入りの CMS フレーバーを使用して Web アプリを作成できます。[Azure SQL Database] と [MySQL] を含むさまざまデータベースのバックエンドから目的に合ったものを選択できます。

既存の Web アセットは .NET、PHP、Java、Node.js,、Python に関係なく Web アプリで実行できます。使い慣れた [FTP] ツールを使用して、それらを Web アプリに移動させることができます。デジタル マーケティング キャンペーンを頻繁に作成している場合、ソース管理システムに既存の Web アセットが存在している可能性があります。[Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) などの一般的なソース管理オプションから Web Apps を直接デプロイできます。

## 俊敏性を保つ

既存のソース管理から公開し続け、App Service Web アプリで A/B テストを実行して俊敏性を保ちます。

Web アプリの計画段階、プロトタイピング、開発の初期段階で、Web アプリの[ステージング スロットにデプロイ]して運用する前に、実際と同じ状態でキャンペーン アプリを確認できます。App Service Web アプリとソース管理を統合することで、ステージング スロットへ[継続的に公開]し、準備ができたらダウンタイムなしで実稼働環境にスワップできます。

また、実際の Web アプリへの変更を計画する場合、実稼働環境でテスト機能を使用して提案される更新で簡単に [A/B テストを実行]できます。また、実際のユーザーの行動を分析し、アプリ デザインにおいて情報を得た上で決断できるようになります。


## ソーシャルに

App Service Web アプリのデジタル マーケティング キャンペーンは Facebook や Twitter などの人気プロバイダーで認証することでソーシャル メディアと統合できます。ASP.NET アプリケーションにおけるこの手法の使用例については、「[認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ]」をご覧ください。

さらに、各ソーシャル メディア サイトでは通常、.NET やその他のフレームワークから統合できる別の方法に関する情報が提供されます。

## リッチ メディアを使用してすべてのデバイスに到達する

次のように、その他の Azure サービスでデジタル マーケティング キャンペーンを強化できます。

-  [Azure Media Services] でビデオをグローバルにアップロードしてストリームする
-  [Azure Marketplace の SendGrid サービス]でユーザーに電子メールを送信する
-  [Mobile Services] によって Windows、iOS、Android デバイスでプレゼンスを確立する
-  [Notification Hub] で多くのデバイスにプッシュ通知を送信する

## グローバル化

Azure Traffic Manager で諸地域のサイトにサービス提供し、Azure CDN でコンテンツを高速配信してグローバル化を目指します。

諸地域のグローバル顧客にサービスを提供するためには、[Azure Traffic Manager] を使用して最適なパフォーマンスを提供する各地域のサイトへサイト訪問者をルーティングします。または、複数の地域でホストされている Web アプリの複数のコピー間で、サイトの負荷を均等に分散できます。

[Azure CDN と Web アプリを統合させて]、静的コンテンツを世界中のユーザーに高速配信します。Web アプリへの待機時間と接続を最小限にしながら、Azure CDN はユーザーに最も近い [CDN ノード]で静的コンテンツをキャッシュします。

## 最適化

自動スケールで自動的にスケールし、Azure Redis Cache でキャッシュして、Web ジョブでタスクをバックグラウンドで実行し、Azure Traffic Manager で高可用性を維持することで、Web アプリを最適化します。

App Service Web Apps の[スケールアップやスケールアウト]機能は、予測できないデジタル マーケティング キャンペーンのワークロードに最適です。Web アプリは [Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)から手動で、[サービス管理 API] や [PowerShell スクリプト]を通じてプログラムで、そして自動スケール機能によって自動でスケールアウトできます。**標準**階層では、自動スケールにより、CPU 使用率に基づいて Web アプリを自動的にスケールアウトできます。この機能により、ユーザーのアクティビティを基に必要に応じて Web アプリをスケールアウトできるため、俊敏性を最大限にすると同時にコストを最小限に抑えることができます。ベスト プラクティスについては、[Troy Hunt] の「[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]」をご覧ください。

[Azure Redis Cache] で Web アプリの応答性を高めます。バックエンド データベースのデータのキャッシュと [ASP.NET のセッション状態]や[出力キャッシュ]などのその他のことに使用します。

[Azure Traffic Manager] を使用して、Web アプリの高可用性を維持します。Traffic Manager では、**フェールオーバー** を使用して、プライマリ サイトに問題がある場合に、トラフィックをセカンダリ サイトに自動的にルーティングします。

## 監視と分析

Azure やサードパーティー製のツールで Web アプリのパフォーマンスを最新の状態に維持します。重要な Web アプリのイベントでアラートを受信します。Application Insight や HDInsight からの Web ログ分析でユーザー情報を簡単に取得します。

[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の Web アプリのブレードで、Web アプリの現在のパフォーマンス メトリックとリソースのクォータの[概要]を取得します。可用性、パフォーマンス、使用状況など、アプリケーションの全体像を把握するために、迅速かつ強力なトラブルシューティング、診断、使用状況の分析を提供する [Azure Application Insights] を使用します。または、Web アプリのデータの詳細な監視を行う、[New Relic] などのサードパーティー製ツールを使用します。

**Standard** レベルでは、アプリの応答性を監視して、Web アプリの応答が停止する際にメール通知を受信します。詳細については、「[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]」を参照してください。

## その他のリソース

- [App Service Web Apps のドキュメント](/services/app-service/web/)
- [Azure App Service Web Apps の学習マップ](websites-learning-map.md)
- [Azure Web ブログ](/blog/topics/web/)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[Azure App Service]: /services/app-service/web/

[Orchard]: web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]: web-sites-gallery-umbraco.md
[Drupal]: web-sites-php-migrate-drupal.md
[WordPress]: web-sites-php-web-site-gallery.md
  
[MySQL]: web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]: web-sites-deploy.md#ftp
[Visual Studio]: web-sites-dotnet-get-started.md
[Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso.md
[Git]: web-sites-publish-source-control.md

[ステージング スロットにデプロイ]: web-sites-staged-publishing.md
[継続的に公開]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B テストを実行]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace の SendGrid サービス]: sendgrid-dotnet-how-to-send-email.md
[Mobile Services]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md
[Notification Hub]: ../mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Azure CDN と Web アプリを統合させて]: cdn-websites-with-cdn.md
[CDN ノード]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[スケールアップやスケールアウト]: /manage/services/web-sites/how-to-scale-websites/
[Azure Management Portal]: http://manage.windowsazure.com/
[サービス管理 API]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET のセッション状態]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[出力キャッシュ]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[概要]: /manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: /develop/net/how-to-guides/new-relic/
[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
 

<!---HONumber=July15_HO5-->