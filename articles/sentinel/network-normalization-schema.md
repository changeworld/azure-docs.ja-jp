---
title: Microsoft Sentinel ネットワーク セッション正規化スキーマ リファレンス (パブリック プレビュー) | Microsoft Docs
description: この記事では、Microsoft Sentinel ネットワーク セッション正規化スキーマを示します。
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: bf13dee6c6a33ae16440357013a34dc36ef93826
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712910"
---
# <a name="microsoft-sentinel-network-session-normalization-schema-reference-public-preview"></a>Microsoft Sentinel ネットワーク セッション正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

ネットワーク セッション正規化スキーマは、IP ネットワークのアクティビティを記述するために使用されます。 これには、ネットワーク接続とネットワーク セッションが含まれます。 そのようなイベントは、オペレーティング システム、ルーター、ファイアウォール、侵入防止システム、Web セキュリティ ゲートウェイなどによって報告されます。

Microsoft Sentinel での正規化の詳細については、「[正規化と Advanced SIEM Information Model (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> この記事では、ネットワーク正規化スキーマのバージョン 0.2 について説明します。[バージョン 0.1](normalization-schema-v1.md) は、ASIM を利用できるようになる前にリリースされたため、ASIM と整合していない部分がいくつかあります。 詳細については、[ネットワーク正規化スキーマのバージョン間の相違点](normalization-schema-v1.md#changes)に関するページを参照してください。 
>

> [!IMPORTANT]
> 現在、ネットワーク正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="support-for-common-network-telemetry-sources"></a>一般的なネットワーク テレメトリ ソースのサポート

ネットワーク正規化スキーマは任意の IP ネットワーク セッションを表すことができますが、特に次のような一般的なソースの種類をサポートするように設計されています。

- Netflow
- ファイアウォール
- 侵入防止システム
- Web サーバー
- Web セキュリティ ゲートウェイ

以下のセクションでは、さまざまなソースの種類に対するスキーマの正規化と使用に関するガイダンスを示します。 各ソースの種類では次のことが可能です。
- 補助フィールド リストからの追加フィールドをサポートします: [中間デバイス フィールド](#Intermediary)、[HTTP セッション フィールド](#http-session-fields)、[検査フィールド](#inspection-fields)。 一部のフィールドは、特定のソースの種類のコンテキストでのみ必須です。
- `EventType` や `EventResult` などの一般的なイベント フィールドに対して、ソースの種類に固有の値を許可します。
- `imNetworkSession` パーサーだけでなく、`imWebSession` パーサーと `inNetworkNotable` パーサーのいずれかまたは両方もサポートします。

### <a name="netflow-log-sources"></a>Netflow ログ ソース

| タスク | 説明|
| --- | --- |
| **Netflow イベントを正規化する** | Netflow イベントを正規化するには、それらを[ネットワーク セッション フィールド](#network-session-fields)にマップします。 Netflow テレメトリでは集計がサポートされており、`EventCount` フィールドの値はこれを反映して Netflow の *Flows* の値に設定される必要があります。 |
| **Netflow イベントを使用する** | Netflow イベントは、ソースに依存しない [imNetworkSession](#use-parsers) パーサーの一部として公開されます。 集約クエリを作成するときは、`EventCount` フィールドの値を考慮してください。これは、常に `1` に設定されるとは限りません。 |
| | |

### <a name="firewall-log-sources"></a>ファイアウォール ログ ソース

| タスク | 説明 |
| --- | --- |
| **ファイアウォール イベントを正規化する** | ファイアウォールからのイベントを正規化するには、関連するイベントを、[イベント](#event-fields)、[ネットワーク セッション](#network-session-fields)、[セッション検査](#inspection-fields)の各フィールドにマップします。 それらのイベントをフィルター処理し、ソースに依存しない [inNetworkNotables](#use-parsers) パーサーに追加します。 |
| **ファイアウォール イベントを使用する** | ファイアウォール イベントは、ソースに依存しない [imNetworkSession](#use-parsers) パーサーの一部として公開されます。 ファイアウォール検査エンジンによって識別される関連イベントも、ソースに依存しない [inNetworkNotables](#use-parsers) パーサーの一部として公開されます。 |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>侵入防止システム (IPS) ログ ソース

| タスク | 説明 |
| --- | --- |
| **IPS イベントを正規化する** | 侵入防止システムからのイベントを正規化するには、[イベント フィールド](#event-fields)、[ネットワーク セッション フィールド](#network-session-fields)、[セッション検査フィールド](#inspection-fields)をマップします。 ソース固有のパーサーが、ソースに依存しない [imNetworkSession](#use-parsers) パーサーと [inNetworkNotables](#use-parsers) パーサーの両方に含まれていることを確認します。 |
| **IPS イベントを使用する** | IPS イベントは、ソースに依存しない [imNetworkSession](#use-parsers) および [inNetworkNotables](#use-parsers) パーサーの一部として公開されます。 |
| | |

### <a name="web-servers"></a>Web サーバー

| タスク | 説明 |
| --- | --- |
| **Web サーバー イベントを正規化する** | Web サーバーからのイベントを正規化するには、[イベント フィールド](#event-fields)、[ネットワーク セッション フィールド](#network-session-fields)、[HTTP セッション フィールド](#http-session-fields)をマップします。 `EventType` の値を `HTTP Session` に設定し、`EventResult` および `EventResultDetails` フィールドに関する HTTP セッション固有のガイダンスに従います。 <br><br>ソース固有のパーサーが、ソースに依存しない [imNetworkSession](#use-parsers) パーサーと [imWebSession](#use-parsers) パーサーの両方に含まれていることを確認します。 |
| **Web サーバー イベントを使用する** | Web サーバー イベントは、ソースに依存しない [imNetworkSession](#use-parsers) パーサーの一部として公開されます。 ただし、HTTP 固有のフィールドを使用するには、[imWebSession](#use-parsers) パーサーを使用します。 |
| | |

### <a name="web-security-gateways"></a>Web セキュリティ ゲートウェイ

| タスク | 説明 |
| --- | --- |
| **Web セキュリティ ゲートウェイ イベントを正規化する** | Web サーバー ゲートウェイからのイベントを正規化するには、[イベント フィールド](#event-fields)、[ネットワーク セッション フィールド](#network-session-fields)、[HTTP セッション フィールド](#http-session-fields)、[セッション検査フィールド](#inspection-fields)、および必要に応じて中間フィールドをマップします。 <br><br>`EventType` を `HTTP` に設定し、`EventResult` および `EventResultDetails` フィールドに関する HTTP セッション固有のガイダンスに従います。 <br><br>ソース固有のパーサーが、ソースに依存しない [imNetworkSession](#use-parsers) パーサーと [imWebSession](#use-parsers) パーサーの両方に含まれていることを確認します。 また、検査エンジンによって検出されたすべてのイベントをフィルター処理し、ソースに依存しない [inNetworkNotables](#use-parsers) パーサーにそれらを追加します。 |
| **Web セキュリティ ゲートウェイ イベントを使用する** | Web サーバー イベントは、ソースに依存しない [imNetworkSession](#use-parsers) パーサーの一部として公開されます。 <br><br>- HTTP 固有のフィールドを使用するには、[imWebSession](#use-parsers) パーサーを使用します。<br>- 検出されたセッションを分析するには、ソースに依存しない [inNetworkNotables](#use-parsers) パーサーを使用します。 |
| | |


## <a name="use-parsers"></a>パーサーを使用する

組み込みのすべてのパーサーを 1 つにまとめる、ソースに依存しないパーサーを使用して、構成されているすべてのソースで分析が確実に実行されるようにするには、次のいずれかのパーサーを使用します。


- **imNetworkSession** は、すべてのネットワーク セッションの場合
- **imWebSession** は、通常であれば Web サーバー、Web プロキシ、Web セキュリティ ゲートウェイによって報告される、HTTP セッションの場合
- **inNetworkNotables** は、通常であれば疑わしいものとして、検出エンジンによって検出されるセッションの場合。 注目すべきイベントは、通常、侵入防止システム、ファイアウォール、Web セキュリティ ゲートウェイによって報告されます。

[Microsoft Sentinel の GitHub リポジトリ](https://aka.ms/AzSentinelNetworkSession)から、[ソースに依存しないパーサーとソース固有のパーサー](normalization-about-parsers.md)をデプロイします。

### <a name="built-in-source-specific-parsers"></a>組み込みのソース固有パーサー

Microsoft Sentinel には、以下に示す組み込みの製品固有のネットワーク セッション パーサーが用意されています。

- ソース固有のパーサー:
  - **Microsoft 365 Defender for Endpoint** - vimNetworkSessionMicrosoft365Defender
  - **Microsoft Defender for IoT - エンドポイント (MD4IoT)** - vimNetworkSessionMD4IoT
  - **Microsoft Sysmon for Linux** - vimNetworkSessionSysmonLinux
  - **Windows イベント ファイアウォール** - Windows Events 515x と Log Analytics エージェントまたは Azure Monitor エージェントを使用して、Event または WindowsEvent テーブルの vimNetworkSessionMicrosoftWindowsEventFirewall に収集された Windows ファイアウォール アクティビティ 

パーサーは、[Microsoft Sentinel GitHub リポジトリ](https://aka.ms/AzSentinelNetworkSession)からデプロイできます。

### <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する

ネットワーク セッション情報モデル用のカスタム パーサーを実装するときは、`imNetworkSession<vendor><Product>` という構文を使用して KQL 関数に名前を付けます。 この関数で、ソースに関連するすべてのフィールドをマップする必要があります。

必要な場合は、ログ ソースに応じて、関連するソースに依存しないパーサーに KQL 関数を追加します。 詳細については、次を参照してください。

- [Netflow ログ ソース](#netflow-log-sources)
- [ファイアウォール ログ ソース](#firewall-log-sources)
- [侵入防止システム (IPS) ログ ソース](#intrusion-prevention-systems-ips-log-sources)
- [Web サーバー](#web-servers)
- [Web セキュリティ ゲートウェイ](#web-security-gateways)

## <a name="schema-details"></a>スキーマの詳細

ネットワーク セッション情報モデルは、[OSSEM ネットワーク エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md)に対応しています。

業界のベスト プラクティスに準拠するため、ネットワーク セッション スキーマでは、記述子 **Src** と **Dst** を使用して、ネットワークセッションのソースとターゲットのデバイスが識別されます。フィールド名にトークン **Dvc** は含まれません。

そのため、たとえば、ソース デバイスのホスト名と IP アドレスは、それぞれ **SrcHostname** と **SrcIpAddr** という名前であり、**Src *Dvc* hostname** および **Src *Dvc* IpAddr** ではありません。 プレフィックス **Dvc** は、レポートまたは中間デバイスに対してのみ使用されます (該当する場合)。

ソース デバイスとターゲット デバイスに関連付けられているユーザーとアプリケーションを記述するフィールドでも、**Src** と **Dst** の記述子が使用されます。

他の ASIM スキーマでは、通常、**Dst** ではなく **Target** が使用されます。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは、レコードごとに Log Analytics によって生成され、[カスタム コネクタを作成する](create-custom-connector.md)ときにオーバーライドできます。


| フィールド | 型 | 考察 (Discussion) |
|-------|------|------------|
| <a name="timegenerated"></a>**TimeGenerated** | DATETIME | イベントがレポート デバイスによって生成された時刻。 |
| **\_ResourceId** | guid | レポート デバイスまたはサービスの Azure リソース ID、または Syslog、CEF、または WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、複数のチャネルを通して同じイベントを異なるテーブルに受信できるが、`EventVendor` と `EventProduct` の値が同じである場合に便利です。 <br><br>たとえば、Sysmon イベントを、**Event** テーブルまたは **SecurityEvent** テーブルのいずれかに収集できます。 |
| | | |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>

### <a name="event-fields"></a>イベント フィールド

イベント フィールドは、すべてのスキーマに共通であり、アクティビティ自体とレポート デバイスを記述します。

| フィールド | クラス | 型 | 説明 |
|-------|-------|------|-------------|
| **EventMessage** | オプション | String | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。 |
| **EventCount** | Mandatory | Integer | レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>**注**: Netflow ソースでは集計がサポートされており、**EventCount** フィールドには Netflow の **FLOWS** フィールドの値が設定されている必要があります。 他のソースの場合は、通常、この値は `1` に設定されます。 |
| **EventStartTime** | Mandatory | 日付/時刻 | ソースで集計がサポートされていて、レコードで複数のイベントが表されている場合、このフィールドにより最初のイベントが生成された日時が指定されます。 それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。 |
| **EventEndTime** | Mandatory | エイリアス | [TimeGenerated](#timegenerated) フィールドの別名。 |
| **EventType** | Mandatory | Enumerated | レコードによってレポートされる操作を記述します。<br><br> ネットワーク セッション レコードの場合、次のような値がサポートされます。<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | オプション | String | イベントの種類の追加説明 (該当する場合)。 <br> ネットワーク セッション レコードの場合、次のような値がサポートされます。<br>- `Start`<br>- `End` |
| **EventResult** | Mandatory | Enumerated | イベントの結果を記述し、次の値の 1 つに正規化されます。 <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA` (該当なし) <br><br>HTTP セッションの場合、`Success` は `400` より小さい状態コードとして定義され、`Failure` は `400` より大きい状態スコードとして定義されます。 HTTP の状態コードの一覧については、[W3 Org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) を参照してください。<br><br>ソースからは [EventResultDetails](#eventresultdetails) フィールドの値のみが提供されることがあり、その場合はそれを分析して **EventResult** の値を取得する必要があります。 |
| <a name="eventresultdetails"></a>**EventResultDetails** | オプション | String | HTTP セッションの場合、値は HTTP の状態コードである必要があります。 <br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は **EventOriginalResultDetails** フィールドに格納する必要があります。|
| **EventOriginalResultDetails**    | オプション    | String     |  ソースによって提供されている場合、[EventResultDetails](#eventresultdetails) の元のレコードに提供された値。|
| **EventSeverity** | Mandatory | Enumerated | 検出された脅威または警告を表す場合は、イベントの重大度。 指定できる値は、`Informational`、`Low`、`Medium`、`High` です。 <br><br>イベントが脅威を表していない場合は、値 `Informational` を使用します。<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [EventOriginalSeverity](#eventoriginalseverity) フィールドに格納します。 |
| <a name="eventoriginalseverity"></a>**EventOriginalSeverity** | オプション | String | ソース レコードで提供された元の重大度の値。 |
| **EventOriginalUid** | オプション | String | 元のレコードの一意の ID (ソースによって提供されている場合)。<br><br>例: `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| **EventOriginalType** | オプション | String | 元のイベントの種類または ID (ソースによって提供されている場合)。 <br><br>例: `5031` |
| <a name="eventproduct"></a>**EventProduct** | Mandatory | String | イベントを生成している製品。<br><br>例: `Sysmon`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。 |
| **EventProductVersion** | オプション | String | イベントを生成している製品のバージョン。<br><br>例: `12.1` |
| **EventVendor** | Mandatory | String | イベントを生成している製品のベンダー。<br><br>例: `Microsoft`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。 |
| **EventSchema** | Mandatory | String | スキーマの名前です。 ここに記載されているスキーマの名前は `NetworkSession` です。 |
| **EventSchemaVersion** | Mandatory | String | スキーマのバージョン。 ここに記載されているスキーマのバージョンは `0.2` です。 |
| **EventReportUrl** | オプション | String | あるリソースのイベントに指定された、そのイベントに関する追加情報を提供する URL。 |
| **Dvc** | エイリアス | String | レポートまたは中間デバイスの一意識別子。<br><br>例: `ContosoDc.Contoso.Azure`<br><br>このフィールドは、[DvcFQDN](#dvcfqdn)、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドの別名である可能性があります。 明確なデバイスがないクラウド ソースの場合は、[EventProduct](#eventproduct) フィールドと同じ値を使用します。 |
| <a name="dvcipaddr"></a>**DvcIpAddr** | 推奨 | IP アドレス | レポートまたは中間デバイスの IP アドレス。<br><br>例: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Mandatory | String | ドメイン情報を除外した、レポートまたは中間デバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。<br><br>例: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | 推奨 | String | レポートまたは中間デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 推奨 | Enumerated | [DvcDomain](#dvcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**注**: [DvcDomain](#dvcdomain) フィールドが使用されている場合、このフィールドは必須です。 |
| <a name="dvcfqdn"></a>**DvcFQDN** | オプション | String | 使用可能な場合にドメイン情報を含めた、レポートまたは中間デバイスのホスト名。 <br><br> 例: `Contoso\DESKTOP-1282V4D`<br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [DvcDomainType](#dvcdomaintype) フィールドには、使用されている形式が反映されます。  |
| <a name="dvcid"></a>**DvcId** | オプション | String | レコードで報告されるレポートまたは中間デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | オプション | Enumerated | [DvcId](#dvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、リストの最初のものを使用し、他のものはそれぞれフィールド名 **DvcAzureResourceId** および **DvcMDEid** を使用して格納します。<br><br>**注**: [DvcId](#dvcid) フィールドが使用されている場合、このフィールドは必須です。 |
| **AdditionalFields** | オプション | 動的 | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名そのままで保持するか、動的な **AdditionalFields** フィールドを作成し、それに追加情報をキーと値のペアとして追加します。 |
| | | | |

### <a name="network-session-fields"></a>ネットワーク セッションのフィールド

次のフィールドは、すべてのネットワーク セッションのアクティビティ ログに共通です。

| フィールド | クラス | 型 | 説明 |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | 推奨 | IP アドレス | 接続またはセッションのターゲットの IP アドレス。 <br><br>例: `2001:db8::ff00:42:8329`<br><br>**注**: [DstHostname](#dsthostname) が指定されている場合、この値は必須です。 |
| <a name="dstportnumber"></a>**DstPortNumber** | オプション | Integer | 送信先 IP ポート。<br><br>例: `443` |
| <a name="dsthostname"></a>**DstHostname** | 推奨 | String | ドメイン情報を除いた、ターゲット デバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。<br><br>例: `DESKTOP-1282V4D`<br><br>**注**: [DstIpAddr](#dstipaddr) が指定されている場合、この値は必須です。 |
| **hostname** | エイリアス | | [DstHostname](#dsthostname) のエイリアス |
| <a name="dstdomain"></a>**DstDomain** | 推奨 | String | ターゲット デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | 推奨 | Enumerated | [DstDomain](#dstdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>[DstDomain](#dstdomain) が使用されている場合は必須です。 |
| **DstFQDN** | オプション | String | 使用可能な場合はドメイン情報を含む、ターゲット デバイスのホスト名。 <br><br>例: `Contoso\DESKTOP-1282V4D` <br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [DstDomainType](#dstdomaintype) フィールドには、使用されている形式が反映されます。   |
| <a name="dstdvcid"></a>**DstDvcId** | オプション | String | レコードで報告されるターゲット デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | オプション | Enumerated | [DstDvcId](#dstdvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>複数の ID を使用できる場合は、上のリストの最初のものを使用し、他のものはそれぞれ **DstDvcAzureResourceId** または **DstDvcMDEid** フィールドに格納します。<br><br>**DstDeviceId** が使用されている場合は必須です。|
| **DstDeviceType** | オプション | Enumerated | ターゲット デバイスの種類。 次の値を指定できます。<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | オプション | String | ターゲット ユーザーの、コンピューターが判読できる英数字の一意表現。 <br><br>以下の形式と種類がサポートされています。<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>ID の種類は、[DstUserIdType](#dstuseridtype) フィールドに格納します。 他の ID がある場合は、フィールド名をそれぞれ **DstUserSid**、**DstUserUid**、**DstUserAADID**、**DstUserOktaId**、**UserAwsId** に正規化することをお勧めします。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | オプション | Enumerated | [DstUserId](#dstuserid) フィールドに格納されている ID の種類。 <br><br>サポートされている値は、`SID`、`UIS`、`AADID`、`OktaId`、`AWSId` です。 |
| <a name="dstusername"></a>**DstUsername** | オプション | String | 使用可能な場合はドメイン情報を含む、ターゲットのユーザー名。 <br><br>以下のいずれかの形式と、以下の優先順位を使用します。<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Simple 形式は、ドメイン情報が利用できない場合にのみ使用します。<br><br>ユーザー名の種類は、[DstUsernameType](#dstusernametype) フィールドに格納します。 他の ID がある場合は、フィールド名を **DstUserUpn**、**DstUserWindows**、**DstUserDn** に正規化することをお勧めします。詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `AlbertE` |
| **User** | エイリアス | | [DstUsername](#dstusername) のエイリアス |
| <a name="dstusernametype"></a>**DstUsernameType** | オプション | Enumerated | [DstUsername](#dstusername) フィールドに格納されているユーザー名の種類を指定します。 サポートされている値は、`UPN`、`Windows`、`DN`、`Simple` です。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `Windows` |
| **DstUserType** | オプション | Enumerated | アクターの種類。 サポートされている値は次のとおりです。<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [DstOriginalUserType](#dstoriginalusertype) フィールドに格納します。 |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | オプション | String | ソースによって提供されている場合、元のターゲット ユーザーの種類。 |
| **DstUserDomain** | オプション | String | このフィールドは、下位互換性のためだけに残されています。 ドメイン情報が使用可能な場合、ASIM では [DstUsername](#dstusername) フィールドの一部としてそれが必要です。 |
| <a name="dstappname"></a>**DstAppName** | オプション | String | ターゲット アプリケーションの名前。<br><br>例: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | オプション | String | レポート デバイスによって報告された、ターゲット アプリケーションの ID。<br><br>例: `124` |
| **DstAppType** | オプション | String | アクターに代わって承認するアプリケーションの種類。 サポートされている値は次のとおりです。<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>[DstAppName](#dstappname) または [DstAppId](#dstappid) が使用されている場合、このフィールドは必須です。 |
| **DstZone** | オプション | String | レポート デバイスにより定義された、送信先のネットワーク ゾーン。<br><br>例: `Dmz` |
| **DstInterfaceName** | オプション | String | 送信先デバイスによる接続またはセッションに使用されるネットワーク インターフェイス。<br><br>例: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | オプション | String | ターゲット デバイスで使用されるネットワーク インターフェイスの GUID。<br><br>例:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | オプション | String | ターゲット デバイスによって接続またはセッションに使用される、ネットワーク インターフェイスの MAC アドレス。<br><br>例: `06:10:9f:eb:8f:14` |
| **DstGeoCountry** | オプション | 国 | ターゲット IP アドレスに関連付けられている国。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `USA` |
| **DstGeoRegion** | 省略可能 | リージョン | ターゲット IP アドレスに関連付けられた国の地域または州。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `Vermont` |
| **DstGeoCity** | オプション | City | ターゲット IP アドレスに関連付けられている都市。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `Burlington` |
| **DstGeoLatitude** | 省略可能 | Latitude | ターゲット IP アドレスに関連付けられている地理的座標の緯度。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `44.475833` |
| **DstGeoLongitude** | 省略可能 | Longitude | ターゲット IP アドレスに関連付けられている地理的座標の経度。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `73.211944` |
| <a name="srcipaddr"></a>**SrcIpAddr** | 推奨 | IP アドレス | 接続またはセッションの開始元の IP アドレス。 **SrcHostname** が指定されている場合、この値は必須です。<br><br>例: `77.138.103.108` |
| **IpAddr** | エイリアス | | [SrcIpAddr](#srcipaddr) の別名 |
| **SrcPortNumber** | オプション | Integer | 接続元の IP ポート。 複数の接続を構成するセッションに関連しないことがあります。<br><br>例: `2335` |
| **SrcHostname** | 推奨 | String | ドメイン情報を除いた、ソース デバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。[SrcIpAddr](#srcipaddr) が指定されている場合、この値は必須です。<br><br>例: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | 推奨 | String | ソース デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 推奨 | Enumerated | [SrcDomain](#srcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows` (例: `contoso`)<br>- `FQDN` (例: `microsoft.com`)<br><br>[SrcDomain](#srcdomain) が使用されている場合は必須です。 |
| **SrcFQDN** | オプション | String | ソース デバイスのホスト名 (使用可能な場合はドメイン情報を含む)。 <br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [SrcDomainType](#srcdomaintype) フィールドには、使用されている形式が反映されます。 <br><br>例: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | オプション | String | レコードで報告されるソース デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | オプション | Enumerated | [SrcDvcId](#srcdvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、上のリストの最初のものを使用し、他のものはそれぞれ **SrcDvcAzureResourceId** および **SrcDvcMDEid** に格納します。<br><br>**注**: [SrcDvcId](#srcdvcid) が使用されている場合、このフィールドは必須です。 |
| **SrcDeviceType** | オプション | Enumerated | ソース デバイスの種類。 次の値を指定できます。<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | オプション | String | ソース ユーザーの、コンピューターが判読できる英数字の一意表現。 形式とサポートされる型は次のとおりです。<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>ID の種類は、[SrcUserIdType](#srcuseridtype) フィールドに格納します。 他の ID を使用できる場合は、フィールド名を SrcUserSid、SrcUserUid、SrcUserAadId、SrcUserOktaId、UserAwsId にそれぞれ正規化することをお勧めします。詳細については、「ユーザー エンティティ」を参照してください。<br><br>例: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | オプション | Enumerated | [SrcUserId](#srcuserid) フィールドに格納されている ID の種類。 サポートされている値: `SID`、`UIS`、`AADID`、`OktaId`、`AWSId`。 |
| <a name="srcusername"></a>**SrcUsername** | オプション | String | 使用可能な場合はドメイン情報を含む、ソースのユーザー名。 以下のいずれかの形式と、以下の優先順位を使用します。<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Simple 形式は、ドメイン情報が利用できない場合にのみ使用します。<br><br>ユーザー名の種類は、[SrcUsernameType](#srcusernametype) フィールドに格納します。 他の ID を使用できる場合は、フィールド名を **SrcUserUpn**、**SrcUserWindows**、**SrcUserDn** に正規化することをお勧めします。<br><br>詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | オプション | Enumerated | [SrcUsername](#srcusername) フィールドに格納されているユーザー名の種類を指定します。 サポートされている値: `UPN`、`Windows`、`DN`、`Simple`。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `Windows` |
| **SrcUserType** | オプション | Enumerated | アクターの種類。 使用できる値は、以下のとおりです。<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [EventOriginalSeverity](#eventoriginalseverity) フィールドに格納します。 |
| **SrcOriginalUserType** | | | ソースによって提供されている場合、元のソース ユーザーの種類。 |
| **SrcUserDomain** | オプション | String | このフィールドは、下位互換性のためだけに残されています。 ドメイン情報が使用可能な場合、ASIM では [SrcUsername](#srcusername) フィールドの一部としてそれが必要です。 |
| <a name="srcappname"></a>**SrcAppName** | オプション | String | ソース アプリケーションの名前。 <br><br>例: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | オプション | String | レポート デバイスによって報告された、ターゲット アプリケーションの ID。<br><br>例: `124` |
| **SrcAppType** | オプション | String | ソース アプリケーションの種類。 サポートされている値は次のとおりです。<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>[SrcAppName](#srcappname) または [SrcAppId](#srcappid) が使用されている場合、このフィールドは必須です。 |
| **SrcZone** | オプション | String | レポート デバイスにより定義された、送信元のネットワーク ゾーン。<br><br>例: `Internet` |
| **SrcIntefaceName** | オプション | String | 送信元デバイスで接続またはセッションに使用されるネットワーク インターフェイス。 <br><br>例: `eth01` |
| **SrcInterfaceGuid** | オプション | String | ソース デバイスで使用されるネットワーク インターフェイスの GUID。<br><br>例:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | オプション | String | 接続またはセッションの開始元のネットワーク インターフェイスの MAC アドレス。<br><br>例: `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | オプション | 国 | 発信元 IP アドレスに関連付けられている国。<br><br>例: `USA` |
| **SrcGeoRegion** | 省略可能 | リージョン | 発信元 IP アドレスに関連付けられている国内の地域。<br><br>例: `Vermont` |
| **SrcGeoCity** | オプション | City | 発信元 IP アドレスに関連付けられている都市。<br><br>例: `Burlington` |
| **SrcGeoLatitude** | 省略可能 | Latitude | 発信元 IP アドレスに関連付けられている地理的座標の緯度。<br><br>例: `44.475833` |
| **SrcGeoLongitude** | 省略可能 | Longitude | 発信元 IP アドレスに関連付けられている地理的座標の経度。<br><br>例: `73.211944` |
| **NetworkApplicationProtocol** | オプション | String | 接続またはセッションで使用されるアプリケーション レイヤー プロトコル。 [DstPortNumber](#dstportnumber) の値が提供されている場合は、**NetworkApplicationProtocol** も含める必要があります。 ソースから値を使用できない場合は、[DstPortNumber](#dstportnumber) から値を得ます。<br><br>例: `HTTP` |
| **NetworkProtocol** | オプション | Enumerated | [IANA プロトコルの割り当て](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml)に記載されている、接続またはセッションによって使用される IP プロトコル。 通常は、`TCP`、`UDP`、または `ICMP`。<br><br>例: `TCP` |
| **NetworkDirection** | オプション | Enumerated | 組織に対する接続またはセッションの方向。 サポートされている値: `Inbound`、`Outbound`、`Listen`。 `Listen` は、デバイスによってネットワーク接続の受け入れが開始されましたが、実際には必ずしも接続されていないことを示します。|
| <a name="networkduration"></a>**NetworkDuration** | オプション | Integer | ネットワーク セッションまたは接続の完了にかかる時間 (ミリ秒単位)。<br><br>例: `1500` |
| **Duration** | エイリアス | | [NetworkDuration](#networkduration) のエイリアス |
| **NetworkIcmpCode** | オプション | Integer | ICMP メッセージの種類を示す数値。IPv4 ネットワーク接続の場合は [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) で示されている値、IPv6 ネットワーク接続の場合は [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) で示されている値。 [NetworkIcmpType](#networkicmptype) の値が提供されている場合、このフィールドは必須です。 ソースから値を使用できない場合は、代わりに [NetworkIcmpType](#networkicmptype) フィールドから値を得ます。<br><br>例: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | オプション | String | ICMP メッセージの種類のテキスト表現。IPv4 ネットワーク接続の場合は [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) で示されている値、IPv6 ネットワーク接続の場合は [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) で示されている値。<br><br>例: `Destination Unreachable` |
| **DstBytes** | 推奨 | Integer | 接続またはセッションで送信先から送信元に送信されたバイト数。 イベントが集計される場合、**DstBytes** は集計されたセッションの合計になります。<br><br>例: `32455` |
| **SrcBytes** | 推奨 | Integer | 接続またはセッションで送信元から送信先に送信されたバイト数。 イベントが集計される場合、**SrcBytes** は集計されたセッションの合計になります。<br><br>例: `46536` |
| **NetworkBytes** | オプション | Integer | 両方向に送信されたバイト数。 **BytesReceived** と **BytesSent** が両方とも存在する場合、**BytesTotal** はその合計と同じである必要があります。 イベントが集計される場合、**NetworkBytes** は集計されたセッションの合計になります。<br><br>例: `78991` |
| **DstPackets** | オプション | Integer | 接続またはセッションで送信先から送信元に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。イベントが集計される場合、**DstPackets** はすべての集計されたセッションの合計になります。<br><br>例: `446` |
| **SrcPackets** | オプション | Integer | 接続またはセッションで送信元から送信先に送信されたパケット数。 パケットの意味はレポート デバイスで定義されます。 イベントが集計される場合、**SrcPackets** は集計されたセッションの合計になります。<br><br>例: `6478` |
| **NetworkPackets** | オプション | Integer | 両方向に送信されたパケット数。 **PacketsReceived** と **PacketsSent** が両方とも存在する場合、**BytesTotal** はその合計と同じである必要があります。 パケットの意味はレポート デバイスで定義されます。 イベントが集計される場合、**NetworkPackets** は集計されたセッションの合計になります。<br><br>例: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | 省略可能 | string | レポート デバイスによって報告されたセッション識別子。 <br><br>例: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | エイリアス | String | [NetworkSessionId](#networksessionid) のエイリアス |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>中間デバイス フィールド

次のフィールドは、ネットワーク セッションを中継するファイアウォールやプロキシなどの中間デバイスに関する情報がレコードに含まれる場合に便利です。

| フィールド | クラス | 型 | 説明 |
| --- | --- | --- | --- |
| **DstNatIpAddr** | オプション | IP アドレス | 中間 NAT デバイスによって報告された場合は、その NAT デバイスがソースとの通信に使用する IP アドレス。<br><br>例: `2::1` |
| **DstNatPortNumber** | オプション | Integer | 中間 NAT デバイスによって報告された場合は、その NAT デバイスがソースとの通信に使用するポート。<br><br>例: `443` |
| **SrcNatIpAddr** | オプション | IP アドレス | 中間 NAT デバイスによって報告された場合は、その NAT デバイスがターゲットとの通信に使用する IP アドレス。<br><br>例: `4.3.2.1` |
| **SrcNatPortNumber** | オプション | Integer | 中間 NAT デバイスによって報告された場合は、その NAT デバイスがターゲットとの通信に使用するポート。<br><br>例: `345` |
| **DvcInboundInterface** | オプション | String | 中間デバイスによって報告された場合は、その NAT デバイスがソース デバイスへの接続に使用するネットワーク インターフェイス。<br><br>例: `eth0` |
| **DvcOutboundInterface** | オプション | String | 中間デバイスによって報告された場合は、その NAT デバイスがターゲット デバイスへの接続に使用するネットワーク インターフェイス。<br><br>例: `Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>HTTP セッションのフィールド

HTTP セッションは、HTTP プロトコルを使用するネットワーク セッションです。 このようなセッションは、多くの場合、Web サーバー、Web プロキシ、Web セキュリティ ゲートウェイによって報告されます。 HTTP セッションに固有の追加フィールドを次に示します。

| フィールド | クラス | 型 | 説明 |
| --- | --- | --- | --- |
| **Url** | 推奨 | String | HTTP/HTTPS ネットワーク セッションの場合、パラメーターを含む完全な HTTP 要求 URL。 イベントが HTTP セッションを表す場合、このフィールドは必須です。<br><br>例: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | オプション | String | URL または URL のドメイン部分の定義されたグループ。 カテゴリは一般に Web セキュリティ ゲートウェイによって提供され、URL が示すサイトの内容に基づきます。<br><br>例: 検索エンジン、成人向け、ニュース、広告、パーク ドメイン。 |
| **UrlOriginal** | オプション | String | URL がレポート デバイスによって変更され、両方の値が提供された場合の、URL の元の値。 |
| **HttpVersion** | オプション | String | HTTP/HTTPS ネットワーク接続の HTTP 要求バージョン。<br><br>例: `2.0` |
| **HttpRequestMethod** | 推奨 | Enumerated | HTTP/HTTPS ネットワーク セッションの HTTP メソッド。 値は [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) と [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2) で定義されており、`GET`、`HEAD`、`POST`、`PUT`、`DELETE`、`CONNECT`、`OPTIONS`、`TRACE`、`PATCH` が含まれます。<br><br>例: `GET` |
| **HttpStatusCode** | エイリアス | | HTTP/HTTPS ネットワーク セッションの HTTP 状態コード。 [EventResultDetails](#eventresultdetails) のエイリアス。 |
| <a name="httpcontenttype"></a>**HttpContentType** | オプション | String | HTTP/HTTPS ネットワーク セッションの HTTP 応答のコンテンツ タイプのヘッダー。 <br><br>**注**: **HttpContentType** フィールドには、コンテンツの形式と、追加のパラメーター (実際の形式を取得するために使用されるエンコードなど) の両方が含まれる場合があります。<br><br> 例: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | オプション | String | [HttpContentType](#httpcontenttype) のコンテンツ形式の部分 <br><br> 例: `text/html` |
| **HttpReferrer** | オプション | String | HTTP/HTTPS ネットワーク セッションの HTTP 参照元ヘッダー。<br><br>**注**: ASIM は OSSEM と同期して、元の HTTP ヘッダーのスペルではなく、"*参照元*" の正しいスペルを使用します。<br><br>例: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | オプション | String | HTTP/HTTPS ネットワーク セッションの HTTP ユーザー エージェント ヘッダー。<br><br>例:<br> `Mozilla/5.0` (Windows NT 10.0、WOW64)<br>`AppleWebKit/537.36` (KHTML、Gecko など)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | エイリアス | | [HttpUserAgent](#httpuseragent) のエイリアス |
| **HttpRequestXff** | オプション | IP アドレス | HTTP/HTTPS ネットワーク セッションの HTTP X-Forwarded-For ヘッダー。<br><br>例: `120.12.41.1` |
| **HttpRequestTime** | オプション | Integer | 要求がサーバーに送信されるまでにかかったミリ秒数 (該当する場合)。<br><br>例: `700` |
| **HttpResponseTime** | オプション | Integer | サーバーで応答を受け取るまでにかかったミリ秒数 (該当する場合)。<br><br>例: `800` |
| **FileName** | オプション | String | HTTP アップロードの場合、アップロードされたファイルの名前。 |
| **FileMD5** | オプション | MD5 | HTTP アップロードの場合、アップロードされたファイルの MD5 ハッシュ。<br><br>例: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | オプション | SHA1 | HTTP アップロードの場合、アップロードされたファイルの SHA1 ハッシュ。<br><br>例:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | オプション | SHA256 | HTTP アップロードの場合、アップロードされたファイルの SHA256 ハッシュ。<br><br>例:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | オプション | SHA512 | HTTP アップロードの場合、アップロードされたファイルの SHA512 ハッシュ。 |
| **FileSize** | オプション | Integer | HTTP アップロードの場合、アップロードされたファイルのバイト サイズ。 |
| **FileContentType** | オプション | String | HTTP アップロードの場合、アップロードされたファイルのコンテンツ タイプ。 |
| | | | |

さらに、HTTP セッションに使用されるときは、次の標準ネットワーク スキーマ フィールドに追加のガイドラインがあります。

- **EventType** は、&quot;HTTP セッション&quot; である必要があります
- **EventResultDetails** は、HTTP 状態コードに設定されている必要があります。
- **EventResult** は、HTTP 状態コードが 400 未満の場合は &quot;Success&quot;、それ以外の場合は &quot;Failure&quot; である必要があります。

### <a name="inspection-fields"></a><a name="inspection-fields"></a>検査のフィールド

次のフィールドは、ファイアウォール、IPS、Web セキュリティ ゲートウェイなどのセキュリティ デバイスによって実行された検査を表すために使用されます。

| フィールド | クラス | 型 | 説明 |
| --- | --- | --- | --- |
| **NetworkRuleName** | オプション | String | [DvcAction](#dvcaction) が決定されたときに使用されたルールの名前または ID。<br><br> 例: `AnyAnyDrop` |
| **NetworkRuleNumber** | オプション | Integer | [DvcAction](#dvcaction) が決定されたときに使用されたルールの番号。<br><br>例: `23` |
| **Rule** | Mandatory | String | `NetworkRuleName` または `NetworkRuleNumber` のいずれか |
| <a name="dvcaction"></a>**DvcAction** | オプション | Enumerated | ネットワーク セッションで実行されたアクション。 サポートされる値は次のとおりです。<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は、[DvcOriginalAction](#dvcoriginalaction) フィールドに格納する必要があります。<br><br>例: `drop` |
| <a name="dvcoriginalaction"></a>**DvcOriginalAction** | オプション | String | レポート デバイスによって提供された元の [DvcAction](#dvcaction)。 |
| **ThreatId** | オプション | String | ネットワーク セッションで識別された脅威またはマルウェアの ID。<br><br>例: `Tr.124` |
| **ThreatName** | オプション | String | ネットワーク セッションで識別された脅威またはマルウェアの名前。<br><br>例: `EICAR Test File` |
| **ThreatCategory** | 省略可能 | String | ネットワーク セッションで識別された脅威またはマルウェアのカテゴリ。<br><br>例: `Trojan` |
| **ThreatRiskLevel** | オプション | Integer | セッションに関連付けられているリスク レベル。 レベルは、**0** から **100** の間の数値である必要があります。<br><br>**注**: 値は、異なるスケールを使用してソース レコードに提供される場合があり、このスケールに正規化する必要があります。 元の値は [ThreatRiskLevelOriginal](#threatriskleveloriginal) に格納する必要があります。 |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | オプション | String | レポート デバイスによって報告されたリスク レベル。 |
| | | | |

### <a name="other-fields"></a>その他のフィールド

イベントがネットワーク セッションのいずれかのエンドポイントによって報告された場合、セッションを開始または終了したプロセスに関する情報が含まれる場合があります。 このような場合は、[ASIM プロセス イベント スキーマ](process-events-normalization-schema.md)を使用してこの情報を正規化します。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Microsoft Sentinel の DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Microsoft Sentinel プロセス イベント正規化スキーマ リファレンス](process-events-normalization-schema.md)
- [Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)
