<properties 
	pageTitle="Azure Websites での基幹業務アプリケーションの作成" 
	description="このガイドでは、Azure Websites を使用してイントラネットの基幹業務アプリケーションを作成する方法 (技術概要) について説明します。これには、認証方式、Service Bus リレー、監視も含まれます。 
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



# Azure Websites での基幹業務アプリケーションの作成

基幹業務アプリケーションには、[Azure Websites] が最適です。基幹業務アプリケーションとは、社内業務用にセキュリティで保護する必要があるイントラネット アプリケーションです。通常は、会社のディレクトリ、社内のデータとサービスへの一部のアクセスや統合に対して認証が必要です。 

基幹業務アプリケーションを Azure Websites に移行すると、次のような大きなメリットがあります。

-  年間の業績考査を処理するアプリケーションなど、動的なワークロード のスケールを上下できます。考査期間になると (特に大規模な会社では) トラフィックが急増します。Azure では負荷が増大する考査期間中にインスタンスを増やし、それ以外の期間は元に戻すことができるので、コスト節約につながります。 
-  インフラストラクチャの取得や管理よりも、アプリケーション開発に集中できます。
-  従業員やパートナーがどこからでもアプリケーションを使用できるようにサポートできます。アプリケーションを使用する際、ユーザーは社内ネットワークに接続する必要がありません。IT スタッフは複雑なリバース プロキシの設定から解放されます。社内アプリケーションへのアクセスを保護する手段として、いくつかの認証方式があります。

次の図は、Azure Websites で実行されている基幹業務アプリケーションの一例です。技術的な投資を最小限にしながら Azure Websites と他のサービスと組み合わせるだけで何ができるかを示しています。**詳細については、図の中の要素をクリックしてください。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>注</strong>
<p>このガイドで取り上げるのは基幹業務アプリケーションと連携する最も一般的な分野やタスクですが、Azure Websites には特殊なニーズに対応できるその他の機能も備わっています。これらの機能については、<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/global-web-presence-solution-overview/">グローバル Web プレゼンス</a>および<a href="http://www.windowsazure.com/ja-jp/manage/services/web-sites/digital-marketing-campaign-solution-overview">デジタル マーケティング キャンペーン</a>に関するガイドをご覧ください。</p>
</div>

### 既存の資産の移行

既存の Web 資産をさまざまな言語やフレームワークから Azure Websites に移行します。

既存の Web 資産は Azure Websites 上で、.NET、PHP、Java、Node.js、Python のどれであろうと実行できます。Azure Websites への移行は、使い慣れた [FTP] ツールやソース管理システムを使用して行うことができます。Azure Websites は、[Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) のような一般的なソース管理オプションからの直接発行をサポートしています。

### 資産の保護

暗号化によって資産を保護し、社内でも社外でも企業ユーザーを認証し、資産の使用を承認します。 

[HTTPS] を使用して盗聴から内部資産を保護します。既に **\*.azurewebsites.net** というドメイン名が SSL 証明書に記載されているため、カスタム ドメインを使用する場合はその SSL 証明書を Azure Websites に移行できます。SSL 証明書ごとに月額料金 (時間単位) がかかります。詳細については、「[Websites 料金の詳細]」をご覧ください。

会社のディレクトリに対して[ユーザーを認証]します。Azure Websites は、Active Directory フェデレーション サービス (AD FS) などの自社の ID プロバイダーや、コーポレート Active Directory の展開で同期された Azure Active Directory テナントを使用してユーザーを認証できます。ユーザーは、社内でも社外でもシングル サインオンで Azure Websites の Web プロパティにアクセスできます。Windows Intune、Office 365 などの既存のサービスは、既に Azure Active Directory を使用しています。お使いの Web サイトに対する同じ Azure Active Directory テナントを[簡単に認証]できます。 

Web サイトのプロパティを使用する際に[ユーザーを承認]します。追加のコードを最小限に抑え、[[ユーザーの認証]] 修飾を使用するなどして、同一のオンプレミスの ASP.NET コーディング パターンを Azure Websites に移行できます。詳細なアクセス制御に対して、自社で管理するアプリケーションと同様の柔軟性を維持します。

### 社内リソースへの接続 ###

パフォーマンス向上のためにクラウドにある場合でもコンプライアンスのために社内にある場合でも、自分の Web サイト データやリソースに接続できます。Azure でデータを保持する方法の詳細については、「[Azure のトラスト センター]」をご覧ください。 

Web サイトで必要なものを [Azure SQL データベース] や [MySQL] など Azure のさまざまなデータベース バックエンドから選択できます。Azure でデータを安全に保持することで、地理的に Web サイトに近い場所でデータを保つことになり、パフォーマンスを最大限に高めることができます。

