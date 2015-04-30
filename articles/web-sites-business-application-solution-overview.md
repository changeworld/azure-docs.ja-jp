<properties 
	pageTitle="Azure App Service での基幹業務 Web アプリの作成" 
	description="このガイドでは、Azure App Service Web アプリ を使用してイントラネットの基幹業務アプリケーションを作成する方法 (技術概要) について説明します。これには、認証方式、Service Bus リレー、監視も含まれます。" 
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



# Azure App Service での基幹業務 Web アプリの作成

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリは基幹業務アプリケーションに最適な選択です。こうしたアプリケーションは、イントラネット アプリケーションで、社内業務用にセキュリティで保護する必要があります。通常は社内ディレクトリや内部設置型データとサービスの一部へのアクセス、またはその統合に対する認証が必要になります。 

基幹業務アプリケーションを App Service Web アプリに移行すると、次のような大きなメリットがあります。

-  年間業績評価を処理するアプリケーションなど、動的なワークロードをスケールアップ/スケールダウンできます。考査期間になると (特に大規模な会社では) トラフィックが急増します。Azure では負荷が増大する考査期間中にインスタンスを増やし、それ以外の期間は元に戻すことができるので、コスト節約につながります。 
-  インフラストラクチャの購入と管理よりもアプリケーションの開発に集中できます。
-  従業員やパートナーが任意の場所からアプリケーションを使用する際のサポートが強化されます。アプリケーションを使用する際、ユーザーは社内ネットワークに接続する必要がありません。IT スタッフは複雑なリバース プロキシの設定から解放されます。社内アプリケーションへのアクセスを保護する手段として、いくつかの認証方式があります。

以下は、App Service Web アプリで実行されている、基幹業務アプリケーションの例です。技術的な投資を最小限に抑えて、Web アプリをその他のサービスを構成することにより実現可能なことを図解しています。**トポグラフィ内の要素をクリックして、詳細をご覧ください。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

> [AZURE.NOTE]
> このガイドで取り上げるのは基幹業務アプリケーションで必要となる最も一般的な分野やタスクですが、Azure App Service Web アプリには特殊なニーズに対応できるその他の機能も備わっています。これらの機能については、[グローバル Web プレゼンス](web-sites-global-web-presence-solution-overview.md)と [デジタル マーケティング キャンペーン](web-sites-digital-marketing-application-solution-overview.md)に関するその他のガイドをご覧ください。

## 既存のアセットの表示

さまざまな言語やフレームワークから既存の web アセットを App Service Web アプリに表示させます。

既存のアセットは .NET、PHP、Java、Node.js、Pythonに関係なく App Service Web アプリで実行できます。使い慣れた [FTP] ツールやソース管理システムを使ってそれらを Web アプリに移動させることができます。Web アプリは、[Visual Studio]、[Visual Studio Online]、[Git] (ローカル、GitHub、BitBucket、DropBox、Mercurial など) などの一般的なソース管理オプションからの直接公開をサポートしています。

## アセットのセキュリティ保護

暗号化によってアセットを保護し、企業ユーザー (オンサイトやリモート) を認証して、ユーザーのアセットの使用を承認します。 

[HTTPS] で内部アセットを盗聴者から保護します。**\*.azurewebsites.net** ドメイン名にはすでに SSL 証明書が付属しており、カスタム ドメインを使用する場合は、SSL 証明書を App Service Web アプリ用に取り込むことができます。各 SSL 証明書には月単位の料金 (時間割り計算) が関連付けられています。詳細については、「[アプリ サービス料金]」をご覧ください。

企業ディレクトリに対して[ユーザーを認証]します。App Service Web アプリでは、Active Directory フェデレーション サービス (AD FS) などの内部設置型の ID プロバイダーや、企業の Active Directory 展開で同期済みの Azure Active Directory テナントでユーザーを認証できます。ユーザーは、オンサイトでも外出先でもシングル サインオンで Web アプリの Web プロパティにアクセスできます。Office 365 や Windows Intune, などの既存のサービスではすでに Azure Active Directory が使われています。[Easy Auth (簡単認証)] を通じて、簡単に Web アプリに同じ Azure Active Directory テナントを認証させることができます。 

Web プロパティの使用に対して、[ユーザーを承認]します。最小限の追加コードで、たとえば `[Authorize]` 装飾を使用して、同じ内部設置型 ASP.NET コード パターンを App Service Web アプリで使用できます。内部設置型で管理するアプリケーションのように、細かくアクセス制御できる柔軟性はそのままになります。

## 内部設置型リソースへの接続 ##

パフォーマンス用のクラウドやコンプライアンス用の内部設置型に関わらず、Web アプリのデータやリソースに接続します。Azure でのデータの保存方法の詳細については、「[Azure のトラスト センター]」をご覧ください。 

Azure のさまざまなデータベース バックエンドから選択して、[Azure SQL Database] や [MySQL] などの Web アプリのニーズに対応できます。Azure でデータを安全に保存することで、地理的にデータを Web アプリの近くに置き、パフォーマンスを最適化させます。

