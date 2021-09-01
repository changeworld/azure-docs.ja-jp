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
ms.openlocfilehash: d1196f634143b2526240fe36775d7c0b272a7e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726068"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Azure Sentinel の DNS 正規化スキーマ リファレンス (パブリック プレビュー)

DNS 情報モデルは、DNS サーバーまたは DNS セキュリティ システムによって報告されるイベントを記述するために使用され、ソースに依存しない分析を有効にするために Azure Sentinel によって使用されます。

詳細については、「[正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> DNS 正規化スキーマは、現在パブリック プレビュー中です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

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

DNS 情報モデルが実装されている KQL 関数の名前は次のとおりです。

| 名前 | 説明 | 使用手順 |
| --- | --- | --- |
| **imDNS** | "*和集合*" を使用して、すべての DNS ソースからの正規化されたイベントを含める集約パーサー。 |- ソースに依存しない分析でソースを追加または削除する場合は、このパーサーを更新します。 <br><br>- ソースに依存しないクエリでこの関数を使用します。|
| **imDNS\<vendor\>\<product\>** | ソース固有のパーサーでは、*imDNSWindowsOMS* などの特定のソース用の正規化が実装されています。 |- 組み込みの正規化パーサーがない場合は、ソースにソース固有のパーサーを追加します。 新しいパーサーへの参照を含むように、集約パーサーを更新します。 <br><br>- 解析と正規化の問題を解決するには、ソース固有のパーサーを更新します。<br><br>- ソース固有の分析には、ソース固有のパーサーを使用します。|
| | | |

パーサーは、[Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)からデプロイできます。

## <a name="normalized-content"></a>正規化されたコンテンツ

次の組み込みの分析規則が、正規化された DNS パーサーで動作するようになっています。
- 追加: 
  - Excessive NXDOMAIN DNS Queries (Normalized DNS) (過剰な NXDOMAIN DNS クエリ (正規化された DNS))
  - DNS events related to mining pools (Normalized DNS) (マイニング プールに関連する DNS イベント (正規化された DNS))
  - DNS events related to ToR proxies (Normalized DNS) (ToR プロキシに関連する DNS イベント (正規化された DNS))
- 正規化された DNS を含むように更新: 
  - Known Barium domains (既知の Barium ドメイン)
  - Known Barium IP addresses (既知の Barium IP アドレス)  
  - Exchange Server Vulnerabilities Disclosed March 2021 IoC Match (2021 年 3 月に公開された Exchange Server の脆弱性の IoC 一致)
  - 既知の GALLIUM ドメインとハッシュ
  - Known IRIDIUM IP (既知の IRIDIUM IP)
  - NOBELIUM - ドメインおよび IP IOC - 2021 年 3 月
  - 既知の Phosphorus グループ ドメイン/IP
  - Known STRONTIUM group domains - July 2019 (既知の STRONTIUM グループ ドメイン - 2019 年 7 月)
  - Solorigate ネットワーク ビーコン
  - DCU 削除に含まれる THALLIUM ドメイン
  - Known ZINC Comebacker and Klackring malware hashes (既知の ZINC Comebacker および Klackring マルウェア ハッシュ)


## <a name="schema-details"></a>スキーマの詳細

DNS 情報モデルは、[OSSEM DNS エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)に合わせて調整されています。

詳細については、[Internet Assigned Numbers Authority (IANA) のドメイン ネーム システム パラメーター リファレンス](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)に関するページを参照してください。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは Log Analytics によってレコードごとに生成され、[カスタム コネクタを作成する](create-custom-connector.md)ときにオーバーライドできます。

| **フィールド** | **Type** | **説明** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 日付/時刻 | イベントがレポート デバイスによって生成された日時。 |
| **\_ResourceId** | guid | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| | | |

> [!NOTE]
> セキュリティにあまり関係のない他の Log Analytics フィールドについては、[Azure Monitor](../azure-monitor/logs/log-standard-columns.md) に関するドキュメントで説明されています。
> 

### <a name="event-fields"></a>イベント フィールド

イベント フィールドはすべてのスキーマに共通であり、アクティビティ自体とレポート デバイスが記述されています。

| **フィールド** | **クラス** | **種類** | **例** | **考察 (Discussion)** |
| --- | --- | --- | --- | --- |
| **EventMessage** | 省略可能 | String | | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。 |
| **EventCount** | Mandatory | 整数型 | `1` | レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>他のソースの場合は、**1** に設定する必要があります。 |
| **EventStartTime** | Mandatory | 日付/時刻 | | ソースが集計に対応しており、レコードで複数のイベントが表されている場合は、このフィールドを使用して、最初のイベントが生成された日時を指定します。 <br><br>それ以外の場合は、[TimeGenerated](#timegenerated) フィールドの別名です。 |
| **EventEndTime** | | エイリアス || [TimeGenerated](#timegenerated) フィールドの別名。 |
| **EventType** | Mandatory | Enumerated | `lookup` | レコードによって報告される操作を示します。 <br><Br> DNS レコードの場合、この値は [DNS の操作コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)になります。 |
| **EventSubType** | 省略可能 | Enumerated || **request** または **response**。 ほとんどのソースでは、[応答だけがログに記録される](#guidelines-for-collecting-dns-events)ため、多くの場合、この値は **response** になります。  |
| **EventResult** | Mandatory | Enumerated | `Success` | 以下の値のいずれかです。**Success**、**Partial**、**Failure**、**NA** (該当なし)。<br> <br>ソース レコードでは、異なる用語を使用して値が指定されている場合があります。それを、これらの値に正規化する必要があります。 または、ソースで [EventResultDetails](#eventresultdetails) フィールドのみが提供されている場合があり、これを分析して EventResult の値を得る必要があります。<br> <br>このレコードが応答ではなく要求を表している場合は、**NA** に設定します。 |
| <a name=eventresultdetails></a>**EventResultDetails** | Mandatory | エイリアス | `NXDOMAIN` | **_EventResult_** フィールドで報告された結果の理由または詳細。 [ResponseCodeName](#responsecodename) フィールドの別名を設定します。|
| **EventOriginalUid** | 省略可能 | String | | ソースによって提供されている場合、元のレコードの一意の ID。 |
| **EventOriginalType**   | 省略可能    | String  | `lookup` |   ソースによって提供されている場合、元のイベントの種類または ID。 |
| <a name ="eventproduct"></a>**EventProduct** | Mandatory | String | `DNS Server` | イベントを生成している製品。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 |
| **EventProductVersion** | 省略可能 | String | `12.1` | イベントを生成している製品のバージョン。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 |
| **EventVendor** | Mandatory | String | `Microsoft` | イベントを生成している製品のベンダー。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 |
| **EventSchemaVersion** | Mandatory | String | `0.1.1` | ここに記載されているスキーマのバージョンは **0.1.1** です。 |
| **EventReportUrl** | 省略可能 | String | | リソースのイベントで提供された、そのイベントに関する他の情報を提供する URL。 |
| <a name="dvc"></a>**Dvc** | Mandatory       | String     |    `ContosoDc.Contoso.Azure` |           イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[Event Product](#eventproduct) フィールドと同じ値を使用します。         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |  `45.21.42.12` |       プロセス イベントが発生したデバイスの IP アドレス。  |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   | `ContosoDc.Contoso.Azure` |              プロセス イベントが発生したデバイスのホスト名。                |
| <a name ="dvcid"></a>**DvcId**               | 省略可能    | String     || プロセス イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | 省略可能 | 動的 | | ソースから保持する必要のある他の情報が提供される場合は、元のフィールド名をそのまま使用するか、**AdditionalFields** 動的フィールドを作成し、追加情報をキーと値のペアとして追加します。 |
| | | | | |

### <a name="dns-specific-fields"></a>DNS 固有のフィールド

以下のフィールドは、DNS イベントに固有のものです。 ただし、その多くは他のスキーマと似ているため、同じ名前付け規則に従います。

| **フィールド** | **クラス** | **種類** | **例** | **ノート** |
| --- | --- | --- | --- | --- |
| **SrcIpAddr** | Mandatory | IP アドレス |  `192.168.12.1 `| DNS 要求を送信しているクライアントの IP アドレス。 再帰的な DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、**127.0.0.1** に設定されます。 |
| **SrcPortNumber** | 省略可能 | Integer |  `54312` | DNS クエリの送信元ポート。 |
| **DstIpAddr** | 省略可能 | IP アドレス |  `127.0.0.1` | DNS 要求を受信しているサーバーの IP アドレス。 通常の DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、**127.0.0.1** に設定されます。 |
| **DstPortNumber** | 省略可能 | Integer |  `53` | 送信先ポート番号 |
| **IpAddr** | | エイリアス | | SrcIpAddr の別名 |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | `www.malicious.com` | 解決する必要があるドメイン。 <br><br>異なる形式でクエリを送信する送信元があることに注意してください。 特に、DNS プロトコル自体では、クエリの末尾にドットが含まれています。 これは除去する必要があります。<br><br>DNS プロトコルでは 1 つの要求に複数のクエリを含めることができますが、このようなシナリオは行われるとしてもまれです。 要求に複数のクエリがある場合は、最初のものをこのフィールドに格納し、必要に応じて残りを [AdditionalFields](#additionalfields) フィールドに保持します。 |
| **Domain** | | エイリアス || [Query](#query) の別名。 |
| **DnsQueryType** | 省略可能 | Integer | `28` | このフィールドには、[DNS のリソース レコードの種類コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)が含まれる場合があります。 |
| **DnsQueryTypeName** | Mandatory | Enumerated | `AAAA` | このフィールドには、[DNS のリソース レコードの種類](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)の名前が含まれる場合があります。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では必要に応じて大文字と小文字を正規化する必要があります。 ソースで数値のクエリの種類コードのみが提供され、クエリの種類の名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。 |
| <a name=responsename></a>**DnsResponseName** | 省略可能 | String | | レコードに含まれる応答の内容。<br> <br> DNS 応答データは、レポート デバイスの間で一貫性がなく、解析が複雑であり、ソースに依存しない分析にとっての価値は高くありません。 したがって、情報モデルでの解析と正規化は必要なく、Azure Sentinel では補助関数を使用して応答情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | `NXDOMAIN` | [DNS 応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では大文字と小文字を正規化する必要があります。 ソースで数値の応答コードのみが提供され、応答コードの名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。 <br><br> このレコードが応答ではなく要求を表している場合は、**NA** に設定します。 |
| **DnsResponseCode** | 省略可能 | Integer | `3` | [DNS 数値応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。|
| **TransactionIdHex** | 推奨 | String | | DNS の一意の 16 進トランザクション ID。 |
| **NetworkProtocol** | 省略可能 | Enumerated | `UDP` | ネットワーク解決イベントによって使用されるトランスポート プロトコル。 値は **UDP** または **TCP** であり、DNS の場合は **UDP** に設定されるのが普通です。 |
| **DnsQueryClass** | 省略可能 | Integer | | [DNS クラス ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。|
| **DnsQueryClassName** | 省略可能 | String | `"IN"` | [DNS クラスの名前](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。 |
| <a name=flags></a>**DnsFlags** | 省略可能 | 文字列のリスト | `["DR"]` | レポート デバイスによって提供されるフラグ フィールド。 フラグ情報が複数のフィールドで提供されている場合は、区切り記号としてコンマを使用して連結します。 <br><br>DNS フラグは解析が複雑で、分析ではあまり使用されないので、解析と正規化は必要なく、Azure Sentinel では補助関数を使用してフラグ情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。|
| <a name=UrlCategory></a>**UrlCategory** |   | String | `Educational \\ Phishing` | DNS イベント ソースで、要求されたドメインのカテゴリが検索されることもあります。 フィールドは、Azure Sentinel のネットワーク スキーマに合わせて **_UrlCategory_** と呼ばれます。 <br><br>**_DomainCategory_** は、DNS に適合する別名として追加されます。 |
| **DomainCategory** | | エイリアス | | [UrlCategory](#UrlCategory) の別名。 |
| **ThreatCategory** |   | String |   | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントも評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、脅威カテゴリでドメインまたは IP アドレスに割り当られる場合があります。 |
| **EventSeverity** | 省略可能 | String | `"Informational"` | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントが評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、評価に基づいて重大度が割り当られる場合があります。 |
| **DvcAction** | 省略可能 | String | `"Blocked"` | DNS イベント ソースで DNS セキュリティも提供される場合は、要求に対してブロックといったアクションが実行される可能性があります。 |
| | | | | |

### <a name="additional-aliases-deprecated"></a>追加の別名 (非推奨)

次のフィールドは、下位互換性のために残されている別名です。
- Query (DnsQuery の別名)
- QueryType (DnsQueryType の別名)
- QueryTypeName (DnsQueryTypeName の別名)
- ResponseName (DnsReasponseName の別名)
- ResponseCodeName (DnsResponseCodeName の別名)
- ResponseCode (DnsResponseCode の別名)
- QueryClass (DnsQueryClass の別名)
- QueryClassName (DnsQueryClassName の別名)
- Flags (DnsFlags の別名)

### <a name="additional-entities"></a>その他のエンティティ

イベントは、ユーザー、ホスト、プロセス、ファイルなどのエンティティを中心に発生します。 各エンティティを記述するには、複数のフィールドが必要になる場合があります。 たとえば、ホストは名前と IP アドレスを持つことがあります。 さらに、1 つのレコードに同じ種類の複数のエンティティが含まれる場合があります (ソース ホストと宛先ホストなど)。 上で説明したように、DNS スキーマにはエンティティを記述するフィールドが含まれています。 ソースにそれらのエンティティを記述する他の情報が含まれる場合は、以下のエンティティに基づいて、この情報をキャプチャするためのフィールドを追加します。 エンティティの詳細については、[Azure Sentinel での正規化](normalization.md)に関する記事を参照してください。

| **エンティティ** | **Fields** | **種類** | **必須フィールド** | **ノート** |
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

フラグ データを解析および正規化する必要はありません。 代わりに、レポート デバイスによって提供されたフラグ データを [Flags](#flags) フィールドに格納します。

また、`_imDNS<vendor>Flags_` という名前の追加の KQL 関数を提供することもできます。この関数は、解析されていない応答を入力として受け取り、次の順序で各フラグを表すブール値が含まれる動的なリストを返します。

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
