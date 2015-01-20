<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約" metaKeywords="クラウド サービス, 仮想マシン, Web サイト, 仮想ネットワーク, SQL データベース, サブスクリプション, ストレージ" description="Azure の一般的なサブスクリプションとサービス制限、上限の一覧表を掲載しています。" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="jeffreyg" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jroth" />

# Azure サブスクリプションとサービスの制限、クォータ、制約

以降、Microsoft Azure の最も一般的な制限を具体的に記載しています。現時点では、すべての Azure サービスをカバーするものではありません。今後、Azure Platform 全体を広くカバーするように制限を展開し、更新していく予定です。

- [サブスクリプションの制限](#subscription)
- [クラウド サービスの制限](#webworkerlimits)
- [仮想マシンの制限](#vmlimits)
- [Web サイトの制限](#websiteslimits)
- [ネットワークの制限](#networkinglimits)
- [ストレージの制限](#storagelimits)
- [バッチのプレビューの制限](#batchlimits)
- [DocumentDB プレビューの制限](#documentdblimits)
- [SQL Database の制限](#sqldblimits)
- [Media Services の制限](#mediaserviceslimits)
- [Service Bus の制限](#servicebuslimits)
- [Active Directory の制限](#adlimits)

> [WACOM.NOTE] 「**既定の制限**」を超える制限の引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料)][azurelimitsblogpost] してください。以下の表の「**上限**」の値を超える制限の引き上げはできません。「**上限**」列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

##<a name="subscription"></a>サブスクリプションの制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりの <a href="http://msdn.microsoft.com/ja-jp/library/azure/hh531793.aspx">コア数</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10,000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/azure/gg456328.aspx">サブスクリプションあたりの</a> 共同管理者数</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/storage-whatis-account/">サブスクリプションあたりの</a> ストレージ アカウント数</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-what-is/">サブスクリプションあたりの</a> クラウド サービス数</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">サブスクリプションあたりの</a> 仮想ネットワーク数<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/jj157100.aspx">サブスクリプションあたりの</a> ローカル ネットワーク数</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりの SQL データベース サーバー数</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>サーバーあたりの SQL データベース数</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりの DNS サーバー数</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりの予約済み IP 数</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりのホステッド サービスの証明書数</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/azure/jj156085.aspx">サブスクリプションあたりの</a> アフィニティ グループ数</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups/">サブスクリプションあたりの</a> リソース グループ数</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/services/batch/">リージョンあたり、サブスクリプションあたりの</a> バッチ プレビュー アカウント数</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>極小のインスタンスで、使用するコア数が 1 コアに満たなくても、コア制限を上限として 1 コアとカウントされます。

<sup>2</sup>それぞれの仮想ネットワークでサポートされる仮想ネットワーク ゲートウェイは 1 つだけです。

##<a name="webworkerlimits"></a>クラウド サービスの制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-what-is/">デプロイあたりの Web/worker ロール数<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InstanceInputEndpoint">デプロイあたりの</a> インスタンス入力エンドポイント</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InputEndpoint">デプロイあたりの</a> 入力エンドポイント</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/ja-jp/library/gg557552.aspx#InternalEndpoint">デプロイあたりの</a> 内部エンドポイント</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Web/worker ロールのクラウド サービスには、それぞれ運用環境用とステージング環境用の 2 つのデプロイを割り当てることができます。この制限は、別個のロール (構成) の数を指しています。ロールあたりのインスタンス数 (スケール) ではないので注意してください。 

##<a name="vmlimits"></a>仮想マシンの制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/ja-jp/documentation/services/virtual-machines/">クラウド サービスあたりの</a> 仮想マシン数<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>クラウド サービスあたりの入力エンドポイント数<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>仮想マシンを作成すると、そのマシンを含めるクラウド サービスが自動的に作成されます。その同じクラウド サービスに、複数の仮想マシンを追加することができます。

<sup>2</sup>仮想マシンは、そのホストとなるクラウド サービスの外にあるため、仮想マシンとの通信は、入力エンドポイントを使用して行われます。同じクラウド サービス内の仮想マシンは、すべての UDP ポートおよびすべての TCP ポート間での通信が自動的に許可され、内部的な通信が可能となっています。

##<a name="websiteslimits"></a>Web サイトの制限

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>ネットワークの制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>合計マシン数<sup>1</sup> (仮想ネットワーク <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">あたり)</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>仮想マシンまたはロール インスタンスの同時 TCP 接続数</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>エンドポイントあたりのアクセス制御リスト (ACL) 数<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>仮想ネットワークあたりのローカル ネットワーク サイト数</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>合計マシン数には、仮想マシンと Web/worker ロール インスタンスが含まれます。

<sup>2</sup>それぞれの仮想ネットワークでサポートされる[仮想ネットワーク ゲートウェイ][gateway]は 1 つだけです。

<sup>3</sup>ACL は、仮想マシンの入力エンドポイントでサポートされます。Web/worker ロールの場合は、入力エンドポイントとインスタンスの入力エンドポイントでサポートされます。

##<a name="storagelimits"></a>ストレージの制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース<sup>1</sup></th>
   <th align="left" valign="middle">既定の制限</th>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの容量 (TB)</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一の BLOB コンテナー、テーブル、キューの最大サイズ</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数</p></td>
   <td valign="middle"><p>制限はストレージ アカウントの容量のみ (500 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>ファイル共有の最大サイズ</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>ファイル共有内の最大ファイル数</p></td>
   <td valign="middle"><p>制限は、ファイル共有の合計容量のみ (5 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Basic 階層)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Standard 階層)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの合計要求レート (オブジェクト サイズは 1KB とする)</p></td>
   <td valign="middle"><p>毎秒最大 20,000 エンティティ (またはメッセージ)</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一 BLOB のターゲット スループット</p></td>
   <td valign="middle"><p>毎秒最大 60 MB または毎秒最大 500 要求</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一キューのターゲット スループット (1 KB のメッセージ)</p></td>
   <td valign="middle"><p>毎秒最大 2000 メッセージ</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一テーブル パーティションのターゲット スループット (1 KB のエンティティ)</p></td>
   <td valign="middle"><p>毎秒最大 2000 エンティティ</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度 (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合 10 Gbps、LRS に 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度 (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合 20 Gbps、LRS に 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度 (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合 5 Gbps、LRS に 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度 (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合 10 Gbps、LRS に 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup>これらの制限の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][storagelimits]」を参照してください。 

<sup>2</sup>Basic 階層の仮想マシンの場合、合計要求レート制限 20,000 に達するのを避けるためには、使用頻度の高い VHD がストレージ アカウントあたり 66 個を超えないようにしてください (20,000/300)。Standard 階層の仮想マシンの場合、使用頻度の高い VHD がストレージ アカウントあたり 40 個を超えないようにしてください (20,000/500)。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]」を参照してください。 

<sup>3</sup>GRS は、[Geo Redundant Storage (地理冗長ストレージ)][georedundantstorage] の略です。LRS は [Locally Redundant Storage (ローカル冗長ストレージ)][locallyredundantstorage] の略です。GRS はローカル冗長でもあることに注意してください。

##<a name="batchlimits"></a>バッチのプレビューの制限

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>DocumentDB プレビューの制限

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>SQL Database の制限

SQL Database の制限については、以下のトピックを参照してください。

 - [Azure SQL データベースのサービス階層 (エディション)][sqltiers]
 - [Azure SQL データベースのサービス階層とパフォーマンス レベル][sqltiersperflevels]
 - [Database Throughput Unit (DTU) Quotas (データベース スループット ユニット (DTU) のクォータ)][sqlDTU]
 - [SQL Database Resource Limits (SQL Database リソースの制限)][sqldblimits]

##<a name="mediaserviceslimits"></a>Media Services の制限

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>Service Bus の制限

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Active Directory の制限

Azure Active Directory (AD) については、次のトピックを参照してください

 - 「[Azure の Active Directory サービスの制限と制約事項][adlimitsandrestrictions]」

##<a name="seealso"></a>関連項目

[Understanding Azure Limits and Increases (Azure の制限と増設について)][azurelimitsblogpost]

[Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/ja-jp/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/ja-jp/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/ja-jp/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/ja-jp/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/ja-jp/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/ja-jp/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/ja-jp/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/ja-jp/library/azure/dn764971.aspx

<!--HONumber=35.2-->
