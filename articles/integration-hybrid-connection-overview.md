<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />

# ハイブリッド接続の概要

ここでは、ハイブリッド接続の概要を示し、サポートされる構成および必要な TCP ポートを一覧表示します。具体的には次の処理が行われます。

-   [ハイブリッド接続とは][ハイブリッド接続とは]
-   [サポートされる構成][サポートされる構成]
-   [セキュリティ][セキュリティ]

## <a name="HCOverview"></a>ハイブリッド接続とは

ハイブリッド接続は、Azure Web サイトおよび Azure モバイル サービスを内部設置型リソースに簡単に接続する便利な方法を提供します。ハイブリッド接続は、Azure BizTalk サービスの機能の一種です。

![Hybrid Connections][Hybrid Connections]

ハイブリッド接続には、以下の利点があります。

-   Web サイトおよびモバイル サービスは、既存の内部設置型データおよびサービスに安全にアクセスできます。
-   複数の Web サイトまたはモバイル サービスが内部設置型リソースにアクセスするためにハイブリッド接続を共有できます。
-   ネットワークへのアクセスに必要な TCP ポート数が最小限になります。
-   ハイブリッド接続を使用するアプリケーションは、ハイブリッド接続を通じて発行される特定の内部設置型リソースにのみ接続します。
-   SQL Server、MySQL、HTTP Web API、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用するすべての内部設置型リソースに接続できます。

> [WACOM.NOTE] 現在は、動的ポート (FTP パッシブ モードまたは拡張パッシブ モードなど) を使用する TCP ベースのサービスはサポートされていません。

-   Azure Web サイトでサポートされるすべてのフレームワーク (.NET、PHP、Java、Python、Node.js) および Azure モバイル サービスでサポートされるすべてのフレームワーク (Node.js、.NET) で使用できます。
-   Web サイトおよびモバイル サービスは、これらがローカル ネットワークに配置されている場合とまったく同じ方法で内部設置型リソースにアクセスできます。たとえば、内部設置型で使用される接続文字列は Azure でも使用できます。

また、エンタープライズの管理者には、ハイブリッド アプリケーションがアクセスする企業のリソースに対して、以下のような管理と表示の機能がハイブリッド接続で提供されます。

-   管理者はグループ ポリシー設定を使用することにより、ネットワークでハイブリッド接続を使用でき、ハイブリッド アプリケーションがアクセスできるリソースを指定することもできます。
-   企業ネットワークのイベント ログおよび監査ログに、ハイブリッド接続でアクセスされるリソースが表示されます。

## <a name="KnownIssues"></a>サポートされる構成

ハイブリッド接続は、以下のフレームワークおよびアプリケーションの組み合わせをサポートします。

-   SQL Server への .NET Framework アクセス
-   WebClient による HTTP/HTTPS サービスへの .NET Framework アクセス
-   SQL Server または MySQL への PHP アクセス
-   SQL Server、MySQL、Oracle への Java アクセス
-   HTTP/HTTPS サービスへの Java アクセス

ハイブリッド接続を使用して内部設置型の SQL Server にアクセスする場合、以下の点を考慮する必要があります。

-   静的ポートを使用するように SQL Express の名前付きインスタンスを構成する必要があります。既定では、SQL Express の名前付きインスタンスは動的ポートを使用します。
-   SQL Express の既定のインスタンスは静的ポートを使用しますが、TCP を有効にする必要があります。既定では、TCP は有効ではありません。
-   クラスタリング グループまたは可用性グループを使用する場合、現在、MultiSubnetFailover=true モードはサポートされていません。
-   現在、ApplicationIntent=ReadOnly はサポートされていません。
-   Azure アプリケーションおよび内部設置型 SQL サーバーでサポートされるエンド ツー エンドの承認方法として、SQL 認証が必要になる場合があります。

## <a name="HCSecurity"></a>セキュリティ

ハイブリッド接続では、共有アクセス署名 (SAS) の承認を使用して、Azure アプリケーションや内部設置型 Hybrid Connection Manager からの接続を保護します。アプリケーションと内部設置型 Hybrid Connection Manager には個別の接続キーが作成されます。これらの接続キーは、個別にロールオーバーまたは取り消しができます。

ハイブリッド接続では、アプリケーションや内部設置型の Hybrid Connection Manager にシームレスかつ安全にキーを配布できます。

「[Create and Manage Hybrid Connections (ハイブリッド接続の作成と管理)][Create and Manage Hybrid Connections (ハイブリッド接続の作成と管理)]」を参照してください。

**アプリケーションの承認は、ハイブリッド接続とは独立しています**。適切であればどのような種類の承認方法でも使用できます。承認方法は、Azure クラウドと内部設置型コンポーネントとの間でサポートされているエンド ツー エンドの承認方法によって異なります。たとえば、Azure アプリケーションは内部設置型 SQL Server にアクセスします。このシナリオでは、SQL 承認を、サポートされるエンド ツー エンドの承認方法として使用できます。

#### TCP ポート

| **Port** | 理由                                                                                                        |
|----------|-------------------------------------------------------------------------------------------------------------|
| 80       | HTTP ポート。証明書の検証に使用されます。                                                                   |
| 443      | HTTPS ポート                                                                                                |
| 5671     | Azure への接続に使用します。TCP ポート 5671 が利用可能でない場合は、TCP ポート 443 を使用します。           |
| 9352     | データのプッシュとプルに使用されます。TCP ポート 9352 が利用可能でない場合は、TCP ポート 443 を使用します。 |

## 次へ

-   [ハイブリッド接続の作成と管理][Create and Manage Hybrid Connections (ハイブリッド接続の作成と管理)]
-   [Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)][Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]
-   [Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)][Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)]
-   [Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)][Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)]

<h2 id="see-also">関連項目</h2>
<ul>
<li><a href="http://msdn.microsoft.com/library/azure/dn232347.aspx">Windows Azure での BizTalk サービス管理の REST API に関するページ</a></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート</a><br/></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング</a><br/></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302281">BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ</a><br/></li>
</ul>

  [ハイブリッド接続とは]: #HCOverview
  [サポートされる構成]: #KnownIssues
  [セキュリティ]: #HCSecurity
  [Hybrid Connections]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
  [Create and Manage Hybrid Connections (ハイブリッド接続の作成と管理)]: http://azure.microsoft.com/ja-jp/documentation/articles/integration-hybrid-connection-create-manage
  [Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started

