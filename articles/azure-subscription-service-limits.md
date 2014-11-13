<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Azure の一般的なサブスクリプションとサービス制限、上限の一覧表を掲載しています。" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Azure サブスクリプションとサービスの制限、クォータ、制約

以降、Microsoft Azure の最も一般的な制限を具体的に記載しています。現時点では、すべての Azure サービスをカバーするものではありません。今後、Azure Platform 全体を広くカバーするように制限を展開し、更新していく予定です。

-   [サブスクリプションの制限][サブスクリプションの制限]
-   [クラウド サービスの制限][クラウド サービスの制限]
-   [仮想マシンの制限][仮想マシンの制限]
-   [Web サイトの制限][Web サイトの制限]
-   [ネットワークの制限][ネットワークの制限]
-   [ストレージの制限][ストレージの制限]
-   [DocumentDB プレビューの制限][DocumentDB プレビューの制限]
-   [SQL Database の制限][SQL Database の制限]
-   [Media Services の制限][Media Services の制限]
-   [Service Bus の制限][Service Bus の制限]

> [WACOM.NOTE]「**既定の制限**」を超える制限の引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料) して][オンライン カスタマー サポートに申請 (無料) して]ください。以下の表の「**上限**」の値を超える制限の引き上げはできません。「**上限**」列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

## <a name="subscription"></a>サブスクリプションの制限

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース</th>
<th align="left">既定の制限</th>
<th align="left">上限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/ja-jp/library/azure/hh531793.aspx">サブスクリプション</a>あたりのコア数<sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/ja-jp/library/azure/gg456328.aspx">共同管理者</a>数</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの<a href="http://azure.microsoft.com/ja-jp/documentation/articles/storage-whatis-account/">ストレージ アカウント</a>数</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-what-is/">クラウド サービス</a>数</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">仮想ネットワーク</a>数<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/ja-jp/library/jj157100.aspx">ローカル ネットワーク</a>数</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの SQL Database 数</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの論理 SQL Database サーバー数</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの DNS サーバー数</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの予約済み IP 数</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりのホステッド サービスの証明書数</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/ja-jp/library/azure/jj156085.aspx">アフィニティ グループ</a>数</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>極小のインスタンスで、使用するコア数が 1 コアに満たなくても、コア制限を上限として 1 コアとカウントされます。

<sup>2</sup>それぞれの仮想ネットワークでサポートされる仮想ネットワーク ゲートウェイは 1 つだけです。

## <a name="webworkerlimits"></a>クラウド サービスの制限

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース</th>
<th align="left">既定の制限</th>
<th align="left">上限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-what-is/">デプロイあたりの Web/worker ロール数<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InstanceInputEndpoint">インスタンスの入力エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InputEndpoint">入力エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InternalEndpoint">内部エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Web/worker ロールのクラウド サービスには、それぞれ運用環境用とステージング環境用の 2 つのデプロイメントを割り当てることができます。この制限は、別個のロール (構成) の数を指しています。ロールあたりのインスタンス数 (スケール) ではないので注意してください。

## <a name="vmlimits"></a>仮想マシンの制限

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース</th>
<th align="left">既定の制限</th>
<th align="left">上限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>クラウド サービスあたりの<a href="http://azure.microsoft.com/ja-jp/documentation/services/virtual-machines/">仮想マシン</a>数<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>クラウド サービスあたりの入力エンドポイント数<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>仮想マシンを作成すると、そのマシンをホストするクラウド サービスが自動的に作成されます。その同じクラウド サービスに、複数の仮想マシンを追加することができます。

<sup>2</sup>仮想マシンは、そのホストとなるクラウド サービスの外にあるため、仮想マシンとの通信は、入力エンドポイントを使用して行われます。同じクラウド サービス内の仮想マシンは、すべての UDP ポートおよびすべての TCP ポート間での通信が自動的に許可され、内部的な通信が可能となっています。