ただし、ビジネスによってはデータを内部設置型で保管する必要がある場合があります。App Service Web アプリでは、データベース バックエンドなどの内部設置型リソースを[ハイブリッド接続]に簡単に設定できます。内部設置型接続の管理を統合する場合は、サイト対サイトの VPN がある [Azure 仮想ネットワーク]で多くの Web アプリを統合できます。その後、Web アプリが内部設置型であるかのように内部設置型リソースにアクセスできるようになります。

## 最適化

自動スケールで自動的にスケールし、Azure Redis Cache でキャッシュして、Web ジョブでタスクをバックグラウンドで実行し、Azure Traffic Manager で高可用性を維持することで、基幹業務アプリケーションを最適にできます。

App Service Web アプリの[スケールアップ/スケールアウト]機能は、ワークロードのサイズに関係なく、基幹業務アプリケーションのニーズを満たします。Web アプリは [Azure 管理ポータル]から手動で、[サービス管理 API] や [PowerShell スクリプト] を通じてプログラムで、そして自動スケール機能によって自動でスケールアウトできます。**標準**階層では、自動スケールにより CPU の使用率に基づいて自動的に Web アプリをスケールアウトします。ベスト プラクティスは、[Troy Hunt] の「[Azure で Web アプリを迅速にスケーリングする 10 の事項 (ブログの投稿)]」をご覧ください。

[Azure Redis Cache] で Web アプリの応答性を高めます。バックエンド データベースからのデータのキャッシュと [ASP.NET のセッション状態]や[出力キャッシュ]などその他のことに使用します。

[Azure Traffic Manager] を使用して Web アプリの高可用性を維持します。Traffic Manager では**フェールオーバー** メソッドを使用し、プライマリ サイトに問題がある場合に、自動的にトラフィックをセカンダリ サイトにルーティングします。

## 監視と分析

Azure やサードパーティー製のツールで Web アプリのパフォーマンスを最新の状態に維持します。重要な Web アプリのイベントでアラートを受信します。Application Insight や HDInsight からの Web ログ分析でユーザー情報を簡単に取得します。 

[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)の Web アプリのブレードで、Web アプリの現在のパフォーマンス メトリックとリソースのクォータの[概要]を取得します。アプリケーション全体の可用性、パフォーマンス、使用に関する 360°ビューを取得するには、迅速でパワフルなトラブルシューティングと診断、使用状況を提供する [Azure Application Insights] を使用します。または [New Relic] など、Web アプリのデータの詳細監視を提供するサードパーティー製のツールを使用します。

**標準**階層では、アプリの応答性を監視して、アプリの応答が停止する際にメール通知を受信します。詳細については、「[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]」をご覧ください。

## その他のリソース

- [App Service Web アプリのドキュメント](/services/app-service/web/)
- [Azure App Service Web アプリの学習マップ](websites-learning-map.md)
- [Azure Web ブログ](/blog/topics/web/)

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[アプリ サービスを試す](http://go.microsoft.com/fwlink/?LinkId=523751)」をご覧ください。そこでは、アプリ サービスで有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Web サイトからアプリ サービスへの変更ガイドについては、次のものをご覧ください。[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 古いポータルから新しいポータルへの変更ガイドについては、次のものをご覧ください。[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)


[Azure App Service]: /services/app-service/web/

[FTP]:web-sites-deploy.md#ftp
[Visual Studio]:web-sites-dotnet-get-started.md
[Visual Studio Online]:cloud-services-continuous-delivery-use-vso.md
[Git]:web-sites-publish-source-control.md

[HTTPS]:web-sites-configure-ssl-certificate.md
[アプリ サービス料金]: /pricing/details/app-service/#ssl-connections
[ユーザーを認証]:web-sites-authentication-authorization.md
[Easy Auth (簡単認証)]:/blog/2014/11/13/azure-websites-authentication-authorization/
[ユーザーを承認]:web-sites-authentication-authorization.md

[Azure のトラスト センター]:/support/trust-center/
[MySQL]:web-sites-php-mysql-deploy-use-git.md
[Azure SQL Database]:web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[ハイブリッド接続]:web-sites-hybrid-connection-get-started.md
[Azure 仮想ネットワーク]:web-sites-integrate-with-vnet.md

[スケールアップ/スケールアウト]:web-sites-scale.md
[Azure 管理ポータル]:http://manage.windowsazure.com/
[サービス管理 API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell スクリプト]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[Azure で Web アプリを迅速にスケーリングする 10 の事項 (ブログの投稿)]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis Cache]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET のセッション状態]:https://msdn.microsoft.com/library/azure/dn690522.aspx
[出力キャッシュ]:https://msdn.microsoft.com/library/azure/dn798898.aspx

[概要]:web-sites-monitor.md
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:store-new-relic-cloud-services-dotnet-application-performance-management.md
[方法:Azure でアラート通知を受け取り、アラート ルールを管理する]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx


<!--HONumber=52-->