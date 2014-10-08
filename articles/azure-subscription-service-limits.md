<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Azure サブスクリプションとサービスの制限、クォータ、制約

以降、Microsoft Azure の最も一般的な制限を具体的に記載しています。現時点では、すべての Azure サービスをカバーするものではありません。今後、Azure Platform 全体を広くカバーするように制限を展開し、更新していく予定です。

-   [サブスクリプションの制限][]
-   [Web/worker の制限][]
-   [仮想マシンの制限][]
-   [ネットワークの制限][]
-   [ストレージの制限][]
-   [SQL Database の制限][]

> [WACOM.NOTE] 「**既定の制限**」の引き上げを希望される場合は、[カスタマー サポート][]にインシデントとしてお問い合わせください。以下の表の「**上限**」の値を超える制限の引き上げはできません。「**上限**」列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">サブスクリプション</a>あたりのコア数<sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">共同管理者</a>数</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの<a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">ストレージ アカウント</a>数</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">クラウド サービス</a>数</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">仮想ネットワーク</a>数<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">ローカル ネットワーク</a>数</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">アフィニティ グループ</a>数</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>極小のインスタンスで、使用するコア数が 1 コアに満たなくても、コア制限を上限として 1 コアとカウントされます。

<sup>2</sup>それぞれの仮想ネットワークでサポートされる仮想ネットワーク ゲートウェイは 1 つだけです。

## <a name="webworkerlimits"></a>Web/worker の制限

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
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">デプロイあたりの Web/worker ロール数<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">インスタンスの入力エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">入力エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">内部エンドポイント</a>数</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Web/worker ロールのクラウド サービスには、それぞれ運用環境用とステージング環境用の 2 つのデプロイメントを割り当てることができます。

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
<td align="left"><p>クラウド サービスあたりの<a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">仮想マシン</a>数<sup>1</sup></p></td>
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

<sup>2</sup>それぞれの仮想ネットワークでサポートされる[仮想ネットワーク ゲートウェイ][]は 1 つだけです。

<sup>3</sup>ACL は、仮想マシンの入力エンドポイントでサポートされます。Web/worker ロールの場合は、入力エンドポイントとインスタンスの入力エンドポイントでサポートされます。

## <a name="storagelimits"></a>ストレージの制限<sup>1</sup>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">リソース</th>
<th align="left">既定の制限</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの容量 (TB)<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>永続ディスクの最大 IOPS</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの最大 IOPS</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>ストレージ アカウントあたりの最大受信速度 (米国リージョン)</p></td>
<td align="left"><p>GRS<sup>4</sup> が有効な場合は 10 Gbps、無効な場合は 20 Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの最大受信速度 (ヨーロッパおよびアジア リージョン)</p></td>
<td align="left"><p>GRS<sup>4</sup> が有効な場合は 5 Gbps、無効な場合は 10 Gbps</p></td>
</tr>
<tr class="even">
<td align="left"><p>ストレージ アカウントあたりの最大送信速度 (米国リージョン)</p></td>
<td align="left"><p>GRS<sup>4</sup> が有効な場合は 20 Gbps、無効な場合は 30 Gbps</p></td>
</tr>
<tr class="odd">
<td align="left"><p>ストレージ アカウントあたりの最大送信速度 (ヨーロッパおよびアジア リージョン)</p></td>
<td align="left"><p>GRS<sup>4</sup> が有効な場合は 10 Gbps、無効な場合は 15 Gbps</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>これらの制限の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][]」を参照してください。

<sup>2</sup>ページ BLOB の場合、容量の使用量として計上されるのは使用中のページのみです。たとえば、127 GB の VHD の仮想マシンで、OS によって使用されているのが 30 GB のみである場合、127 GB 全体ではなく、VHD 内で使用されている 30 GB の部分だけが課金の対象となります。

<sup>3</sup>20,000 IOPS の制限に達するのを避けるためには、使用頻度の高い VHD がアカウントあたり 40 個を超えないようにしてください。

<sup>4</sup>Geo-Redundant Storage (地理冗長ストレージ) アカウント

## <a name="sqldblimits"></a>SQL Database の制限

SQL Database の制限については、以下のトピックを参照してください。

-   [Azure SQL データベースのサービス階層 (エディション)][]
-   [Azure SQL データベースのサービス階層とパフォーマンス レベル][]
-   [SQL Database Resource Limits (SQL Database リソースの制限)][]

## <a name="seealso"></a> 関連項目

[Azure の仮想マシンおよびクラウド サービスのサイズ][]

[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][]

[Azure SQL データベースのサービス階層 (エディション)][]

[Azure SQL データベースのサービス階層とパフォーマンス レベル][]

[SQL Database Resource Limits (SQL Database リソースの制限)][]

  [サブスクリプションの制限]: #subscription
  [Web/worker の制限]: #webworkerlimits
  [仮想マシンの制限]: #vmlimits
  [ネットワークの制限]: #networkinglimits
  [ストレージの制限]: #storagelimits
  [SQL Database の制限]: #sqldblimits
  [カスタマー サポート]: http://azure.microsoft.com/en-us/support/faq/
  [サブスクリプション]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [共同管理者]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [ストレージ アカウント]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [クラウド サービス]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [仮想ネットワーク]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [ローカル ネットワーク]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [アフィニティ グループ]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [インスタンスの入力エンドポイント]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [入力エンドポイント]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [内部エンドポイント]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [仮想マシン]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [仮想ネットワーク ゲートウェイ]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Azure SQL データベースのサービス階層 (エディション)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Azure SQL データベースのサービス階層とパフォーマンス レベル]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [SQL Database Resource Limits (SQL Database リソースの制限)]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Azure の仮想マシンおよびクラウド サービスのサイズ]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
