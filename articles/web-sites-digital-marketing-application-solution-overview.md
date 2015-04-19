<properties 
	pageTitle="Azure Websites でのデジタル マーケティング キャンペーンの作成" 
	description="このガイドでは、Azure Websites を使用してデジタル マーケティング キャンペーンを作成する方法 (技術概要) を説明します。デプロイメント、ソーシャル メディアの統合、スケールの戦略、監視も取り上げます。" 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Azure Websites でのデジタル マーケティング キャンペーン
デジタル マーケティング キャンペーンには、[Azure Websites] が最適です。通常、デジタル マーケティング キャンペーンは、短期的なマーケティング目標を達成するため一定期間のみ実施されます。この場合、主に 2 つのシナリオが考えられます。1 つは、サードパーティのマーケティング会社が顧客のためにキャンペーンを作成し、販促期間中はそのマーケティング会社がキャンペーンを管理するシナリオです。もう 1 つは、デジタル マーケティング キャンペーンをマーケティング会社が作成した後、そのリソースの所有権を顧客へ譲渡するシナリオです。その後は顧客側でデジタル マーケティング キャンペーンが運営管理されます。Azure Websites はどちらのシナリオにも適しています。 

> [AZURE.NOTE] アカウントにサインアップする前に Azure Websites を実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Websites で、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

Azure Websites を使用したグローバルなマルチ チャンネル デジタル マーケティング キャンペーンの例を以下に示します。技術的な投資を最小限に抑え、Azure Websites と他のサービスと組み合わせるだけで何ができるかを示しています。**詳細については、図の中の要素をクリックしてください。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> このガイドで取り上げるのは Azure Websites のデジタル マーケティング キャンペーンで必要となる最も一般的な分野やタスクですが、Azure Websites で実装できるその他の一般的なソリューションも備わっています。これらのソリューションについては、<a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">グローバル Web プレゼンス</a>および<a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">基幹業務アプリケーション</a>のガイドをご覧ください。

### 資産を最初から作成する、または既存の資産を移行する

ギャラリーでよく使われる CMS からすぐに新しいサイトを作成するか、さまざまな言語やフレームワークから既存の Web 資産を Azure Websites に移行できます。

Azure ギャラリーは、一般的な Web サイト コンテンツ管理システム (CMS) ([Orchard]、[Umbraco]、[Drupal]、[WordPress] など) からテンプレートを提供します。お気に入りの CMS フレーバーを使用して Web サイトを作成できます。ユーザーは、さまざまなデータベース バックエンド ([Azure SQL データベース]、[MySQL] など) から選択してニーズを満たすことができます。

既存の Web 資産は Azure Websites 上で、.NET、PHP、Java、Node.js、Python に関係なく実行できます。これらをよく知られている [FTP] ツールを使用して、Azure Websites に移行できます。デジタル マーケティング キャンペーンを頻繁に作成しているユーザーは、既にソース管理システムに Web 資産を持っている可能性があります。その場合は、一般的なソース管理オプション ([Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など))から Azure Websites ディレクトリに直接デプロイできます。

### 迅速性の維持

Azure Websites で、既存のソース管理から直接、継続的に発行することで迅速性を維持し、A/B テストを実行します。 

サイトの計画段階、プロトタイピング、開発の初期段階で、ユーザーとその顧客は、Azure Website の[ステージング スロットにデプロイする]ことで、キャンペーン サイトを運用する前に実際と同じ状態で動作を確認できます。ソース管理と Azure Websites を統合することで、ステージング スロットに[継続的に発行する]、準備ができるとダウンタイムなしで運用サイトにスワップします。 

また、実際の Web サイトへの変更を計画する場合、運用テスト機能を使用して、提案された更新について容易に [A/B テストを実行]し、実際のユーザーの操作を分析することで、サイト設計について情報に基づいた判断を下すことができます。


### ソーシャル化

Azure Websites のデジタル マーケティング キャンペーンは、Facebook や Twitter などの人気のあるプロバイダーで認証することでソーシャル メディアと統合できます。ASP.NET アプリケーションでのこの手法の使用例については、「[メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する]」をご覧ください。 

さらに、各ソーシャル メディア サイトでは、通常、.NET や他の多くのフレームワークから他の統合方法に関する情報を提供しています。

### リッチ メディアの使用とすべてのデバイスへの接続

他の Azure サービスを使用して、デジタル マーケティング キャンペーンを強化します。

-  [Azure Media Services] を使用したグローバルなビデオのアップロードおよびストリーミング
-  [Azure Marketplace の SendGrid サービス]を使用したユーザーへの電子メールの送信
-  [Mobile Services] を使用した、Windows、iOS、Android デバイスでの存在感の確立
-  [通知ハブ]を使用した数百万台のデバイスへのプッシュ通知の送信

### グローバル化

Azure Traffic Manager を使用した地域サイトへのサービス提供と、Azure CDN を使用した超高速のコンテンツ配信によってグローバル化します。