ただし、業務の状況によってはデータを社内に保持することが求められる場合もあります。Azure Websites を使用すると、データベース バックエンドなど社内リソースへの[ハイブリッド接続]を簡単に設定できます。社内のネットワークを一元管理したい場合は、多くの Azure Websites を、サイト間 VPN を持つ 1 つの [Azure の仮想ネットワーク] に統合します。ユーザーは、Azure Websites があたかも社内にあるかのように、内部設置型リソースにアクセスできます。[Enterprise Pizza - Service Bus][enterprisepizza] を使用して Web サイトから社内に接続します。

### 最適化

Autoscale 機能による自動サイズ設定、Azure Redis Cache によるキャッシュ、Web ジョブによるバックグラウンド タスクの実行、Azure Traffic Manager による高可用性の維持を通じて、基幹業務アプリケーションを最適化できます。

Azure Websites の[Web サイトの規模の設定]機能は、ワークロードの規模にかかわらず基幹業務アプリケーションのニーズを満たします。手動で Web サイトをスケールアウトする場合は、[Azure 管理ポータル]から行います。プログラムを使用する場合は、[サービス管理 API] または [PowerShell スクリプト]、自動で行う場合は自動スケール機能から行います。**標準**のホスティング プランでは、自動スケールを使用して、CPU 使用率に基づいて Web サイトを自動的にスケールアウトできます。ベスト プラクティスについては、「[Troy Hunt]'s [10 things I learned about rapidly scaling websites with Azure] (Troy Hunt の Azure による迅速な Web サイトのスケーリングで私が学んだ 10 の事項)」をご覧ください。

[Azure Redis Cache] を使用して、Web サイトの応答性を高められます。これにより、バックエンド データベースからのデータや、[ASP.NET セッション状態]や[出力キャッシュ]などのデータをキャッシュできます。

[Azure Traffic Manager] を使用して Web サイトの可用性を高く維持できます。プライマリ サイトに問題がある場合、Traffic Manager は**Failover** メソッドを使用して自動的にセカンダリ サイトにトラフィックをルーティングします。

### 監視と分析

Azure かサードパーティ製ツールを使用して、Web サイトのパフォーマンスを最新の状態に保つことができます。Web サイトの重要なイベントに関するアラートを受信します。Application Insight や HDInsight からの Web ログ分析を使用して、簡単にユーザーの分析を行うことができます。 

Azure Websites のダッシュボードでは、Web サイトの現在のパフォーマンス メトリックとリソース クォータの[概要]を取得できます。アプリケーションの可用性、パフォーマンス、使用状況などあらゆる情報に対して [Azure Application Insights] を使用して、迅速かつ強力なトラブルシューティング、診断、使用状況の分析を行います。または、[New Relic] などのサードパーティ製ツールを使用して、Web サイトに対する高度なデータ監視を行います。

**標準**のホスティング プランでは、サイトの応答性を監視し、サイトが応答しなくなるメール通知を受信します。詳細については、「[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]」をご覧ください。

## その他のリソース

- [Azure Websites のドキュメント](/ja-jp/documentation/services/websites/)
- [Learning map for Azure Websites (Azure Websites のラーニング マップ)](/ja-jp/documentation/articles/websites-learning-map/)
- [Azure Web ブログ](/blog/topics/web/)



[Azure Websites]:/ja-jp/services/websites/

[FTP]:/ja-jp/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/ja-jp/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/ja-jp/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/ja-jp/documentation/articles/web-sites-configure-ssl-certificate/
[Websites 料金の詳細]:/ja-jp/pricing/details/web-sites/#service-ssl
[ユーザーの認証]:/ja-jp/documentation/articles/web-sites-authentication-authorization/
[簡単な認証]:/blog/2014/11/13/azure-websites-authentication-authorization/
[ユーザーの承認]:/ja-jp/documentation/articles/web-sites-authentication-authorization/

[Azure のトラスト センター]:/ja-jp/support/trust-center/
[MySQL]:/ja-jp/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL データベース]:/ja-jp/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[ハイブリッド接続]:/ja-jp/documentation/articles/web-sites-hybrid-connection-get-started/
[Azure の仮想ネットワーク]:/ja-jp/documentation/articles/web-sites-integrate-with-vnet/

[スケール アップとスケール アウト]:/ja-jp/manage/services/web-sites/how-to-scale-websites/
[Azure 管理ポータル]:http://manage.windowsazure.com/
[サービス管理 API]:http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]:http://msdn.microsoft.com/ja-jp/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[10 things I learned about rapidly scaling websites with Azure (Azure による迅速な Web サイトのスケーリングで私が学んだ 10 の事項)]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET セッション状態]:https://msdn.microsoft.com/ja-jp/library/azure/dn690522.aspx
[出力キャッシュ]:https://msdn.microsoft.com/ja-jp/library/azure/dn798898.aspx

[概要]:/ja-jp/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/ja-jp/develop/net/how-to-guides/new-relic/
[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx



<!--HONumber=42-->
