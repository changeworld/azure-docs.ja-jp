---
title: Azure Sentinel の DNS 正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Azure Sentinel の DNS 正規化スキーマについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: 4bdb65fddfe7f72407c432fd03cce0558637ab39
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419014"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Azure Sentinel の DNS 正規化スキーマ リファレンス (パブリック プレビュー)

DNS 情報モデルは、DNS サーバーまたは DNS セキュリティ システムによって報告されるイベントを記述するために使用され、ソースに依存しない分析を有効にするために Azure Sentinel によって使用されます。

詳細については、「[正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> 現在、DNS 正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="guidelines-for-collecting-dns-events"></a>DNS イベントの収集に関するガイドライン

DNS は、多数のコンピューターが対象になる可能性があるという点でユニークなプロトコルです。 また、DNS では UDP が使用されるので、要求と応答は切り離されており、互いに直接関連付けられていません。

次の図は、4 つのセグメントを含む簡略化された DNS 要求フローを示したものです。 実際の要求は、もっと複雑で、さらに多くのセグメントが含まれる場合があります。

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="簡略化された DNS 要求フロー。":::

DNS 要求フローでは要求と応答のセグメントは互いに直接接続されないので、完全なログ記録を行うと大量の重複が発生する可能性があります。

ログに記録する最も重要なセグメントはクライアントへの応答であり、ドメイン名のクエリ、検索の結果、クライアントの IP アドレスが提供されます。 多くの DNS システムではこのセグメントのみがログに記録されますが、他のもログに記録する価値のある部分があります。 たとえば、DNS キャッシュのポイズニング攻撃では、多くの場合、アップストリーム サーバーからの偽の応答が利用されます。

データ ソースで完全な DNS ログがサポートされていて、複数のセグメントのログ記録を選択してある場合は、クエリを調整して、Azure Sentinel でのデータの重複を防ぐ必要があります。

たとえば、次の正規化を使用してクエリを変更することがあります。

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>パーサー

### <a name="source-agnostic-parsers"></a>ソースに依存しないパーサー

組み込みのすべてのパーサーを 1 つにまとめる、ソースに依存しないパーサーを使用して、構成済みのソース全体にわたって分析が実行されるようにするには、クエリでテーブル名として次の KQL 関数を使用します。

| 名前 | 説明 | 使用手順 |
| --- | --- | --- |
| **imDNS** | "*和集合*" を使用して、すべての DNS ソースからの正規化されたイベントを含める集約パーサー。 |- ソースに依存しない分析でソースを追加または削除する場合は、このパーサーを更新します。 <br><br>- ソースに依存しないクエリでこの関数を使用します。|
| **ASimDNS** | `imDns` 関数に似ていますが、パラメーターがサポートされていないため、 **[ログ]** ページの時刻の選択で `custom` 値を必ずしも使用する必要はありません。 |- ソースに依存しない分析でソースを追加または削除する場合は、このパーサーを更新します。<br><br>- パラメーターを使用する予定がない場合は、ソースに依存しないクエリでこの関数を使用します。|
| **vimDNS\<vendor\>\<product\>** | ソース固有のパーサーでは、*imDNSWindowsOMS* などの特定のソース用の正規化が実装されています。 |- 組み込みの正規化パーサーがない場合は、ソースにソース固有のパーサーを追加します。 新しいパーサーへの参照を含むように、`im` 集約パーサーを更新します。 <br><br>- 解析と正規化の問題を解決するには、ソース固有のパーサーを更新します。<br><br>- ソース固有の分析には、ソース固有のパーサーを使用します。|
| **ASimDNS\<vendor\>\<product\>** | ソース固有のパーサーでは、特定のソース用の正規化が実装されています。 `vim*` 関数とは異なり、`ASimDNS*` 関数ではパラメーターがサポートされていません。 |- 組み込みの正規化パーサーがない場合は、ソースにソース固有のパーサーを追加します。 新しいパーサーへの参照を含むように、`ASim` 集約パーサーを更新します。<br><br>- 解析と正規化の問題を解決するには、ソース固有のパーサーを更新します。<br><br>- パラメーターを使用しない場合の対話型クエリには、ソース固有のパーサー `ASim` を使用します。|
| | | |

パーサーは、[Azure Sentinel GitHub リポジトリ](https://aka.ms/azsentinelDNS)からデプロイできます。

### <a name="built-in-source-specific-parsers"></a>組み込みのソース固有パーサー

Azure Sentinel には、次の組み込みの製品固有の DNS パーサーが用意されています。

  - **Microsoft DNS サーバー**、Log Analytics エージェントを使用して収集されます - ASimDnsMicrosoftOMS (通常)、vimDnsMicrosoftOMS (パラメーター化)
  - **Cisco Umbrella** - ASimDnsCiscoUmbrella (通常)、vimDnsCiscoUmbrella (パラメーター化)
  - **Infoblox NIOS** - ASimDnsInfobloxNIOS (通常)、vimDnsInfobloxNIOS (パラメーター化)
  - **GCP DNS** - ASimDnsGcp (通常)、vimDnsGcp (パラメーター化)
  - **Corelight Zeek DNS イベント** - ASimDnsCorelightZeek (通常)、vimDnsCorelightZeek  (パラメーター化)
  - Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集される **Sysmon for Windows** (イベント 22)、Event と WindowsEvent 両方のテーブルをサポート - ASimDnsMicrosoftSysmon (通常)、vimDnsMicrosoftSysmon (パラメーター化)

パーサーは、[Azure Sentinel GitHub リポジトリ](https://aka.ms/azsentinelDNS)からデプロイできます。

### <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する

DNS 情報モデルにカスタム パーサーを実装するときは、次の構文を使用して KQL 関数に名前を付けます: `vimDns<vendor><Product` (パラメーター化されたパーサーの場合)、`ASimDns<vendor><Product` (通常のパーサーの場合)。

### <a name="filtering-parser-parameters"></a>パーサー パラメーターのフィルター処理

`im` および `vim*` パーサーでは、[フィルター処理パラメーター](normalization-about-parsers.md#optimized-parsers)がサポートされています。 これらのパーサーは省略可能ですが、クエリのパフォーマンスを向上させることができます。

次のフィルター処理パラメーターを使用できます。

| 名前     | 種類      | 説明 |
|----------|-----------|-------------|
| **starttime** | DATETIME | この時間以降に実行された DNS クエリのみをフィルター処理します。 |
| **endtime** | DATETIME | この時間以前に実行が完了した DNS クエリのみをフィルター処理します。 |
| **srcipaddr** | string | この送信元 IP アドレスからの DNS クエリのみをフィルター処理します。 |
| **domain_has_any**| 動的 | `domain` (または`query`) に、一覧表示されているいずれかのドメイン名が (イベント ドメインの一部として) 含まれている場合に、DNS クエリのみをフィルター処理します。
| **responsecodename** | string | 応答コード名が指定された値と一致する DNS クエリのみをフィルター処理します。 例: NXDOMAIN |
| **response_has_ipv4** | string | 指定された IP アドレスまたは IP アドレス プレフィックスで応答フィールドが始まる DNS クエリのみをフィルター処理します。 このパラメーターは、単一の IP アドレスまたはプレフィックスに対してフィルター処理する場合に使用します。 応答が指定されていないソースの結果は返されません。|
| **response_has_any_prefix** | 動的| 一覧表示されているいずれかの IP アドレスまたは IP アドレス プレフィックスで応答フィールドが始まる DNS クエリのみをフィルター処理します。 このパラメーターは、IP アドレスまたはプレフィックスの一覧に対してフィルター処理する場合に使用します。 応答が指定されていないソースの結果は返されません。 |
| **eventtype**| string | 指定した種類の DNS クエリのみがフィルター処理されます。 値が指定されていない場合は、検索クエリのみが返されます。 |
||||

たとえば、ドメイン名の解決に失敗した最後の日から DNS クエリのみをフィルター処理するには、次のように指定します。

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

指定されたドメイン名の一覧に対して DNS クエリのみをフィルター処理するには、次を使用します。

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## <a name="normalized-content"></a>正規化されたコンテンツ

DNS ASIM スキーマのサポートには、正規化された DNS パーサーを使用した次の組み込み分析規則のサポートも含まれています。 Azure Sentinel GitHub リポジトリへのリンクは以下で参照として示されていますが、これらのルールは [Azure Sentinel Analytics ルール ギャラリー](detect-threats-built-in.md)でも見つけることができます。 リンクされた GitHub ページを使用して、一覧のルールに関連するハンティング クエリをコピーします。

次の組み込みの分析規則が、正規化された DNS パーサーで動作するようになっています。
 - (プレビュー) TI のドメイン エンティティの DNS イベントへのマッピング (正規化された DNS)
 - (プレビュー) TI の IP エンティティの DNS イベントへのマッピング (正規化された DNS)
 - [潜在的 DGA の検出 (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
 - [Excessive NXDOMAIN DNS Queries (Normalized DNS) (過剰な NXDOMAIN DNS クエリ (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [DNS events related to mining pools (Normalized DNS) (マイニング プールに関連する DNS イベント (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [DNS events related to ToR proxies (Normalized DNS) (ToR プロキシに関連する DNS イベント (正規化された DNS))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Known Barium domains (既知の Barium ドメイン)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Known Barium IP addresses (既知の Barium IP アドレス)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server Vulnerabilities Disclosed March 2021 IoC Match (2021 年 3 月に公開された Exchange Server の脆弱性の IoC 一致)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [既知の GALLIUM ドメインとハッシュ](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (既知の IRIDIUM IP)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - ドメインおよび IP IOC - 2021 年 3 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [既知の Phosphorus グループ ドメイン/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Known STRONTIUM group domains - July 2019 (既知の STRONTIUM グループ ドメイン - 2019 年 7 月)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate ネットワーク ビーコン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [DCU 削除に含まれる THALLIUM ドメイン](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Known ZINC Comebacker and Klackring malware hashes (既知の ZINC Comebacker および Klackring マルウェア ハッシュ)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)


## <a name="schema-details"></a>スキーマの詳細

DNS 情報モデルは、[OSSEM DNS エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)に合わせて調整されています。

詳細については、[Internet Assigned Numbers Authority (IANA) のドメイン ネーム システム パラメーター リファレンス](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)に関するページを参照してください。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは Log Analytics によってレコードごとに生成され、[カスタム コネクタを作成する](create-custom-connector.md)ときにオーバーライドできます。

| **フィールド** | **Type** | **説明** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 日付/時刻 | イベントがレポート デバイスによって生成された日時。 |
| **\_ResourceId** | guid | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを複数のチャネルを通じて異なるテーブルで受信できるときに、同じ EventVendor 値と EventProduct 値を設定する場合に役に立ちます。<br><br>たとえば、Sysmon イベントを、イベント テーブルまたは WindowsEvent テーブルのいずれかに収集できます。 |
| | | |

> [!NOTE]
> セキュリティにあまり関係のない他の Log Analytics フィールドについては、[Azure Monitor](../azure-monitor/logs/log-standard-columns.md) に関するドキュメントで説明されています。
> 

### <a name="event-fields"></a>イベント フィールド

イベント フィールドはすべてのスキーマに共通であり、アクティビティ自体とレポート デバイスが記述されています。

| **フィールド** | **クラス** | **Type**  | **考察 (Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | 省略可能 | String | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。 |
| **EventCount** | Mandatory | Integer | レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>他のソースの場合は、**1** に設定する必要があります。 <br><br>例:`1`|
| **EventStartTime** | Mandatory | 日付/時刻 | ソースが集計に対応しており、レコードで複数のイベントが表されている場合は、このフィールドを使用して、最初のイベントが生成された日時を指定します。 <br><br>それ以外の場合は、[TimeGenerated](#timegenerated) フィールドの別名です。 |
| **EventEndTime** | エイリアス || [TimeGenerated](#timegenerated) フィールドの別名。 |
| **EventType** | Mandatory | Enumerated | レコードによって報告される操作を示します。 <br><Br> DNS レコードの場合、この値は [DNS の操作コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)になります。 <br><br>例: `lookup`|
| **EventSubType** | 省略可能 | Enumerated | **request** または **response**。 ほとんどのソースでは、[応答だけがログに記録される](#guidelines-for-collecting-dns-events)ため、多くの場合、この値は **response** になります。  |
| **EventResult** | Mandatory | Enumerated | 以下の値のいずれかです。**Success**、**Partial**、**Failure**、**NA** (該当なし)。<br> <br>ソース レコードでは、異なる用語を使用して値が指定されている場合があります。それを、これらの値に正規化する必要があります。 または、ソースで [EventResultDetails](#eventresultdetails) フィールドのみが提供されている場合があり、これを分析して EventResult の値を得る必要があります。<br> <br>このレコードが応答ではなく要求を表している場合は、**NA** に設定します。 <br><br>例: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | エイリアス | | **_EventResult_** フィールドで報告された結果の理由または詳細。 [ResponseCodeName](#responsecodename) フィールドの別名を設定します。|
| **EventOriginalUid** | 省略可能 | String | ソースによって提供されている場合、元のレコードの一意の ID。 |
| **EventOriginalType**   | 省略可能    | String  |  ソースによって提供されている場合、元のイベントの種類または ID。<br><br>例: `lookup` |
| <a name ="eventproduct"></a>**EventProduct** | Mandatory | String | イベントを生成している製品。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 <br><br>例: `DNS Server` |
| **EventProductVersion** | 省略可能 | String | イベントを生成している製品のバージョン。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 <br><br>例: `12.1` |
| **EventVendor** | Mandatory | String | イベントを生成している製品のベンダー。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。<br><br>例: `Microsoft`|
| **EventSchemaVersion** | Mandatory | String | ここに記載されているスキーマのバージョンは **0.1.2** です。 |
| **EventSchema** | Mandatory | String | ここに記載されているスキーマの名前は **Dns** です。 |
| **EventReportUrl** | 省略可能 | String | リソースのイベントで提供された、そのイベントに関する他の情報を提供する URL。 |
| <a name="dvc"></a>**Dvc** | Mandatory       | String     |    イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[Event Product](#eventproduct) フィールドと同じ値を使用します。 <br><br>例: `ContosoDc.Contoso.Azure`       |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |  プロセス イベントが発生したデバイスの IP アドレス。 <br><br>例: `45.21.42.12` |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   | プロセス イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`                |
| <a name ="dvcid"></a>**DvcId**               | 省略可能    | String     | プロセス イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | 省略可能 | 動的 | ソースから保持する必要のある他の情報が提供される場合は、元のフィールド名をそのまま使用するか、**AdditionalFields** 動的フィールドを作成し、追加情報をキーと値のペアとして追加します。 |
| | | | |

### <a name="dns-specific-fields"></a>DNS 固有のフィールド

以下のフィールドは、DNS イベントに固有のものです。 ただし、その多くは他のスキーマと似ているため、同じ名前付け規則に従います。

| **フィールド** | **クラス** | **Type** | **ノート** |
| --- | --- | --- | --- |
| **SrcIpAddr** | 推奨 | IP アドレス | DNS 要求を送信しているクライアントの IP アドレス。 再帰的な DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、`127.0.0.1` に設定されます。<br><br>例: `192.168.12.1` |
| **SrcPortNumber** | 省略可能 | Integer | DNS クエリの送信元ポート。<br><br>例: `54312` |
| **DstIpAddr** | 省略可能 | IP アドレス | DNS 要求を受信しているサーバーの IP アドレス。 通常の DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、`127.0.0.1` に設定されます。<br><br>例: `127.0.0.1` |
| **DstPortNumber** | 省略可能 | Integer  | 送信先ポート番号。<br><br>例: `53` |
| **IpAddr** | エイリアス | | SrcIpAddr の別名 |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | 解決する必要があるドメイン。 <br><br>**注**: ソースによっては、このクエリが異なる形式で送信されます。 たとえば、DNS プロトコル自体では、このクエリは末尾にドット ( **.** ) が含まれているため、これを削除する必要があります。<br><br>DNS プロトコルでは 1 つの要求に複数のクエリを含めることができますが、このようなシナリオは行われるとしてもまれです。 要求に複数のクエリがある場合は、最初のものをこのフィールドに格納し、必要に応じて残りを [AdditionalFields](#additionalfields) フィールドに保持します。<br><br>例: `www.malicious.com` |
| **Domain** | エイリアス | | [Query](#query) の別名。 |
| **DnsQueryType** | 省略可能 | Integer | このフィールドには、[DNS のリソース レコードの種類コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)が含まれる場合があります。 <br><br>例: `28`|
| **DnsQueryTypeName** | 推奨 | Enumerated | このフィールドには、[DNS のリソース レコードの種類](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)の名前が含まれる場合があります。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では必要に応じて大文字と小文字を正規化する必要があります。 ソースで数値のクエリの種類コードのみが提供され、クエリの種類の名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。<br><br>例: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | 省略可能 | String | レコードに含まれる応答の内容。<br> <br> DNS 応答データは、レポート デバイスの間で一貫性がなく、解析が複雑であり、ソースに依存しない分析にとっての価値は高くありません。 したがって、情報モデルでの解析と正規化は必要なく、Azure Sentinel では補助関数を使用して応答情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | [DNS 応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では大文字と小文字を正規化する必要があります。 ソースで数値の応答コードのみが提供され、応答コードの名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。 <br><br> このレコードが応答ではなく要求を表している場合は、**NA** に設定します。 <br><br>例: `NXDOMAIN` |
| **DnsResponseCode** | 省略可能 | Integer | [DNS 数値応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>例: `3`|
| **TransactionIdHex** | 推奨 | String | DNS の一意の 16 進トランザクション ID。 |
| **NetworkProtocol** | 省略可能 | Enumerated | ネットワーク解決イベントによって使用されるトランスポート プロトコル。 値は **UDP** または **TCP** であり、DNS の場合は **UDP** に設定されるのが普通です。 <br><br>例: `UDP`|
| **DnsQueryClass** | 省略可能 | Integer | [DNS クラス ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。|
| **DnsQueryClassName** | 省略可能 | String | [DNS クラスの名前](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。 <br><br>例: `IN`|
| <a name=flags></a>**DnsFlags** | 省略可能 | 文字列のリスト | レポート デバイスによって提供されるフラグ フィールド。 フラグ情報が複数のフィールドで提供されている場合は、区切り記号としてコンマを使用して連結します。 <br><br>DNS フラグは解析が複雑で、分析ではあまり使用されないので、解析と正規化は必要なく、Azure Sentinel では補助関数を使用してフラグ情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。 <br><br>例: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  省略可能 | String | DNS イベント ソースで、要求されたドメインのカテゴリが検索されることもあります。 フィールドは、Azure Sentinel のネットワーク スキーマに合わせて **_UrlCategory_** と呼ばれます。 <br><br>**_DomainCategory_** は、DNS に適合する別名として追加されます。 <br><br>例: `Educational \\ Phishing` |
| **DomainCategory** | 省略可能 | エイリアス | [UrlCategory](#UrlCategory) の別名。 |
| **ThreatCategory** | 省略可能 | String | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントも評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、脅威カテゴリでドメインまたは IP アドレスに割り当られる場合があります。 |
| **EventSeverity** | 省略可能 | String | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントが評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、評価に基づいて重大度が割り当られる場合があります。 <br><br>例: `Informational`|
| **DvcAction** | 省略可能 | String | DNS イベント ソースで DNS セキュリティも提供される場合は、要求に対してブロックといったアクションが実行される可能性があります。 <br><br>例: `Blocked` |
| **DnsFlagsAuthenticated** | 省略可能 | ブール型 | DNSSEC に関連する DNS の `AD` フラグは、応答において、応答の回答セクションと権限セクションに含まれるすべてのデータが、サーバーのポリシーに従ってそのサーバーによって検証されたことを示します。 詳細については、[RFC 3655 セクション 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) を参照してください。    |
| **DnsFlagsAuthoritative** | 省略可能 | ブール型 | DNS の `AA` フラグは、サーバーからの応答に権限があったかどうかを示します    |
| **DnsFlagsCheckingDisabled** | 省略可能 | ブール型 | DNSSEC に関連する DNS の `CD` フラグは、クエリにおいて、検証されていないデータがクエリを送信しているシステムで許容されることを示します。 詳細については、[RFC 3655 セクション 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) を参照してください。   |
| **DnsFlagsRecursionAvailable** | 省略可能 | ブール型 | DNS の `RA` フラグは、応答において、サーバーが再帰クエリをサポートすることを示します。   |
| **DnsFlagsRecursionDesired** | 省略可能 | ブール型 | DNS の `RD` フラグは、要求において、サーバーが再帰クエリを使用するのをクライアントが望むことを示します。   |
| **DnsFlagsTruncates** | 省略可能 | ブール型 | DNS の `TC` フラグは、最大応答サイズを超えたために応答が切り詰められたことを示します。  |
| **DnsFlagsZ** | 省略可能 | ブール型 | DNS の `Z` フラグは非推奨の DNS フラグであり、古い DNS システムによって報告される可能性があります。  |
| | | | |

### <a name="deprecated-aliases"></a>非推奨の別名

次のフィールドは、現在は非推奨ですが、下位互換性のために保持されている別名です。 これらは、2021 年 12 月 31 日にスキーマから削除されます。

- Query (DnsQuery の別名)
- QueryType (DnsQueryType の別名)
- QueryTypeName (DnsQueryTypeName の別名)
- ResponseName (DnsReasponseName の別名)
- ResponseCodeName (DnsResponseCodeName の別名)
- ResponseCode (DnsResponseCode の別名)
- QueryClass (DnsQueryClass の別名)
- QueryClassName (DnsQueryClassName の別名)
- Flags (DnsFlags の別名)

### <a name="added-fields"></a>追加されたフィールド

スキーマのバージョン 0.1.2 には、次のフィールドが追加されています。
- EventSchema - 現在は省略可能ですが、2022 年 1 月 1 日に必須になります。
- 結合された **[フラグ](#flags)** フィールドを拡張する専用フラグ フィールド: `DnsFlagsAuthoritative`、`DnsFlagsCheckingDisabled`、`DnsFlagsRecursionAvailable`、`DnsFlagsRecursionDesired`、`DnsFlagsTruncates`、`DnsFlagsZ`。

### <a name="additional-entities"></a>その他のエンティティ

イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に進化します。各エンティティでは、それを記述するために複数のフィールドが必要になる場合があります。 例:

- ホストは、名前と IP アドレスの両方を持つことができます。
- 1 つのレコードに、ソース ホストと宛先ホストの両方など、同じ種類の複数のエンティティが含まれる場合があります

この記事で説明したように、DNS スキーマにはエンティティを記述するフィールドが含まれています。 ソースにエンティティを記述する他の情報が含まれる場合は、次の表に示すエンティティに基づいて、この情報をキャプチャするためのフィールドを追加します。

詳細については、[Azure Sentinel の正規化](normalization.md)に関するページを参照してください。


| **エンティティ** | **Fields** | **Type** | **必須フィールド** | **ノート** |
| --- | --- | --- | --- | --- |
| **俳優** | Actor\* | User |  | ほとんどの DNS イベント ソースでは、ユーザーの情報は提供されません。これは、通常、DNS プロトコルの一部ではありません。 <br><br>場合によっては、通常はソース IP アドレスをユーザー情報に解決することで、レポート デバイスからユーザー情報が提供されます。 そのような場合は、スキーマ エンティティのドキュメントで説明されているようにユーザーを表します。 情報はソース IP アドレスに基づくので、記述子として **Actor** を使用します。 <br><br>**注**: エンティティ記述子として **Actor** を使用するときは、**User** フィールドを追加する必要はありません。 |
| **ソース デバイス** | Src\* | Device | `SrcIpAddr` | 通常は、DNS イベント ソースによって要求のソースの IP アドレスが報告されるため、**SrcIpAddr** は必須です。 <br><br>レポート デバイスにより要求のソースに関する情報がさらに提供される場合、またはデータをエンリッチする場合は、デバイス エンティティのガイドラインに従い、フィールドのプレフィックスとして **Src** を使用して正規化します。 |
| **宛先デバイス** | Dst\* | Device |  | DNS イベント ソースからは、通常、要求の宛先に関する情報は報告されません。 レポート デバイスにより要求の宛先に関する情報が提供される場合、またはデータをエンリッチする場合は、デバイス エンティティのガイドラインに従い、プレフィックスとして **Dst** を使用して正規化します。 |
| **レポート デバイス** | Dvc\* | Device | `Dvc` | ほとんどのイベントには、レポート デバイスに関する情報が含まれます。 それらのフィールドにはプレフィックス Dvc を使用します|
| **プロセス情報** | プロセス\* | Process |  | クライアント デバイスで DNS クエリを生成したプロセスに関連する情報。 |
| | | | | |

## <a name="handling-dns-response"></a>DNS の応答の処理

ほとんどの場合、ログに記録された DNS イベントには応答情報は含まれません。応答情報は大きく、詳細である可能性があります。 レコードにさらに多くの応答情報が含まれる場合は、レコードのとおりに [ResponseName](#responsename) フィールドに格納します。

また、`_imDNS<vendor>Response_` という名前の追加の KQL 関数を提供することもできます。この関数は、解析されていない応答を入力として受け取り、次の構造体で動的な値を返します。

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

動的な値の各ディクショナリのフィールドは、各 DNS 応答のフィールドに対応します。 `part` エントリには、ディクショナリが属する応答の部分を反映する `answer`、`authority`、または `additional` が含まれる必要があります。

> [!TIP]
> パフォーマンスを最適にするには、必要な場合にだけ、最初のフィルター処理の後でのみ `imDNS<vendor>Response` 関数を呼び出して、パフォーマンスが向上するようにします。
>

## <a name="handling-dns-flags"></a>DNS のフラグの処理

フラグ データを解析および正規化する必要はありません。 代わりに、レポート デバイスによって提供されたフラグ データを [Flags](#flags) フィールドに格納します。 個々のフラグの値を簡単に決定できる場合は、専用のフラグ フィールドを使用することもできます。 

また、`_imDNS<vendor>Flags_` という名前の追加の KQL 関数を提供することもできます。この関数は、解析されていない応答または専用フラグ フィールドを入力として受け取り、次の順序で各フラグを表すブール値が含まれる動的なリストを返します。

- 認証済み (AD)
- 権限 (AA)
- 無効の確認 (CD)
- 再帰が利用可能 (RA)
- 再帰が望ましい (RD)
- 切り詰め (TC)
- Z

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Azure Sentinel での正規化](normalization.md)
- [Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Azure Sentinel データ正規化スキーマ リファレンス](normalization-schema.md)
- [Azure Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Azure Sentinel プロセス イベント正規化スキーマ リファレンス](process-events-normalization-schema.md)
- [Azure Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)
