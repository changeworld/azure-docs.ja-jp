---
title: Microsoft Azure Sentinel の DHCP 正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel の DHCP 正規化スキーマについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 27e9e052265264a298e27fe64dfad4561c68dd12
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724313"
---
# <a name="microsoft-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Microsoft Azure Sentinel の DHCP 正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

DHCP 情報モデルは、DHCP サーバーによって報告されるイベントを記述するために使用され、ソースに依存しない分析を有効にするために Microsoft Azure Sentinel によって使用されます。

詳細については、「[正規化と Microsoft Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> 現在、DHCP 正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="schema-overview"></a>スキーマの概要

ASIM DHCP スキーマは、DHCP サーバーの活動を表します。これには、クライアント システムからリースされた DHCP IP アドレスを求める要求の処理や、付与されたリースを使用した DNS サーバーの更新が含まれます。

DHCP イベントの最も重要なフィールドは、[SrcIpAddr](#srcipaddr) と [SrcHostname](#srchostname) です。これらは DHCP サーバーがリースを付与することによってバインドされ、それぞれ [IpAddr](#ipaddr) と [Hostname](#hostname) のフィールドが別名になっています。 [SrcMacAddr](#srcmacaddr) フィールドは、IP アドレスがリースされていない場合に使用されるクライアント コンピューターを表すことからも重要です。  

DHCP サーバーでクライアントが拒否される場合があります。それは、セキュリティ上の懸念があるため、またはネットワークが飽和状態になっているためです。 また、クライアントが検疫される場合もあります。その場合は、制限のあるネットワークに接続するための IP アドレスがリースされます。 [EventResult](#eventresult)、[EventResultDetails](#eventresultdetails)、[DvcAction](#dvcaction) の各フィールドには、DHCP サーバーの応答とアクションについての情報が提供されます。

リースの期間は、[DhcpLeaseDuration](#dhcpleaseduration) フィールドに格納されます。

## <a name="schema-details"></a>スキーマの詳細

ASIM は、[オープン ソース セキュリティ イベント メタデータ (OSSEM)](https://github.com/OTRF/OSSEM) プロジェクトに合わせて調整されます。

OSSEM には、ASIM DHCP スキーマに相当する DHCP スキーマがありません。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは Log Analytics によってレコードごとに生成され、[カスタム コネクタを作成する](create-custom-connector.md)ときにオーバーライドできます。

| **フィールド** | **Type** | **説明** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | 日付/時刻 | イベントがレポート デバイスによって生成された日時。 |
| **\_ResourceId** | guid | レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。 |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを異なるテーブルに対して複数のチャネルを通じて受信でき、同じ [EventVendor](#eventvendor) 値と [EventProduct](#eventproduct) 値を持つ場合に便利です。<br><br>たとえば、Sysmon イベントを、イベント テーブルまたは WindowsEvent テーブルのいずれかに収集できます。 |
| | | |

> [!NOTE]
> セキュリティにあまり関係のない他の Log Analytics フィールドについては、[Azure Monitor](../azure-monitor/logs/log-standard-columns.md) に関するドキュメントで説明されています。
> 

### <a name="event-fields"></a>イベント フィールド

イベント フィールドはすべてのスキーマに共通であり、アクティビティ自体とレポート デバイスが記述されています。

| **フィールド** | **クラス** | **Type**  | **考察 (Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | オプション | String | レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。 |
| **EventCount** | Mandatory | Integer | レコードによって記述されるイベントの数。<br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。<br><br>他のソースの場合は、`1` に設定する必要があります。<br><br>例: `1`|
| **EventStartTime** | Mandatory | 日付/時刻 | ソースが集計に対応しており、レコードで複数のイベントが表されている場合は、このフィールドを使用して、最初のイベントが生成された日時を指定します。 <br><br>それ以外の場合は、[TimeGenerated](#timegenerated) フィールドの別名です。 |
| **EventEndTime** | エイリアス || [TimeGenerated](#timegenerated) フィールドの別名。 |
| **EventType** | Mandatory | Enumerated | レコードによって報告される操作を示します。 <br><Br> 設定可能な値は、`Assign`、`Renew`、`Release`、および `DNS Update` です。 <br><br>例: `Assign`| 
| <a name="eventresult"></a>**EventResult** | Mandatory | Enumerated | 以下の値のいずれかです。`Success`、`Partial`、`Failure`、`NA` (該当なし)。<br> <br>ソース レコードでは、異なる用語を使用して値が指定されている場合があります。それを、これらの値に正規化する必要があります。 また、ソースで [EventResultDetails](#eventresultdetails) フィールドのみが提供される場合もあり、これを分析して **EventResult** 値を導き出す必要があります。 クライアントが DHCP サーバーによって検疫される場合は、このフィールドを `Partial` に設定する必要があります。<br><br>例: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | エイリアス | | [EventResult](#eventresult) フィールドでレポートされた結果の理由または詳細。 <br><Br> 指定できる値は、`Exhausted`、`Quarantined`、`Denied` です。 <br><br>例: `Exhausted` |
| **EventOriginalResultDetails**    | オプション    | String     |  ソースによって提供されている場合、[EventResultDetails](#eventresultdetails) の元のレコードに提供された値。 Windows の DHCP サーバー ログの場合、ここに QResult フィールド値を格納します。 |
| **EventOriginalUid** | オプション | String | ソースによって提供されている場合、元のレコードの一意の ID。 |
| **EventOriginalType**   | オプション    | String  |  元のイベントの種類または ID (ソースによって提供されている場合)。<br><br>例: `DNS Assign Failed` |
| <a name ="eventproduct"></a>**EventProduct** | Mandatory | String | イベントを生成している製品。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 <br><br>例: `DHCP Server` |
| **EventProductVersion** | オプション | String | イベントを生成している製品のバージョン。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。 <br><br>例: `12.1` |
| <a name="eventvendor"></a>**EventVendor** | Mandatory | String | イベントを生成している製品のベンダー。 このフィールドは、ソース レコードで使用できないことがあります。その場合は、パーサーで設定する必要があります。<br><br>例: `Microsoft`|
| **EventSchemaVersion** | Mandatory | String | ここに記載されているスキーマのバージョンは **0.1.0** です。 |
| **EventSchema** | Mandatory | String | ここに記載されているスキーマの名前は **Dhcp** です。 |
| **EventReportUrl** | オプション | String | リソースのイベントで提供された、そのイベントに関する他の情報を提供する URL。 |
| <a name="dvc"></a>**Dvc** | エイリアス | String | DHCP サーバーの一意の識別子。<br><br>例: `ContosoDc.Contoso.Azure`<br><br>このフィールドは、[DvcFQDN](#dvcfqdn)、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドの別名である可能性があります。 明確なデバイスがないクラウド ソースの場合は、[EventProduct](#eventproduct) フィールドと同じ値を使用します。 |
| <a name="dvcipaddr"></a>**DvcIpAddr** | 推奨 | IP アドレス | DHCP サーバーの IP アドレス。<br><br>例: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Mandatory | String | ドメイン情報を除いた、DHCP サーバーのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。<br><br>例: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | 推奨 | String | DHCP サーバーのドメイン。<br><br>例: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | 推奨 | Enumerated | [DvcDomain](#dvcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**注**: [DvcDomain](#dvcdomain) フィールドが使用されている場合、このフィールドは必須です。 |
| <a name="dvcfqdn"></a>**DvcFQDN** | オプション | String | 使用可能な場合はドメイン情報を含む、DHCP サーバーのホスト名。 <br><br> 例: `Contoso\DESKTOP-1282V4D`<br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [DvcDomainType](#dvcdomaintype) フィールドには、使用されている形式が反映されます。  |
| <a name="dvcid"></a>**DvcId** | オプション | String | レコードで報告される DHCP サーバーの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | オプション | Enumerated | [DvcId](#dvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、リストの最初のものを使用し、他のものはそれぞれフィールド名 **DvcAzureResourceId** および **DvcMDEid** を使用して格納します。<br><br>**注**: [DvcId](#dvcid) フィールドが使用されている場合、このフィールドは必須です。 |
| **EventSeverity** | オプション | Enumerated | DHCP サーバーがクライアントを検疫した場合は `Low` に設定され、サーバーがクライアントをブロックした場合は `Medium` に設定されます。 それ以外の場合は `Informational` に設定されます。 <br><br>例: `Informational`|
| <a name="dvcaction"></a>**DvcAction** | オプション | Enumerated | DHCP サーバーによって実行されるアクション。 指定できる値は、`Allow`、`Deny`、`Quarantine` です。<br><br>例: `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | オプション | 動的 | ソースから保持する必要のある他の情報が提供される場合は、元のフィールド名をそのまま使用するか、**AdditionalFields** 動的フィールドを作成し、追加情報をキーと値のペアとして追加します。 |
| | | | |

### <a name="dhcp-specific-fields"></a>DHCP 固有のフィールド

以下のフィールドは DHCP イベントに固有ですが、多くは他のスキーマのフィールドに類似しており、同じ名前付け規則に従います。

| **フィールド** | **クラス** | **Type** | **ノート** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | Mandatory | IP アドレス | DHCP サーバーによってクライアントに割り当てられた IP アドレス。<br><br>例: `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | エイリアス | | [SrcIpAddr](#srcipaddr) の別名 |
| <a name="requestedipaddr"></a>**RequestedIpAddr** | オプション | IP アドレス | DHCP クライアントによって要求された IP アドレス (使用可能な場合)。<br><br>例: `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | Mandatory | String | DHCP リースを要求しているデバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。<br><br>例: `DESKTOP-1282V4D` |
| <a name="hostname"></a>**Hostname** | エイリアス | | [SrcHostname](#srchostname) の別名 |
| <a name="srcdomain"></a> **SrcDomain** | 推奨 | String | ソース デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 推奨 | Enumerated | [SrcDomain](#srcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows` (例: `contoso`)<br>- `FQDN` (例: `microsoft.com`)<br><br>[SrcDomain](#srcdomain) が使用されている場合は必須です。 |
| **SrcFQDN** | オプション | String | ソース デバイスのホスト名 (使用可能な場合はドメイン情報を含む)。 <br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [SrcDomainType](#srcdomaintype) フィールドには、使用されている形式が反映されます。 <br><br>例: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | オプション | String | レコードで報告されるソース デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | オプション | Enumerated | [SrcDvcId](#srcdvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、上のリストの最初のものを使用し、他のものはそれぞれ **SrcDvcAzureResourceId** および **SrcDvcMDEid** に格納します。<br><br>**注**: [SrcDvcId](#srcdvcid) が使用されている場合、このフィールドは必須です。 |
| **SrcDeviceType** | オプション | Enumerated | ソース デバイスの種類。 次の値を指定できます。<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | オプション | String | ソース ユーザーの、コンピューターが判読できる英数字の一意表現。 形式とサポートされる型は次のとおりです。<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>- **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId**: `00urjk4znu3BcncfY0h7`<br>- **AWSId**: `72643944673`<br><br>ID の種類は、[SrcUserIdType](#srcuseridtype) フィールドに格納します。 他の ID を使用できる場合は、フィールド名を SrcUserSid、SrcUserUid、SrcUserAadId、SrcUserOktaId、UserAwsId にそれぞれ正規化することをお勧めします。<br><br>例: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | オプション | Enumerated | [SrcUserId](#srcuserid) フィールドに格納されている ID の種類。 サポートされている値: `SID`、`UIS`、`AADID`、`OktaId`、`AWSId`。 |
| <a name="srcusername"></a>**SrcUsername** | オプション | String | 使用可能な場合はドメイン情報を含む、ソースのユーザー名。 以下のいずれかの形式と、以下の優先順位を使用します。<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Simple 形式は、ドメイン情報が利用できない場合にのみ使用します。<br><br>ユーザー名の種類は、[SrcUsernameType](#srcusernametype) フィールドに格納します。 他の ID を使用できる場合は、フィールド名を **SrcUserUpn**、**SrcUserWindows**、**SrcUserDn** に正規化することをお勧めします。<br><br>詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `AlbertE` |
| **ユーザー名** | エイリアス | | [SrcUsername](#srcusername) の別名 |
| <a name="srcusernametype"></a>**SrcUsernameType** | オプション | Enumerated | [SrcUsername](#srcusername) フィールドに格納されているユーザー名の種類を指定します。 サポートされている値: `UPN`、`Windows`、`DN`、`Simple`。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `Windows` |
| **SrcUserType** | オプション | Enumerated | アクターの種類。 使用できる値は、以下のとおりです。<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [EventOriginalUserType](#srcoriginalusertype) フィールドに格納します。 |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | ソースによって提供されている場合、元のソース ユーザーの種類。 |
| <a name="srcmacaddr"></a>**SrcMacAddr** | Mandatory | MAC アドレス | DHCP リースを要求しているクライアントの MAC アドレス。 <br><br>**注**: Windows DHCP サーバーの場合、パーサーによって挿入される必要があるコロンを除いた、標準以外の方法で MAC アドレスがログに記録されます。<br><br>例: `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | オプション | Integer | クライアントに付与されたリースの長さ (秒単位)。 |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | 省略可能 | string | レポート デバイスによって報告されたセッション識別子。 Windows DHCP サーバーの場合は、これを TransactionID フィールドに設定します。 <br><br>例: `2099570186` |
| **SessionId** | エイリアス | String | [DhcpkSessionId](#dhcpsessionid) の別名 |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | オプション | Integer | DHCP セッションの完了にかかる時間 (ミリ秒単位)。<br><br>例: `1500` |
| **Duration** | エイリアス | | [DhcpSessionDuration](#dhcpsessionduration) の別名 |
| **DhcpSrcDHCId** | オプション | String | [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701) で定義されているとおりの DHCP クライアント ID |
| **DhcpCircuitId** | オプション | String | [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046) で定義されているとおりの DHCP 回線 ID |
| **DhcpSubscriberId** | オプション | String | [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993) で定義されているとおりの DHCP サブスクライバー ID |
| **DhcpVendorClassId**  | オプション | String | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925) で定義されているとおりの DHCP ベンダー クラス ID。 |
| **DhcpVendorClass**  | オプション | String | [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925) で定義されているとおりの DHCP ベンダー クラス。|
| **DhcpUserClassId**  | オプション | String | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004) で定義されているとおりの DHCP ユーザー クラス ID。|
| **DhcpUserClass** | オプション | String | [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004) で定義されているとおりの DHCP ユーザー クラス。|
| | | | |

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [ASIM ウェビナー](https://www.youtube.com/watch?v=WoGD-JeC7ng)を視聴したり、[スライド](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)を確認したりしてください
- [Advanced SIEM Information Model のスキーマ](normalization-about-schemas.md)
- [Advanced SIEM Information Model のパーサー](normalization-about-parsers.md)
- [Advanced SIEM Information Model のコンテンツ](normalization-content.md)
