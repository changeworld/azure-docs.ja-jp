<properties 
	pageTitle="Azure App Service Web アプリにグローバル Web プレゼンスを作成する" 
	description="このガイドでは、Azure App Service Web アプリに組織の (.COM) サイトをホストする方法 (技術概要) について説明します。これには、デプロイメント、カスタム ドメイン、SSL、監視が含まれます。" 
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
	ms.date="04/08/2015" 
	ms.author="cephalin"/>


# Azure App Service Web アプリにグローバル Web プレゼンスを作成する

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリは、.COM サイトにグローバル Web プレゼンスを確立するために必要な機能をすべて備えています。組織の規模に関係なく、ビジネス、ブランド認識、顧客とのコミュニケーションを促進するには、堅牢なセキュリティで保護された、スケーラブルなプラットフォームが必要です。App Service Web アプリは、Microsoft がサポートするビジネス継続性により、企業のブランドとアイデンティティを維持します。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

以下は、App Service Web アプリで実行されている .COM Web サイトの例です。技術的な投資を最小限に抑えて、Web アプリをその他のサービスを構成することにより実現可能なことを図解しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> このガイドでは、Azure App Service Web アプリで公開されている .COM サイトを実行するために調整されている最も一般的な領域とタスクの一部を紹介します。ただし、Azure App Service Web アプリで実装できるその他の一般的なソリューションもあります。これらのソリューションを確認するには、[デジタル マーケティング キャンペーン](web-sites-digital-marketing-application-solution-overview.md)の他のガイドと [基幹業務アプリケーション](web-sites-business-application-solution-overview.md)に関するその他のガイドをご覧ください.

## 最初から作成するか、既存のアセットを使用する

ギャラリーの人気の CMS から新しいサイトをすばやく作成するか、またはさまざまな言語とフレームワークから既存の Web アセットを App Service Web アプリに取り込みます。

Azure Marketplace では、[Orchard]、[Umbraco]、[Drupal]、[WordPress] など、一般的な Web サイト コンテンツ管理システム (CMS) からのテンプレートを提供しています。お気に入りの CMS フレーバーを使用して Web アプリを作成できます。[Azure SQL データベース]と [MySQL] を含むさまざまデータベースのバックエンドから目的に合ったものを選択できます。

既存のアセットは .NET、PHP、Java、Node.js、Pythonに関係なく App Service Web アプリで実行できます。使い慣れた [FTP] ツールやソース管理システムを使ってそれらを Web アプリに移動させることができます。Web アプリは、[Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) などの一般的なソース管理オプションからの直接公開をサポートしています。

## 確実に公開する

既存のソース管理システムから継続的に直接公開し、コンテンツのライブテストを行うことによって、Web サイトを確実に公開します。 

計画段階、プロトタイピング、サイト開発の初期段階で、App Service Web アプリのサイトを[ステージング スロットに展開]して実際に運用する前に実際の動作バージョンを確認できます。ソース管理を Web アプリと統合することにより、ステージング スロットへ[継続的に公開]し、準備ができたらダウンタイムなしで実稼働環境にスワップできます。実稼働サイトで問題が発生した場合は、すぐにサイトの以前のバージョンにスワップできます。 

また、実際の Web サイトへの変更を計画する場合、実稼働環境でのテスト機能を使用して、提案されている更新で簡単に [A/B テストを実行]できます。また、実際のユーザーの行動を分析し、サイトの設計において情報を得た上で決断できるようになります。

## ブランドとセキュリティ保護

無料の App Service Web アプリ ドメインを使用するか、登録したドメイン名にマップし、証明機関の署名入り SSL 証明書でブランドを安全に保護します。

Web サイトを Web アプリで実行する場合、**\*.azurewebsites.net** ドメインは無料です。または、Web サイトを GoDaddy などの DNS レジストリから取得した[カスタム ドメイン] (contoso.com など) にマップすることもできます。

ユーザー情報の収集、電子商取引の実行、その他の機密データ管理などを行う場合は、ブランドの評判や顧客を [HTTPS] で保護できます。**\*.azurewebsites.net** ドメイン名にはすでに SSL 証明書が付属しており、カスタム ドメインを使用する場合は、SSL 証明書を Web アプリ用に取り込むことができます。各 SSL 証明書には月単位の料金 (時間割り計算) が関連付けられています。詳細については、「[アプリ サービス料金]」をご覧ください。

## グローバル化

Azure Traffic Manager で諸地域のサイトにサービス提供し、Azure CDN でコンテンツを高速配信してグローバル化を目指します。

