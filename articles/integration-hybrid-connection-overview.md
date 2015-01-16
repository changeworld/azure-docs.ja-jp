<properties urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="ハイブリッド接続の概要 |Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="セキュリティを含め、ハイブリッド接続について説明します。" metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="mandia" />



# ハイブリッド接続の概要
ここでは、ハイブリッド接続の概要を示し、サポートされる構成および必要な TCP ポートを一覧表示します。具体的には次の処理が行われます。

- [ハイブリッド接続とは](#HCOverview)
- [サポートされる構成](#KnownIssues)
- [セキュリティとポート](#HCSecurity)

##<a name="HCOverview"></a>ハイブリッド接続とは

ハイブリッド接続は、Azure Web サイトおよび Azure モバイル サービスを内部設置型リソースに簡単に接続する便利な方法を提供します。ハイブリッド接続は、Azure BizTalk サービスの機能の一種です。

![Hybrid Connections][HCImage]

ハイブリッド接続には、以下の利点があります。

- Web サイトおよびモバイル サービスは、既存の内部設置型データおよびサービスに安全にアクセスできます。
- 複数の Web サイトまたはモバイル サービスが内部設置型リソースにアクセスするためにハイブリッド接続を共有できます。 
- ネットワークへのアクセスに必要な TCP ポート数が最小限になります。
- ハイブリッド接続を使用するアプリケーションは、ハイブリッド接続を通じて発行される特定の内部設置型リソースにのみ接続します。
- SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用するすべての内部設置型リソースに接続できます。

> [WACOM.NOTE] 現在は、動的ポート (FTP パッシブ モードまたは拡張パッシブ モードなど) を使用する TCP ベースのサービスはサポートされていません。

- Azure Web サイトでサポートされるすべてのフレームワーク (.NET、PHP、Java、Python、Node.js) および Azure モバイル サービスでサポートされるすべてのフレームワーク (Node.js、.NET) で使用できます。
- Web サイトおよびモバイル サービスは、これらがローカル ネットワークに配置されている場合とまったく同じ方法で内部設置型リソースにアクセスできます。たとえば、内部設置型で使用される接続文字列は Azure でも使用できます。


また、エンタープライズの管理者には、ハイブリッド アプリケーションがアクセスする企業のリソースに対して、以下のような管理と表示の機能がハイブリッド接続で提供されます。

- 管理者はグループ ポリシー設定を使用することにより、ネットワークでハイブリッド接続を使用でき、ハイブリッド アプリケーションがアクセスできるリソースを指定することもできます。
- 企業ネットワークのイベント ログおよび監査ログに、ハイブリッド接続でアクセスされるリソースが表示されます。


##<a name="KnownIssues"></a>サポートされる構成

ハイブリッド接続は、以下のフレームワークおよびアプリケーションの組み合わせをサポートします。

- SQL Server への .NET Framework アクセス
- WebClient による HTTP/HTTPS サービスへの .NET Framework アクセス
- SQL Server または MySQL への PHP アクセス
- SQL Server、MySQL、Oracle への Java アクセス
- HTTP/HTTPS サービスへの Java アクセス

ハイブリッド接続を使用して内部設置型の SQL Server にアクセスする場合、以下の点を考慮する必要があります。

- 静的ポートを使用するように SQL Express の名前付きインスタンスを構成する必要があります。既定では、SQL Express の名前付きインスタンスは動的ポートを使用します。
- SQL Express の既定のインスタンスは静的ポートを使用しますが、TCP を有効にする必要があります。既定では、TCP は有効ではありません。
- クラスタリング グループまたは可用性グループを使用する場合、現在、MultiSubnetFailover=true モードはサポートされていません。
- 現在、ApplicationIntent=ReadOnly はサポートされていません。
- Azure アプリケーションおよび内部設置型 SQL サーバーでサポートされるエンド ツー エンドの承認方法として、SQL 認証が必要になる場合があります。


##<a name="HCSecurity"></a>セキュリティとポート

ハイブリッド接続では、共有アクセス署名 (SAS) の承認を使用して、Azure アプリケーションや内部設置型 Hybrid Connection Manager からの接続を保護します。アプリケーションと内部設置型 Hybrid Connection Manager には個別の接続キーが作成されます。これらの接続キーは、個別にロールオーバーまたは取り消しができます。

ハイブリッド接続では、アプリケーションや内部設置型の Hybrid Connection Manager にシームレスかつ安全にキーを配布できます。 

「[ハイブリッド接続の作成と管理](http://azure.microsoft.com/ja-jp/documentation/articles/integration-hybrid-connection-create-manage)」を参照してください。 

**アプリケーションの承認は、ハイブリッド接続とは独立しています**。適切であればどのような種類の承認方法でも使用できます。承認方法は、Azure クラウドと内部設置型コンポーネントとの間でサポートされているエンド ツー エンドの承認方法によって異なります。たとえば、Azure アプリケーションは内部設置型 SQL Server にアクセスします。このシナリオでは、SQL 承認を、サポートされるエンド ツー エンドの承認方法として使用できます。

####TCP ポート
ハイブリッド接続では、プライベート ネットワークからの送信 TCP 接続または送信 HTTP 接続のみが必要です。ネットワークへの受信接続を許可するために、ファイアウォール ポートを開いたり、ネットワーク境界の構成を変更したりする必要はありません。

ハイブリッド接続では、次の TCP ポートが使用されます。

<table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>理由</th>
    </tr>
    <tr>
        <td>80</td>
        <td>HTTP ポート。証明書の検証に使用されます。</td>
    </tr>
    <tr>
        <td>443</td>
        <td>HTTPS ポート</td>
    </tr>
	<tr>
        <td>5671</td>
        <td>Azure への接続に使用します。TCP ポート 5671 が利用可能でない場合は、TCP ポート 443 を使用します。</td>
	</tr>
	<tr>
        <td>9352</td>
        <td>データのプッシュとプルに使用されます。TCP ポート 9352 が利用可能でない場合は、TCP ポート 443 を使用します。</td>
	</tr>
</table>


## 次へ

- [ハイブリッド接続の作成と管理](http://azure.microsoft.com/ja-jp/documentation/articles/integration-hybrid-connection-create-manage)
- [Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)](http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)


## 関連項目

- [Windows Azure での BizTalk サービス管理の REST API に関するページ](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
