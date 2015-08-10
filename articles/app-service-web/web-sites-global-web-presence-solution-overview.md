<properties 
	pageTitle="Azure App Service Web Apps にグローバル Web プレゼンスを作成する" 
	description="このガイドでは、Azure App Service Web Apps に組織の (.COM) サイトをホストする方法 (技術概要) について説明します。これには、デプロイ、カスタム ドメイン、SSL、監視が含まれます。" 
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


# Azure App Service Web Apps にグローバル Web プレゼンスを作成する

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps は、.COM サイトにグローバル Web プレゼンスを確立するために必要な機能をすべて備えています。組織の規模に関係なく、ビジネス、ブランド認識、顧客とのコミュニケーションを促進するには、堅牢なセキュリティで保護された、スケーラブルなプラットフォームが必要です。App Service Web Apps は、Microsoft がサポートするビジネス継続性により、企業のブランドとアイデンティティを維持します。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

以下は、App Service Web Apps で実行されている .COM Web サイトの例です。技術的な投資を最小限に抑えて、Web Apps をその他のサービスを構成することにより実現可能なことを図解しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。**

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]このガイドでは、Azure App Service Web Apps で公開されている .COM サイトを実行するために調整されている最も一般的な領域とタスクの一部を紹介します。ただし、Azure App Service Web Apps で実装できるその他の一般的なソリューションもあります。これらのソリューションについては、[デジタル マーケティング キャンペーン](web-sites-digital-marketing-application-solution-overview.md)と[基幹業務アプリケーション](web-sites-business-application-solution-overview.md)のガイドをご覧ください。

## 最初から作成するか、既存のアセットを使用する

ギャラリーの人気の CMS から新しいサイトをすばやく作成するか、またはさまざまな言語とフレームワークから既存の Web アセットを App Service Web Apps に取り込みます。

Azure Marketplace では、[Orchard]、[Umbraco]、[Drupal]、[WordPress] など、一般的な Web サイト コンテンツ管理システム (CMS) からのテンプレートを提供しています。お気に入りの CMS フレーバーを使用して Web アプリを作成できます。[Azure SQL Database] と [MySQL] を含むさまざまデータベースのバックエンドから目的に合ったものを選択できます。

既存のアセットは .NET、PHP、Java、Node.js、Pythonに関係なく App Service Web Apps で実行できます。使い慣れた [FTP] ツールやソース管理システムを使って、それらを Web Apps に移動することができます。Web Apps は、[Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) などの一般的なソース管理オプションからの直接発行をサポートしています。

## 確実に発行する

既存のソース管理システムから継続的に直接発行し、コンテンツのライブテストを行うことによって、Web サイトを確実に発行します。

計画段階、プロトタイピング、サイト開発の初期段階で、App Service Web Apps のサイトを[ステージング スロットにデプロイ]して実際に運用する前に実際の動作バージョンを確認できます。ソース管理を Web Apps と統合することにより、ステージング スロットへ[継続的に発行]し、準備ができたらダウンタイムなしで実稼働環境にスワップできます。実稼働サイトで問題が発生した場合は、すぐにサイトの以前のバージョンにスワップできます。

また、実際の Web サイトへの変更を計画する場合、実稼働環境でのテスト機能を使用して、提案されている更新で簡単に [A/B テストを実行]できます。また、実際のユーザーの行動を分析し、サイトの設計において情報を得た上で決断できるようになります。

## ブランドとセキュリティ保護

無料の App Service Web Apps ドメインを使用するか、登録したドメイン名にマップし、証明機関の署名入り SSL 証明書でブランドを安全に保護します。

Web サイトを Web Apps で実行する場合、**\*.azurewebsites.net** ドメインは無料です。または、Web サイトを GoDaddy などの DNS レジストリから取得した[カスタム ドメイン] (contoso.com など) にマップすることもできます。

ユーザー情報の収集、電子商取引の実行、その他の機密データ管理などを行う場合は、ブランドの評判や顧客を [HTTPS] で保護できます。**\*.azurewebsites.net** ドメイン名にはすでに SSL 証明書が付属しており、カスタム ドメインを使用する場合は、SSL 証明書を Web Apps 用に取り込むことができます。各 SSL 証明書には月単位の料金 (時間割り計算) が関連付けられています。詳細については、[App Service の料金の詳細]をご覧ください。

## グローバル化

Azure Traffic Manager で諸地域のサイトにサービス提供し、Azure CDN でコンテンツを高速配信してグローバル化を目指します。

