<properties 
	pageTitle="Azure Websites でのグローバル Web プレゼンスの作成" 
	description="このガイドでは、Azure Websites で組織の (.COM) サイトをホストする方法 (技術概要) について説明します。これには、デプロイメント、カスタム ドメイン、SSL、監視が含まれます。" 
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


# Azure Websites でのグローバル Web プレゼンスの作成

[Azure Web サイト]は .COM サイトのグローバル Web プレゼンスを確立するために必要なすべての機能を備えています。組織の規模に関係なく、ビジネスを展開し、ブランド認識を高め、顧客とのコミュニケーションを向上させるためには、堅牢なセキュリティで保護された、スケーラブルなプラットフォームが必要です。Azure の Web サイトでは、Microsoft がサポートするビジネス継続性によって、企業のブランド イメージとアイデンティティを維持できます。

> [AZURE.NOTE] アカウントにサインアップする前に Azure Web サイトを実際に使ってみるには、<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a> にアクセスしてください。Azure Web サイトで、有効期限付きの ASP.NET スターター サイトを無償で簡単に作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

Azure の Web サイトで実行されている、.COM の Web サイトの例を次に示します。技術的な投資を最小限に抑えたその他のサービスと組み合わせた Azure の Web サイトによって実現できることを簡潔に説明しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> このガイドでは、Azure の Web サイトでの公開 .COM サイトの実行に必要な最も一般的な領域とタスクの一部を取り上げています。ただし、Azure の Web サイトで実装できるその他の一般的な解決方法があります。これらの解決方法については、「<a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">デジタル マーケティング キャンペーン</a>」および「<a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">ビジネス アプリケーション</a>」のその他のガイドをご覧ください。

### 最初から作成する、または既存のアセットを表示する

ギャラリー内の人気のある CMS から新しいサイトを作成するか、またはさまざまな言語やフレームワークから既存の Web アセットを Azure の Web サイトに表示します。

Azure ギャラリーは、人気のある Web サイトのコンテンツ管理システム (CMS) で、[Orchard]、[Umbraco]、[Drupal]、[WordPress] などのテンプレートを提供します。お気に入りの CMS フレーバーを使用して Web サイトを作成できます。ニーズに応じて、[Azure SQL データベース]および [MySQL] などのさまざまなデータベースのバックエンドから選択できます。

.NET、PHP、Java、Node.js、または Python などの既存の Web アセットを Azure の Web サイトで実行できます。使い慣れた [FTP] ツールまたはソース管理システムを使用して、それらを Azure の Ｗeb サイトに移動できます。Azure の Web サイトは、[Visual Studio]、[Visual Studio Online]、および [Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) のよく知られたソース管理オプションからの直接発行をサポートしています。

### 確実に公開します

既存のソース管理システムから継続的に直接発行し、コンテンツの運用テストにより、Ｗeb サイトを確実に公開します。 

サイトの計画、プロトタイプ作成などの初期の開発段階では、Azure Web サイトの[ステージング スロットに展開して]実際に運用する前に、Web サイトの実際の動作バージョンを確認できます。ソース管理を Azure の Ｗeb サイトと統合することによって、ステージング スロットに[継続的に発行]し、準備が整ったら、ダウンタイムなしで実稼働環境にスワップできます。実稼働サイトで問題が発生した場合でも、すぐに以前のバージョンにスワップできます。 

また、実際の Web サイトに変更を計画する場合は、実稼働機能でのテストを使用して、提案された更新に対して容易に [A/B テストを実行]できるため、実際のユーザーの操作を分析して、サイト設計に対し、情報に基づいた決定を行います。

### ブランド化とセキュリティ保護

無料 Web サイトのドメインを使用するか、登録済みのドメイン名にマップし、CA 署名入りの SSL 証明書によってブランドを保護します。

**\*.azurewebsites.net** ドメインは、Web サイトを Azure の Web サイトで無料で実行できます。または、GoDaddy などの任意の DNS レジストリ から取得した [カスタム ドメイン] (contoso.com など) に Web サイトをマッピングすることもできます。

ユーザー情報を収集し、電子商取引を実行、またはその他の機密データを管理することにより、ブランドの評価と顧客の [HTTPS] を保護できます。**\*.azurewebsites.net** ドメイン名には、SSL 証明書が付属しており、カスタム ドメインを使用すると、その SSL 証明書を Azure の Web サイトに表示できます。各 SSL 証明書には、月単位の料金 (時間割り計算) が関連付けられています。詳細については、「[Web サイトの料金詳細]」をご覧ください。

### グローバルにアクセス

Azure Traffic Manager によって地域のサイトに Azure CDN による高速なコンテンツを配信を提供し、グローバルにアクセスします。

それぞれの地域のグローバルな顧客には、[Azure Traffic Manager] を使用して、最適なパフォーマンスを提供する地域のサイトにサイトの訪問者をルーティングします。または、複数の地域でホストされている Web サイトの複数のコピー間にサイト負荷を均等に分散できます。