諸地域のグローバル顧客にサービスを提供するためには、[Azure Traffic Manager] を使用して最適なパフォーマンスを提供する各地域のサイトへサイト訪問者をルーティングします。または、複数の地域でホストされている Web アプリの複数のコピー間で、サイトの負荷を均等に分散できます。

[Azure CDN と Web アプリを統合させて]、静的コンテンツを世界中のユーザーに高速配信します。Web サイトへの待機時間と接続を最小限にしながら、Azure CDN はユーザーに最も近い [CDN ノード]で静的コンテンツをキャッシュします。

## 最適化

自動スケールで自動的にスケールし、Azure Redis Cache でキャッシュして、Web ジョブでタスクをバックグラウンドで実行し、Azure Traffic Manager で高可用性を維持することで、.COM サイトを最適化します。

ワークロードのサイズに関係なく、App Service Web アプリの[スケール アップやスケール アウト]機能は .COM サイトのニーズを満たしています。Web サイトは [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)から手動で、[サービス管理 API] や [PowerShell スクリプト]を通じてプログラムで、そして自動スケール機能によって自動でスケールアウトできます。**標準**ホスティング プランでは、自動スケールにより CPU 使用率に基づいて Web サイトを自動的にスケールアウトします。ベスト プラクティスは、[Troy Hunt] の「[Azure で Web アプリを迅速にスケーリングする 10 の事項 (ブログの投稿)]」をご覧ください。

[Azure Redis Cache] を使用して Web サイトの応答性を向上します。バックエンド データベースからのデータのキャッシュと [ASP.NET のセッション状態]や[出力キャッシュ]などその他のことに使用します。

[Azure Traffic Manager] を使用してWeb サイトの高可用性を維持します。Traffic Manager では**フェールオーバー** メソッドを使用し、プライマリ サイトに問題がある場合に、自動的にトラフィックをセカンダリ サイトにルーティングします。

## 監視と分析

Azure またはサードパーティ製のツールで Web サイトのパフォーマンスを最新の状態に維持します。Web サイトの重大なイベントでアラートを受信します。Application Insight や HDInsight からの Web ログ分析でユーザー情報を簡単に取得します。 

[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の Web アプリのブレードで、Web サイトの現在のパフォーマンス メトリックとリソースのクォータの[概要]を取得します。アプリケーション全体の可用性、パフォーマンス、使用に関する 360°ビューを取得するには、迅速でパワフルなトラブルシューティングと診断、使用状況を提供する [Azure Application Insights] を使用します。または [New Relic] など、Web サイトの詳細監視データを提供するサードパーティ製のツールを使用します。

**標準**ホスティング プランでは、サイトが応答を停止するたびに、応答性監視サイトは電子メール通知を受信します。詳細については、「[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]」をご覧ください。

## リッチ メディアを使用してすべてのデバイスに到達する

以下のようなリッチ メディアで .COM サイトを魅力的にします。

-  [Azure Media Services] でビデオをグローバルにアップロードしてストリームする
-  [Azure Marketplace の SendGrid サービス]でユーザーに電子メールを送信する

## その他のリソース

- [App Service Web アプリのドキュメント](/services/app-service/web/)
- [Azure App Service Web アプリの学習マップ](websites-learning-map.md)
- [Azure Web ブログ](/blog/topics/web/)

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure App Service]: /services/app-service/web/

[Orchard]:web-sites-dotnet-orchard-cms-gallery.md
[Umbraco]:web-sites-gallery-umbraco.md
[Drupal]:web-sites-php-migrate-drupal.md
[WordPress]:web-sites-php-web-site-gallery.md
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Azure SQL データベース]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[ステージング スロットに展開]:web-sites-staged-publishing.md 
[継続的に公開]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[A/B テストを実行]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[カスタム ドメイン]:web-sites-custom-domain-name.md
[HTTPS]:web-sites-configure-ssl-certificate.md
[アプリ サービス料金]: /pricing/details/app-service/#ssl-connections

[Azure Traffic Manager]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[Azure CDN と Web アプリを統合させて]:cdn-websites-with-cdn.md 
[CDN ノード]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[スケール アップやスケール アウト]:web-sites-scale.md
[Azure 管理ポータル]:http://manage.windowsazure.com/
[サービス管理 API]:https://msdn.microsoft.com/library/azure/ee460799.aspx
[PowerShell スクリプト]:https://msdn.microsoft.com/library/azure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[Azure で Web アプリを迅速にスケーリングする 10 の事項 (ブログの投稿)]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET のセッション状態]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[出力キャッシュ]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[概要]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:store-new-relic-cloud-services-dotnet-application-performance-management.md
[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure Media Services]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace の SendGrid サービス]:sendgrid-dotnet-how-to-send-email.md



<!--HONumber=52-->