諸地域のグローバル顧客にサービスを提供するためには、[Azure Traffic Manager] を使用して最適なパフォーマンスを提供する各地域のサイトへサイト訪問者をルーティングします。または、複数の地域でホストされている Web アプリの複数のコピー間で、サイトの負荷を均等に分散できます。

[Azure CDN と Web アプリを統合させて]、静的コンテンツを世界中のユーザーに高速配信します。Web サイトへの待機時間と接続を最小限にしながら、Azure CDN はユーザーに最も近い [CDN ノード]で静的コンテンツをキャッシュします。

## 最適化

自動スケールで自動的にスケールし、Azure Redis Cache でキャッシュして、Web ジョブでタスクをバックグラウンドで実行し、Azure Traffic Manager で高可用性を維持することで、.COM サイトを最適化します。

ワークロードのサイズに関係なく、App Service Web Apps の[スケールアップやスケールアウト]機能は .COM サイトのニーズを満たしています。Web サイトは [Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)から手動で、[サービス管理 API] や [PowerShell スクリプト]を通じてプログラムで、そして自動スケール機能によって自動でスケールアウトできます。**標準**ホスティング プランでは、自動スケールにより CPU 使用率に基づいて Web サイトを自動的にスケールアウトします。ベスト プラクティスについては、[Troy Hunt] の「[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]」をご覧ください。

[Azure Redis Cache] を使用して Web サイトの応答性を向上します。バックエンド データベースのデータのキャッシュと [ASP.NET のセッション状態]や[出力キャッシュ]などのその他のことに使用します。

[Azure Traffic Manager] を使用してWeb サイトの高可用性を維持します。Traffic Manager では、**フェールオーバー** を使用して、プライマリ サイトに問題がある場合に、トラフィックをセカンダリ サイトに自動的にルーティングします。

## 監視と分析

Azure またはサードパーティ製のツールで Web サイトのパフォーマンスを最新の状態に維持します。Web サイトの重大なイベントでアラートを受信します。Application Insight や HDInsight からの Web ログ分析でユーザー情報を簡単に取得します。

[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の Web アプリのブレードで、Web サイトの現在のパフォーマンス メトリックとリソースのクォータの[概要]を取得します。可用性、パフォーマンス、使用状況など、アプリケーションの全体像を把握するために、迅速かつ強力なトラブルシューティング、診断、使用状況の分析を提供する [Azure Application Insights] を使用します。または [New Relic] など、Web サイトの詳細監視データを提供するサードパーティ製のツールを使用します。

**標準**ホスティング プランでは、サイトが応答を停止するたびに、応答性監視サイトは電子メール通知を受信します。詳細については、「[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]」を参照してください。

## リッチ メディアを使用してすべてのデバイスに到達する

以下のようなリッチ メディアで .COM サイトを魅力的にします。

-  [Azure Media Services] でビデオをグローバルにアップロードしてストリームする
-  [Azure Marketplace の SendGrid サービス]でユーザーに電子メールを送信する

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
[継続的に発行]: http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B テストを実行]: http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[カスタム ドメイン]: web-sites-custom-domain-name.md
[HTTPS]: web-sites-configure-ssl-certificate.md
[App Service の料金の詳細]: /pricing/details/app-service/#ssl-connections

[Azure Traffic Manager]: http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Azure CDN と Web アプリを統合させて]: cdn-websites-with-cdn.md
[CDN ノード]: https://msdn.microsoft.com/library/azure/gg680302.aspx

[スケールアップやスケールアウト]: web-sites-scale.md
[Azure Management Portal]: http://manage.windowsazure.com/
[サービス管理 API]: https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell スクリプト]: https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]: https://twitter.com/troyhunt
[10 things I learned about rapidly scaling web apps with Azure (Azure での Web アプリの迅速なスケーリングに関する 10 項目)]: http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]: /blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET のセッション状態]: https://msdn.microsoft.com/library/azure/dn690522.aspx
[出力キャッシュ]: https://msdn.microsoft.com/library/azure/dn798898.aspx

[概要]: web-sites-monitor.md
[Azure Application Insights]: http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]: ../store-new-relic-cloud-services-dotnet-application-performance-management.md
[方法: Azure でアラート通知を受け取り、アラート ルールを管理する]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]: http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace の SendGrid サービス]: sendgrid-dotnet-how-to-send-email.md

 

<!---HONumber=July15_HO5-->