[Web サイトと Azure CDN の統合]によって、グローバルなユーザーに高速な静的コンテンツを配信します。Azure CDN は、待機時間と、Web サイトへの接続を最小限にする、ユーザーに最も近い [CDN ノード]で静的コンテンツをキャッシュします。

### 最適化

自動スケールによる自動的なスケーリング、Azure Redis Cache によるキャッシュ、Web ジョブによるバックグラウンド タスクの実行、および Azure Traffic Manager による高可用性の維持によって .COM サイトを最適化します。

Azure Web サイトの[スケールアップおよびスケールアウト]の機能は、ワークロードのサイズに関わらず、.COM サイトのニーズを満たします。[Azure 管理ポータル]を使用して手動で、[サービス管理 API] または [PowerShell スクリプト]でプログラムを使用して、または自動スケール機能を使用して自動的に Web サイトをスケール アウトします。**標準**ホスティング プラン、自動スケールでは、CPU 使用率に基づいて Web サイトを自動的にスケール アウトできます。ベスト プラクティスについては、[Troy Hunt] の「[10 things I learned about rapidly scaling websites with Azure (Azure を使用して Web サイトを迅速にスケーリングする 10 の方法)]」をご覧ください。

[Azure Redis Cache] を使用して Web サイトの応答性を向上させます。バックエンド データベースと[ASP.NET セッション状態]および[出力キャッシュ]などからのデータのキャッシュに使用します。

[Azure Traffic Manager] を使用して Web サイトの高可用性を維持します。プライマリ サイトに問題がある場合は、**フェールオーバー** メソッドを使用して、Traffic Manager は、トラフィックを自動的にセカンダリ サイトにルーティングします。

### 監視および分析

Azure または サードパーティ製のツールで Web サイトのパフォーマンスを最新の状態に維持します。Web サイトに重要なイベントが発生した場合は、アラートを受信します。Application Insight または HDInsight の Web のログ分析によってユーザー情報を簡単に取得します。 

Web サイトの現在のパフォーマンス メトリックの[概要]と Azure Web サイトのダッシュボードのリソース クォータを参照します。アプリケーションの全般的な可用性、パフォーマンス、使用方法については、「[Azure Application Insights]」を使用して、迅速で強力なトラブルシューティング、診断、および使用方法の詳細についてご覧ください。または、[New Relic] のようなサードパーティのツールを使用して、Web サイトに詳細な監視データを提供します。

**標準**ホスティング プランでは、サイトが応答を停止すると、監視サイトの応答性機能によって電子メールの通知が受信されます。詳細については、「[方法:Receive Alert Notifications and Manage Alert Rules in Azure (Azure でアラート通知を受け取り、アラート ルールを管理する)]」をご覧ください。

### リッチ メディアを使用して、すべてのデバイスに到達

次のようなリッチ メディアで .COM サイトを魅力的なものにします。

-  [Azure メディア サービス]を使用してビデオをグローバルにアップロードおよびストリーミングする
-  [Azure Marketplace での SendGrid サービス]を使用してユーザーに電子メールを送信

## その他のリソース

- [Azure の Web サイトのドキュメント](/ja-jp/documentation/services/websites/)
- [Azure Web サイトのラーニング マップ](/ja-jp/documentation/articles/websites-learning-map/)
- [Azure の Web ブログ](/blog/topics/web/)



[Azure Web サイト]:/ja-jp/services/websites/

[Orchard]:/ja-jp/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/ja-jp/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/ja-jp/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/ja-jp/documentation/articles/web-sites-php-web-site-gallery/
[MySQL]:/ja-jp/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL Database]:/ja-jp/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/ja-jp/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/ja-jp/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/ja-jp/documentation/articles/web-sites-publish-source-control/

[ステージング スロットに展開します]:/ja-jp/documentation/articles/web-sites-staged-publishing/ 
[継続的に発行]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B テストの実行]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[カスタム ドメイン]:/ja-jp/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
[Web サイトの料金詳細]:/ja-jp/pricing/details/web-sites/#service-ssl

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Web サイトと Azure CDN の統合]:/ja-jp/documentation/articles/cdn-websites-with-cdn/ 
[CDN ノード]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[スケール アップとスケール アウト]:/ja-jp/manage/services/web-sites/how-to-scale-websites/
[Azure 管理ポータル]:http://manage.windowsazure.com/
[サービス管理 API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure (Azure を使用して Web サイトを迅速にスケーリングする 10 の方法)]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET セッション状態]:https://msdn.microsoft.com/ja-jp/library/azure/dn690522.aspx
[出力キャッシュ]:https://msdn.microsoft.com/ja-jp/library/azure/dn798898.aspx

[概要]:/ja-jp/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/ja-jp/develop/net/how-to-guides/new-relic/
[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure メディア サービス]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[SendGrid service in Azure Marketplace での SendGrid サービス]:/ja-jp/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