それぞれのリージョンでグローバルな顧客にサービスを提供するために、[Azure Traffic Manager] を使用して、サイトの利用者を最高のパフォーマンスを提供する地域サイトに導きます。また、サイトの負荷を、複数のリージョンにホストされている Web サイトの複数のコピーに均等に分散することもできます。

[Web サイトと Azure CDN を統合する]ことで、グローバルにユーザーに超高速の静的コンテンツを配信します。Azure CDN は、静的コンテンツをユーザーに最も近い [CDN ノード]にキャッシュすることで、待ち時間と Web サイトへの接続を最小限に抑えます。

### 最適化

自動スケールによる自動サイズ設定、Azure Redis Cache によるキャッシング、Web ジョブによるバックグラウンド タスクの実行、Azure Traffic Manager による高可用性の維持によってサイトを最適化します。

Azure Website の[スケールアップとスケールアウト]の機能は、予期できないワークロードに最適です。これはデジタル マーケティング キャンペーンに当てはまります。手動で Web サイトをスケールアウトする場合は、[Azure 管理ポータル]から行います。プログラムを使用する場合は、[サービス管理 API] または [PowerShell スクリプト]、自動で行う場合は自動スケール機能から行います。**標準**のホスティング プランでは、自動スケールを使用して、CPU 使用率に基づいて Web サイトを自動的にスケールアウトできます。この機能を使用すると、ユーザー操作に基づいて必要な場合にのみ Web サイトをスケールアウトすることで、最大の俊敏性と最小のコストを同時に実現できます。ベスト プラクティスについては、「[Troy Hunt]'s [10 things I learned about rapidly scaling websites with Azure (Azure による迅速な Web サイトのスケーリングで私が学んだ 10 の事項)]」をご覧ください。

[Azure Redis Cache] を使用して、Web サイトの応答性を高めます。これは、バックエンド データベースからのデータや、[ASP.NET セッション状態]や[出力キャッシュ]などのデータをキャッシュします。

[Azure Traffic Manager] を使用して Web サイトの高可用性を維持します。プライマリ サイトに問題がある場合、Traffic Manager は**フェールオーバー** メソッドを使用して自動的にセカンダリ サイトにトラフィックをルーティングします。

### 監視と分析

Azure またはサードパーティ製ツールを使用して、Web サイトのパフォーマンスを最新の状態に維持します。Web サイトの重要なイベントに関するアラートを受信します。Application Insight や HDInsight からの Web ログ分析を使用して、簡単にユーザーを分析できます。 

Azure Websites のダッシュボードでは、Web サイトの現在のパフォーマンス メトリックとリソース クォータの[概要]を取得します。アプリケーションの可用性、パフォーマンス、使用状況のすべての情報に対しては、[Azure Application Insights] を使用して、迅速かつ強力なトラブルシューティング、診断、使用状況の分析を行います。または、[New Relic] などのサードパーティ製ツールを使用して、Web サイトに対して高度なデータ監視を行います。

**標準**のホスティング プランでは、サイトの応答性を監視し、サイトが応答しなくなると電子メール通知を受信します。詳細については、「[方法:Receive Alert Notifications and Manage Alert Rules in Azure (Azure でアラート通知を受け取り、アラート ルールを管理する)][方法:Azure でアラート通知を受け取り、アラート ルールを管理する]」をご覧ください。

## その他のリソース

- [Azure Websites のドキュメント](/ja-jp/documentation/services/websites/)
- [Learning map for Azure Websites (Azure Websites のラーニング マップ)](websites-learning-map.md)
- [Azure Web ブログ](/blog/topics/web/)


[Azure Websites]:/ja-jp/services/websites/

[Orchard]:/ja-jp/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/ja-jp/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/ja-jp/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/ja-jp/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/ja-jp/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL データベース]:/ja-jp/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/ja-jp/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/ja-jp/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/ja-jp/documentation/articles/web-sites-publish-source-control/

[ステージング スロットにデプロイする]:/ja-jp/documentation/articles/web-sites-staged-publishing/ 
[継続的に発行する]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B テストを実行]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC アプリケーションを Azure の Web サイトに展開する]:/ja-jp/develop/net/tutorials/web-site-with-sql-database/

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace の SendGrid サービス]:/ja-jp/documentation/articles/sendgrid-dotnet-how-to-send-email/
[Mobile Services]:/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[通知ハブ]:/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Web サイトと Azure CDN を統合する]:/ja-jp/documentation/articles/cdn-websites-with-cdn/ 
[CDN ノード]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[スケール アップとスケール アウト]:/ja-jp/manage/services/web-sites/how-to-scale-websites/
[Azure 管理ポータル]:http://manage.windowsazure.com/
[サービス管理 API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure (Azure による迅速な Web サイトのスケーリングで私が学んだ 10 の事項)]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET セッション状態]:https://msdn.microsoft.com/ja-jp/library/azure/dn690522.aspx
[出力キャッシュ]:https://msdn.microsoft.com/ja-jp/library/azure/dn798898.aspx

[概要]:/ja-jp/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/ja-jp/develop/net/how-to-guides/new-relic/
[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  


<!--HONumber=42-->
