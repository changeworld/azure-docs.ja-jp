<properties 
	pageTitle="ハイブリッド接続の概要 |Azure" 
	description="セキュリティ、TCP ポート、サポートされる構成を含め、ハイブリッド接続について説明します。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>


# ハイブリッド接続の概要
ここでは、ハイブリッド接続の概要を示し、サポートされる構成および必要な TCP ポートを一覧表示します。


## ハイブリッド接続とは

ハイブリッド接続は Azure BizTalk サービスの機能の一種です。ハイブリッド接続は、Azure Web アプリ (以前の Web サイト) と Azure モバイル アプリ (以前のモバイル サービス) をファイアウォールの後ろにある内部設置型リソースに簡単に接続する便利な方法を提供します。

![Hybrid Connections][HCImage]

ハイブリッド接続には、以下の利点があります。

- Web アプリとモバイル アプリは既存の内部設置型のデータとサービスに安全にアクセスできます。
- 複数の Web アプリまたはモバイル アプリが内部設置型リソースにアクセスするためにハイブリッド接続を共有できます。 
- ネットワークへのアクセスに必要な TCP ポート数が最小限になります。
- ハイブリッド接続を使用するアプリケーションは、ハイブリッド接続を通じて発行される特定の内部設置型リソースにのみ接続します。
- SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用するすべての内部設置型リソースに接続できます。

	> [AZURE.NOTE]現在は、動的ポート (FTP パッシブ モードまたは拡張パッシブ モードなど) を使用する TCP ベースのサービスはサポートされていません。

- Azure Web アプリでサポートされるすべてのフレームワーク (.NET、PHP、Java、Python、Node.js) と Azure モバイル アプリでサポートされるすべてのフレームワーク (Node.js、.NET) で使用できます。
- Web アプリとモバイル アプリは、これらがローカル ネットワークに配置されている場合とまったく同じ方法で内部設置型リソースにアクセスできます。たとえば、内部設置型で使用される接続文字列は Azure でも使用できます。


また、エンタープライズの管理者には、ハイブリッド アプリケーションがアクセスする企業のリソースに対して、以下のような管理と表示の機能がハイブリッド接続で提供されます。

- 管理者はグループ ポリシー設定を使用することにより、ネットワークでハイブリッド接続を使用でき、ハイブリッド アプリケーションがアクセスできるリソースを指定することもできます。
- 企業ネットワークのイベント ログおよび監査ログに、ハイブリッド接続でアクセスされるリソースが表示されます。


## サポートされる構成

ハイブリッド接続は、以下のフレームワークおよびアプリケーションの組み合わせをサポートします。

- SQL Server への .NET Framework アクセス
- WebClient による HTTP/HTTPS サービスへの .NET Framework アクセス
- SQL Server または MySQL への PHP アクセス
- SQL Server、MySQL、Oracle への Java アクセス
- HTTP/HTTPS サービスへの Java アクセス

ハイブリッド接続を使用して内部設置型の SQL Server にアクセスする場合、以下の点を考慮する必要があります。

- 静的ポートを使用するように SQL Express の名前付きインスタンスを構成する必要があります。既定では、SQL Express の名前付きインスタンスは動的ポートを使用します。
- SQL Express の既定のインスタンスは静的ポートを使用しますが、TCP を有効にする必要があります。既定では、TCP は有効ではありません。
- クラスタリング グループまたは可用性グループを使用する場合、現在、`MultiSubnetFailover=true` モードはサポートされていません。
- 現在、`ApplicationIntent=ReadOnly` はサポートされていません。
- Azure アプリケーションおよび内部設置型 SQL サーバーでサポートされるエンド ツー エンドの承認方法として、SQL 認証が必要になる場合があります。


## セキュリティとポート

ハイブリッド接続では、共有アクセス署名 (SAS) の承認を使用して、Azure アプリケーションや内部設置型 Hybrid Connection Manager からの接続を保護します。アプリケーションと内部設置型 Hybrid Connection Manager には個別の接続キーが作成されます。これらの接続キーは、個別にロールオーバーまたは取り消しができます。

ハイブリッド接続では、アプリケーションや内部設置型の Hybrid Connection Manager にシームレスかつ安全にキーを配布できます。

「[Create and Manage Hybrid Connections (ハイブリッド接続の作成と管理)](integration-hybrid-connection-create-manage.md)」を参照してください。

**アプリケーションの承認は、ハイブリッド接続とは独立しています**。適切であればどのような種類の承認方法でも使用できます。承認方法は、Azure クラウドと内部設置型コンポーネントとの間でサポートされているエンド ツー エンドの承認方法によって異なります。たとえば、Azure アプリケーションは内部設置型 SQL Server にアクセスします。このシナリオでは、SQL 承認を、サポートされるエンド ツー エンドの承認方法として使用できます。

#### TCP ポート
ハイブリッド接続では、プライベート ネットワークからの送信 TCP 接続または送信 HTTP 接続のみが必要です。ネットワークへの受信接続を許可するために、ファイアウォール ポートを開いたり、ネットワーク境界の構成を変更したりする必要はありません。

ハイブリッド接続では、次の TCP ポートが使用されます。

Port | 必要である理由
--- | ---
9350 ～ 9354 | これらのポートはデータ転送に使用されます。Service Bus リレー マネージャーはポート 9350 を調べて、TCP 接続を利用できるかどうかを決定します。利用できる場合は、ポート 9352 も利用可能であるとみなされます。データ トラフィックはポート 9352 を経由します。<br/><br/>これらのポートへの発信接続を許可します。
5671 | ポート 9352 がデータ トラフィックに使用される場合、ポート 5671 は制御チャンネルとして使用されます。<br/><br/>これらのポートへの発信接続を許可します。 
80、443 | ポート 9352 とポート 5671 を使用できない場合、ポート 80 とポート 443 がデータ転送と制御チャンネルに使用される代替ポートになります。<br/><br/>これらのポートへの発信接続を許可します。<br/><br/>**注記** 他の TCP ポートの代わりにこれらのフォールバックを使用することは推奨されません。HTTP/WebSocket はデータ チャンネルのネイティブ TCP ではなくプロトコルとして使用されます。パフォーマンスの低下を招く可能性があります。



## 次へ

[ハイブリッド接続の作成と管理](integration-hybrid-connection-create-manage.md)<br/> [Azure Web サイトを内部設置型リソースに接続する](../web-sites-hybrid-connection-get-started.md)<br/> [Azure Web アプリから内部設置型 SQL Server に接続する](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Azure モバイル サービスとハイブリッド接続](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## 関連項目

[Microsoft Azure の BizTalk サービスを管理するための REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) [BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md) <br/> [Azure 管理ポータルを利用した BizTalk サービスの作成](biztalk-provision-services.md)<br/> [BizTalk サービス: ダッシュボード タブ、モニター タブ、スケール タブ](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!---HONumber=July15_HO3-->