## <a name="websiteslimits"></a>Web サイトの制限

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>ネットワークの制限

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース</th>
<th align="left">既定の制限</th>
<th align="left">上限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>合計マシン数<sup>1</sup> (<a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">仮想ネットワーク</a><sup>2</sup>あたり)</p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>仮想マシンまたはロール インスタンスの同時 TCP 接続数</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>エンドポイントあたりのアクセス制御リスト (ACL) 数<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>仮想ネットワークあたりのローカル ネットワーク サイト数</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>合計マシン数には、仮想マシンと Web/worker ロール インスタンスが含まれます。

<sup>2</sup>それぞれの仮想ネットワークでサポートされる[仮想ネットワーク ゲートウェイ][仮想ネットワーク ゲートウェイ]は 1 つだけです。

<sup>3</sup>ACL は、仮想マシンの入力エンドポイントでサポートされます。Web/worker ロールの場合は、入力エンドポイントとインスタンスの入力エンドポイントでサポートされます。

## <a name="storagelimits"></a>ストレージの制限

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース<sup>1</sup></th>
<th align="left">既定の制限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの容量 (TB)</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="even">
<td align="left"><p>単一の BLOB コンテナー、テーブル、キューの最大サイズ</p></td>
<td align="left"><p>500 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数</p></td>
<td align="left"><p>制限はストレージ アカウントの容量のみ (500 TB)</p></td>
</tr>
<tr class="even">
<td align="left"><p>ファイル共有の最大サイズ</p></td>
<td align="left"><p>5 TB</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ファイル共有内の最大ファイル数</p></td>
<td align="left"><p>制限は、ファイル共有の合計容量のみ (5 TB)</p></td>
</tr>
<tr class="even">
<td align="left"><p>永続ディスクあたり最大 8 KB IOPS (Basic 階層)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>永続ディスクあたり最大 8 KB IOPS (Standard 階層)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>ストレージ アカウントあたりの合計要求レート (オブジェクト サイズは 1KB とする)</p></td>
<td align="left"><p>毎秒最大 20,000 エンティティ (またはメッセージ)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>単一 BLOB のターゲット スループット</p></td>
<td align="left"><p>毎秒最大 60 MB または毎秒最大 500 要求</p></td>
</tr>
<tr class="even">
<td align="left"><p>単一キューのターゲット スループット (1 KB のメッセージ)</p></td>
<td align="left"><p>毎秒最大 2000 メッセージ</p></td>
</tr>
<tr class="odd">
<td align="left"><p>単一テーブル パーティションのターゲット スループット (1 KB のエンティティ)</p></td>
<td align="left"><p>毎秒最大 2000 エンティティ</p></td>
</tr>
<tr class="even">
<td align="left"><p>ストレージ アカウントあたりの最大受信速度 (米国リージョン)</p></td>
<td align="left"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 20 Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの最大送信速度 (米国リージョン)</p></td>
<td align="left"><p>GRS<sup>3</sup> が有効な場合は 20 Gbps、LRS の場合は 30 Gbps</p></td>
</tr>
<tr class="even">
<td align="left"><p>ストレージ アカウントあたりの最大受信速度 (ヨーロッパおよびアジア リージョン)</p></td>
<td align="left"><p>GRS<sup>3</sup> が有効な場合は 5 Gbps、LRS の場合は 10 Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの最大送信速度 (ヨーロッパおよびアジア リージョン)</p></td>
<td align="left"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 15 Gbps</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>これらの制限の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]」を参照してください。

<sup>2</sup>Basic 階層の仮想マシンの場合、合計要求レート制限 20,000 に達するのを避けるためには、使用頻度の高い VHD がストレージ アカウントあたり 66 個を超えないようにしてください (20,000/300)。Standard 階層の仮想マシンの場合、使用頻度の高い VHD がストレージ アカウントあたり 40 個を超えないようにしてください (20,000/500)。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ][Azure の仮想マシンおよびクラウド サービスのサイズ]」を参照してください。

<sup>3</sup>GRS は [Geo Redundant Storage (地理冗長ストレージ)][Geo Redundant Storage (地理冗長ストレージ)] の略です。LRS は [Locally Redundant Storage (ローカル冗長ストレージ)][Locally Redundant Storage (ローカル冗長ストレージ)] の略です。GRS はローカル冗長でもあることに注意してください。

## <a name="documentdblimits"></a>DocumentDB プレビューの制限

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>SQL Database の制限

SQL Database の制限については、以下のトピックを参照してください。

-   [Azure SQL データベースのサービス階層 (エディション)][Azure SQL データベースのサービス階層 (エディション)]
-   [Azure SQL データベースのサービス階層とパフォーマンス レベル][Azure SQL データベースのサービス階層とパフォーマンス レベル]
-   [Database Throughput Unit (DTU) Quotas (データベース スループット ユニット (DTU) のクォータ)][Database Throughput Unit (DTU) Quotas (データベース スループット ユニット (DTU) のクォータ)]
-   [SQL Database Resource Limits (SQL Database リソースの制限)][SQL Database Resource Limits (SQL Database リソースの制限)]

## <a name="mediaserviceslimits"></a>Media Services の制限

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Service Bus の制限

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a> 関連項目

[Understanding Azure Limits and Increases (Azure の制限と増設について)][オンライン カスタマー サポートに申請 (無料) して]

[Azure の仮想マシンおよびクラウド サービスのサイズ][Azure の仮想マシンおよびクラウド サービスのサイズ]

  [サブスクリプションの制限]: #subscription
  [クラウド サービスの制限]: #webworkerlimits
  [仮想マシンの制限]: #vmlimits
  [Web サイトの制限]: #websiteslimits
  [ネットワークの制限]: #networkinglimits
  [ストレージの制限]: #storagelimits
  [DocumentDB プレビューの制限]: #documentdblimits
  [SQL Database の制限]: #sqldblimits
  [Media Services の制限]: #mediaserviceslimits
  [Service Bus の制限]: #servicebuslimits
  [オンライン カスタマー サポートに申請 (無料) して]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [仮想ネットワーク ゲートウェイ]: http://msdn.microsoft.com/ja-jp/library/azure/jj156210.aspx
  [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Azure の仮想マシンおよびクラウド サービスのサイズ]: http://msdn.microsoft.com/ja-jp/library/azure/dn197896.aspx
  [Geo Redundant Storage (地理冗長ストレージ)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [Locally Redundant Storage (ローカル冗長ストレージ)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Azure SQL データベースのサービス階層 (エディション)]: http://msdn.microsoft.com/ja-jp/library/azure/dn741340.aspx
  [Azure SQL データベースのサービス階層とパフォーマンス レベル]: http://msdn.microsoft.com/ja-jp/library/azure/dn741336.aspx
  [Database Throughput Unit (DTU) Quotas (データベース スループット ユニット (DTU) のクォータ)]: http://msdn.microsoft.com/ja-jp/library/azure/ee336245.aspx#DTUs
  [SQL Database Resource Limits (SQL Database リソースの制限)]: http://msdn.microsoft.com/ja-jp/library/azure/dn338081.aspx
