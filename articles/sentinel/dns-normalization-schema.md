---
title: Microsoft Sentinel の DNS 正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Microsoft Sentinel の DNS 正規化スキーマについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: a47f3503af5d59fdabe4a2f814076c0af87ec82a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721387"
---
# <a name="microsoft-sentinel-dns-normalization-schema-reference-public-preview"></a>Microsoft Sentinel の DNS 正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

DNS 情報モデルは、DNS サーバーまたは DNS セキュリティ システムによって報告されるイベントを記述するために使用され、ソースに依存しない分析を有効にするために Microsoft Sentinel によって使用されます。

詳細については、「[正規化と Microsoft Sentinel 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> 現在、DNS 正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="schema-overview"></a>スキーマの概要

ASIM の DNS スキーマは DNS プロトコル アクティビティを表し、DNS サーバー、または DNS サーバーに DNS 要求を送信するデバイスによってログに記録される場合があります。 DNS プロトコル アクティビティには、DNS クエリ、DNS サーバーの更新、および DNS の一括データ転送が含まれます。 スキーマはプロトコル アクティビティを表しているため、RFC によって管理され、必要に応じてこの記事で言及されているパラメーター リストが正式に割り当てられます。 DNS スキーマは、DNS サーバーの監査イベントを表していません。 

DNS サーバーによって報告される最も重要なアクティビティは、`EventType` フィールドが `Query` に設定されている DNS クエリです。   

DNS イベントで特に重要なフィールドは次のとおりです。

- [DnsQuery](#query)。クエリが発行されたドメイン名を報告します。
- [SrcIpAddr](#srcipaddr) ([IpAddr](#ipaddr) の別名)。要求が生成された IP アドレスを表します。 
- [EventResultDetails](#eventresultdetails)。要求が成功したかどうかを報告し、失敗した場合はその理由を報告します。
- [DnsResponseName](#responsename) (使用可能な場合)。サーバーからクエリに対して提供される応答を保持します。 ASIM は応答の解析を必要とせず、その形式はソースによって異なります。 ソースに依存しないコンテンツでこのフィールドを使用するには、`has` または `contains` 演算子を使用してコンテンツを検索します。

クライアント デバイスで収集された DNS イベントには、[ユーザー](#user)と[プロセス](#process)の情報が含まれる場合もあります。 

## <a name="guidelines-for-collecting-dns-events"></a>DNS イベントの収集に関するガイドライン

DNS は、多数のコンピューターが対象になる可能性があるという点でユニークなプロトコルです。 また、DNS では UDP が使用されるので、要求と応答は切り離されており、互いに直接関連付けられていません。

次の図は、4 つのセグメントを含む簡略化された DNS 要求フローを示したものです。 実際の要求は、もっと複雑で、さらに多くのセグメントが含まれる場合があります。

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="簡略化された DNS 要求フロー。":::

DNS 要求フローでは要求と応答のセグメントは互いに直接接続されないので、完全なログ記録を行うと大量の重複が発生する可能性があります。

ログに記録する最も重要なセグメントはクライアントへの応答であり、ドメイン名のクエリ、検索の結果、クライアントの IP アドレスが提供されます。 多くの DNS システムではこのセグメントのみがログに記録されますが、他のもログに記録する価値のある部分があります。 たとえば、DNS キャッシュのポイズニング攻撃では、多くの場合、アップストリーム サーバーからの偽の応答が利用されます。

データ ソースで完全な DNS ログがサポートされていて、複数のセグメントのログ記録を選択してある場合は、クエリを調整して、Microsoft Sentinel でのデータの重複を防ぐ必要があります。

たとえば、次の正規化を使用してクエリを変更することがあります。

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>パーサー

### <a name="source-agnostic-parsers"></a>ソースに依存しないパーサー

すぐに使えるすべてのパーサーを 1 つにまとめる、ソースに依存しないパーサーを使用して、構成済みのソース全体にわたって分析が実行されるようにするには、クエリでテーブル名として次の KQL 関数を使用します。

| 名前 | 説明 | 使用手順 |
| --- | --- | --- |
| **imDNS** | "*和集合*" を使用して、すべての DNS ソースからの正規化されたイベントを含める集約パーサー。 |- ソースに依存しない分析でソースを追加または削除する場合は、このパーサーを更新します。 <br><br>- ソースに依存しないクエリでこの関数を使用します。|
| **ASimDNS** | `imDns` 関数に似ていますが、パラメーターがサポートされていないため、 **[ログ]** ページの時刻の選択で `custom` 値を必ずしも使用する必要はありません。 |- ソースに依存しない分析でソースを追加または削除する場合は、このパーサーを更新します。<br><br>- パラメーターを使用する予定がない場合は、ソースに依存しないクエリでこの関数を使用します。|
| **vimDNS\<vendor\>\<product\>** | ソース固有のパーサーでは、*imDNSWindowsOMS* などの特定のソース用の正規化が実装されています。 |- すぐに使える正規化パーサーがない場合は、ソースにソース固有のパーサーを追加します。 新しいパーサーへの参照を含むように、`im` 集約パーサーを更新します。 <br><br>- 解析と正規化の問題を解決するには、ソース固有のパーサーを更新します。<br><br>- ソース固有の分析には、ソース固有のパーサーを使用します。|
| **ASimDNS\<vendor\>\<product\>** | ソース固有のパーサーでは、特定のソース用の正規化が実装されています。 `vim*` 関数とは異なり、`ASimDNS*` 関数ではパラメーターがサポートされていません。 |- すぐに使える正規化パーサーがない場合は、ソースにソース固有のパーサーを追加します。 新しいパーサーへの参照を含むように、`ASim` 集約パーサーを更新します。<br><br>- 解析と正規化の問題を解決するには、ソース固有のパーサーを更新します。<br><br>- パラメーターを使用しない場合の対話型クエリには、ソース固有のパーサー `ASim` を使用します。|
| | | |

パーサーは、[Microsoft Sentinel GitHub リポジトリ](https://aka.ms/azsentinelDNS)からデプロイできます。

### <a name="out-of-the-box-source-specific-parsers"></a>すぐに使える、ソース固有のパーサー

Microsoft Sentinel には、次のようなすぐに使える製品固有の DNS パーサーが用意されています。

| **名前** | **説明** |
| --- | --- |
|**Microsoft DNS サーバー**  |   **DNS コネクタと Log Analytics エージェントを使用して収集されます**。 <br> - `ASimDnsMicrosoftOMS` (通常) <br>- `vimDnsMicrosoftOMS` (パラメーター化) <br><br>  **NXlog を使用して収集されます**。 <br> - `ASimDnsMicrosoftNXlog` (通常)<br>- `vimDnsMicrosoftNXlog` (パラメーター化) |
| **Azure Firewall** |- `ASimDnsAzureFirewall` (通常)<br>- `vimDnsAzureFirewall` (パラメーター化) |
|**Windows の Sysmon** (イベント 22) | `Event` と `WindowsEvent` の両方のテーブルをサポートする、**Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます**。 <br>- `ASimDnsMicrosoftSysmon` (通常)<br>- `vimDnsMicrosoftSysmon` (パラメーター化)  |
|**Cisco Umbrella**  | - `ASimDnsCiscoUmbrella` (通常)<br>- `vimDnsCiscoUmbrella` (パラメーター化)  |
|**Infoblox NIOS**  |- `ASimDnsInfobloxNIOS` (通常)<br>- `vimDnsInfobloxNIOS` (パラメーター化) |
| **GCP DNS** |- `ASimDnsGcp` (通常)<br>- `vimDnsGcp` (パラメーター化) |
| **Corelight Zeek DNS イベント** | - `ASimDnsCorelightZeek` (通常)<br>- `vimDnsCorelightZeek` (パラメーター化) |
| **zScaler ZIA** |- `AsimDnszScalerZIA` (通常)<br>- `vimDnszScalerZIA` (パラメーター化)  |
| | |

パーサーは、[Microsoft Sentinel GitHub リポジトリ](https://aka.ms/azsentinelDNS)からデプロイできます。

### <a name="add-your-own-normalized-parsers"></a>独自の正規化されたパーサーを追加する

DNS 情報モデルにカスタム パーサーを実装するときは、次の構文を使用して KQL 関数に名前を付けます。

- `vimDns<vendor><Product` (パラメーター化パーサーの場合)
- `ASimDns<vendor><Product` (標準パーサーの場合)

### <a name="filtering-parser-parameters"></a>パーサー パラメーターのフィルター処理

`im` および `vim*` パーサーでは、[フィルター処理パラメーター](normalization-about-parsers.md#optimized-parsers)がサポートされています。 これらのパーサーは省略可能ですが、クエリのパフォーマンスを向上させることができます。

次のフィルター処理パラメーターを使用できます。

| 名前     | 型      | 説明 |
|----------|-----------|-------------|
| **starttime** | DATETIME | この時間以降に実行された DNS クエリのみをフィルター処理します。 |
| **endtime** | DATETIME | この時間以前に実行が完了した DNS クエリのみをフィルター処理します。 |
| **srcipaddr** | string | この送信元 IP アドレスからの DNS クエリのみをフィルター処理します。 |
| **domain_has_any**| 動的 | `domain` (または`query`) に、一覧表示されているいずれかのドメイン名が (イベント ドメインの一部として) 含まれている場合に、DNS クエリのみをフィルター処理します。
| **responsecodename** | string | 応答コード名が指定された値と一致する DNS クエリのみをフィルター処理します。 <br>例: `NXDOMAIN` |
| **response_has_ipv4** | string | 指定された IP アドレスまたは IP アドレス プレフィックスで応答フィールドが始まる DNS クエリのみをフィルター処理します。 このパラメーターは、単一の IP アドレスまたはプレフィックスに対してフィルター処理する場合に使用します。 <br><br>応答が指定されていないソースの結果は返されません。|
| **response_has_any_prefix** | 動的| 一覧表示されているいずれかの IP アドレスまたは IP アドレス プレフィックスで応答フィールドが始まる DNS クエリのみをフィルター処理します。 <br><br>このパラメーターは、IP アドレスまたはプレフィックスの一覧に対してフィルター処理する場合に使用します。 <br><br>応答が指定されていないソースの結果は返されません。 |
| **eventtype**| string | 指定した種類の DNS クエリのみがフィルター処理されます。 値が指定されていない場合は、検索クエリのみが返されます。 |
| | | |

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

正規化された DNS イベントを使用する分析ルールの完全な一覧については、「[DNS クエリのセキュリティ コンテンツ](normalization-content.md#dns-query-security-content)」セクションを参照 してください。

## <a name="schema-details"></a>スキーマの詳細

DNS 情報モデルは、[OSSEM DNS エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md)に合わせて調整されています。

詳細については、[Internet Assigned Numbers Authority (IANA) のドメイン ネーム システム パラメーター リファレンス](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)に関するページを参照してください。

### <a name="common-fields"></a>共通フィールド

すべてのスキーマに共通するフィールドについては、[ASIM スキーマの概要](normalization-about-schemas.md#common)に関する記事を参照してください。 次のフィールドには、DNS イベントに関する特定のガイドラインがあります。

| **フィールド** | **クラス** | **Type**  | **説明** |
| --- | --- | --- | --- |
| **EventType** | Mandatory | Enumerated | レコードによって報告される操作を示します。 <br><br> DNS レコードの場合、この値は [DNS の操作コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)になります。 <br><br>例: `lookup`|
| **EventSubType** | オプション | Enumerated | **request** または **response**。 <br><br>ほとんどのソースでは、[応答だけがログに記録される](#guidelines-for-collecting-dns-events)ため、多くの場合、この値は **response** になります。  |
| <a name=eventresultdetails></a>**EventResultDetails** | エイリアス | | **_EventResult_** フィールドで報告された結果の理由または詳細。 <br><br> [ResponseCodeName](#responsecodename) フィールドの別名を設定します。|
| **EventSchemaVersion** | Mandatory | String | ここに記載されているスキーマのバージョンは **0.1.3** です。 |
| **EventSchema** | Mandatory | String | ここに記載されているスキーマの名前は **Dns** です。 |
| **Dvc** フィールド| -      | -    | DNS イベントの場合、デバイス フィールドは DNS イベントを報告するシステムを指します。 |
| | | | |


### <a name="dns-specific-fields"></a>DNS 固有のフィールド

以下のフィールドは DNS イベントに固有ですが、多くは他のスキーマのフィールドに類似しているため、同じ名前付け規則に従います。

| **フィールド** | **クラス** | **Type** | **ノート** |
| --- | --- | --- | --- |
| <a name="src"></a>**Src** | 推奨       | String     |    ソース デバイスの一意識別子。 <br><br>このフィールドは、[SrcDvcId](#srcdvcid)、[SrcHostname](#srchostname)、または [SrcIpAddr](#srcipaddr) フィールドの別名になる可能性があります。 <br><br>例: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | 推奨 | IP アドレス | DNS 要求を送信しているクライアントの IP アドレス。 再帰的な DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、`127.0.0.1` に設定されます。 <br><br>例: `192.168.12.1` |
| **SrcPortNumber** | オプション | Integer | DNS クエリの送信元ポート。<br><br>例: `54312` |
| <a name="ipaddr"></a>**IpAddr** | エイリアス | | [SrcIpAddr](#srcipaddr) の別名 |
| **SrcGeoCountry** | オプション | 国 | 発信元 IP アドレスに関連付けられている国。<br><br>例: `USA` |
| **SrcGeoRegion** | 省略可能 | リージョン | 発信元 IP アドレスに関連付けられている国内の地域。<br><br>例: `Vermont` |
| **SrcGeoCity** | オプション | City | 発信元 IP アドレスに関連付けられている都市。<br><br>例: `Burlington` |
| **SrcGeoLatitude** | 省略可能 | Latitude | 発信元 IP アドレスに関連付けられている地理的座標の緯度。<br><br>例: `44.475833` |
| **SrcGeoLongitude** | 省略可能 | Longitude | 発信元 IP アドレスに関連付けられている地理的座標の経度。<br><br>例: `73.211944` |
| **SrcRiskLevel** | オプション | Integer | ソースに関連付けられているリスク レベル。 この値は、`0` から `100` の範囲に調整する必要があります。`0` は問題なし、`100` は高リスクです。<br><br>例: `90` |
| <a name="srchostname"></a>**SrcHostname** | 推奨 | String | ドメイン情報を除いた、ソース デバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。[SrcIpAddr](#srcipaddr) が指定されている場合、この値は必須です。<br><br>例: `DESKTOP-1282V4D` |
| **hostname** | エイリアス | | [SrcHostname](#srchostname) の別名 |
|<a name="srcdomain"></a> **SrcDomain** | 推奨 | String | ソース デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | 推奨 | Enumerated | [SrcDomain](#srcdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows` (例: `contoso`)<br>- `FQDN` (例: `microsoft.com`)<br><br>[SrcDomain](#srcdomain) が使用されている場合は必須です。 |
| **SrcFQDN** | オプション | String | ソース デバイスのホスト名 (使用可能な場合はドメイン情報を含む)。 <br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [SrcDomainType](#srcdomaintype) フィールドには、使用されている形式が反映されます。 <br><br>例: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | オプション | String | レコードで報告されるソース デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | オプション | Enumerated | [SrcDvcId](#srcdvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEid`<br><br>複数の ID を使用できる場合は、上のリストの最初のものを使用し、他のものはそれぞれ **SrcDvcAzureResourceId** および **SrcDvcMDEid** に格納します。<br><br>**注**: [SrcDvcId](#srcdvcid) が使用されている場合、このフィールドは必須です。 |
| **SrcDeviceType** | オプション | Enumerated | ソース デバイスの種類。 次の値を指定できます。<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | オプション | String | ソース ユーザーの、コンピューターが判読できる英数字の一意表現。 形式とサポートされる型は次のとおりです。<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>ID の種類は、[SrcUserIdType](#srcuseridtype) フィールドに格納します。 <br><br>他の ID を使用できる場合は、フィールド名を **SrcUserSid**、**SrcUserUid**、**SrcUserAadId**、**SrcUserOktaId** および **UserAwsId** にそれぞれ正規化することをお勧めします。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | オプション | Enumerated | [SrcUserId](#srcuserid) フィールドに格納されている ID の種類。 サポートされている値: `SID`、`UIS`、`AADID`、`OktaId`、`AWSId`。 |
| <a name="srcusername"></a>**SrcUsername** | オプション | String | 使用可能な場合はドメイン情報を含む、ソースのユーザー名。 以下のいずれかの形式と、以下の優先順位を使用します。<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Simple 形式は、ドメイン情報が利用できない場合にのみ使用します。<br><br>ユーザー名の種類は、[SrcUsernameType](#srcusernametype) フィールドに格納します。 他の ID を使用できる場合は、フィールド名を **SrcUserUpn**、**SrcUserWindows**、**SrcUserDn** に正規化することをお勧めします。<br><br>詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `AlbertE` |
| <a name="user"></a>**User** | エイリアス | | [SrcUsername](#srcusername) の別名 |
| <a name="srcusernametype"></a>**SrcUsernameType** | オプション | Enumerated | [SrcUsername](#srcusername) フィールドに格納されているユーザー名の種類を指定します。 サポートされている値: `UPN`、`Windows`、`DN`、`Simple`。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `Windows` |
| **SrcUserType** | オプション | Enumerated | アクターの種類。 使用できる値は、以下のとおりです。<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [EventOriginalUserType](#eventoriginalusertype) フィールドに格納します。 |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | オプション | String | ソースによって提供されている場合、元のソース ユーザーの種類。 |
| **SrcUserDomain** | オプション | String | このフィールドは、下位互換性のためだけに残されています。 ドメイン情報が使用可能な場合、ASIM では [SrcUsername](#srcusername) フィールドの一部としてそれが必要です。 |
| <a name="srcprocessname"></a>**SrcProcessName**              | オプション     | String     |   DNS 要求を開始するプロセスのファイル名。 この名前は通常、プロセス名と見なされます。  <br><br>例: `C:\Windows\explorer.exe`  |
| <a name="process"></a>**Process**        | エイリアス        |            | [SrcProcessName](#srcprocessname) の別名 <br><br>例: `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| Mandatory    | String        | DNS 要求を開始するプロセスのプロセス ID (PID)。<br><br>例: `48610176`           <br><br>**注**: さまざまなシステムに対応するために、型は "*文字列*" として定義されますが、Windows と Linux ではこの値は数値である必要があります。 <br><br>Windows または Linux のマシンを使用しており、かつ別の型を使用した場合は、必ず値を変換してください。 たとえば、16 進数の値を使用した場合は、10 進数の値に変換します。    |
| **SrcProcessGuid**              | オプション     | String     |  DNS 要求を開始するプロセスの生成された一意識別子 (GUID)。   <br><br> 例: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**Dst** | 推奨       | String     |    DNS 要求を受信するサーバーの一意識別子。 <br><br>このフィールドは、[DstDvcId](#dstdvcid)、[DstHostname](#dsthostname)、または [DstIpAddr](#dstipaddr) フィールドの別名になる可能性があります。 <br><br>例: `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | オプション | IP アドレス | DNS 要求を受信しているサーバーの IP アドレス。 通常の DNS 要求の場合、この値は通常はレポート デバイスであり、ほとんどの場合、`127.0.0.1` に設定されます。<br><br>例: `127.0.0.1` |
| **DstGeoCountry** | オプション | 国 | ターゲット IP アドレスに関連付けられている国。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `USA` |
| **DstGeoRegion** | 省略可能 | リージョン | ターゲット IP アドレスに関連付けられた国の地域または州。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `Vermont` |
| **DstGeoCity** | オプション | City | ターゲット IP アドレスに関連付けられている都市。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `Burlington` |
| **DstGeoLatitude** | 省略可能 | Latitude | ターゲット IP アドレスに関連付けられている地理的座標の緯度。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `44.475833` |
| **DstGeoLongitude** | 省略可能 | Longitude | ターゲット IP アドレスに関連付けられている地理的座標の経度。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。<br><br>例: `73.211944` |
| **DstcRiskLevel** | オプション | Integer | 送信先に関連付けられているリスク レベル。 この値は、0 から 100 の範囲に調整する必要があります。0 は問題なし、100 は高リスクです。<br><br>例: `90` |
| **DstPortNumber** | オプション | Integer  | 送信先ポート番号。<br><br>例: `53` |
| <a name="dsthostname"></a>**DstHostname** | オプション | String | ドメイン情報を除いた、ターゲット デバイスのホスト名。 デバイス名を使用できない場合は、関連する IP アドレスをこのフィールドに格納します。<br><br>例: `DESKTOP-1282V4D`<br><br>**注**: [DstIpAddr](#dstipaddr) が指定されている場合、この値は必須です。 |
| <a name="dstdomain"></a>**DstDomain** | オプション | String | ターゲット デバイスのドメイン。<br><br>例: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | オプション | Enumerated | [DstDomain](#dstdomain) の種類 (既知の場合)。 次の値を指定できます。<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>[DstDomain](#dstdomain) が使用されている場合は必須です。 |
| **DstFQDN** | オプション | String | 使用可能な場合はドメイン情報を含む、ターゲット デバイスのホスト名。 <br><br>例: `Contoso\DESKTOP-1282V4D` <br><br>**注**: このフィールドでは、従来の FQDN 形式と Windows のドメイン\ホスト名形式の両方がサポートされています。 [DstDomainType](#dstdomaintype) フィールドには、使用されている形式が反映されます。   |
| <a name="dstdvcid"></a>**DstDvcId** | オプション | String | レコードで報告されるターゲット デバイスの ID。<br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | オプション | Enumerated | [DstDvcId](#dstdvcid) の種類 (既知の場合)。 次の値を指定できます。<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>複数の ID を使用できる場合は、上のリストの最初のものを使用し、他のものはそれぞれ **DstDvcAzureResourceId** または **DstDvcMDEid** フィールドに格納します。<br><br>**DstDeviceId** が使用されている場合は必須です。|
| **DstDeviceType** | オプション | Enumerated | ターゲット デバイスの種類。 次の値を指定できます。<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | 解決する必要があるドメイン。 <br><br>**注**: ソースによっては、このクエリが異なる形式で送信されます。 たとえば、DNS プロトコル自体では、このクエリは末尾にドット ( **.** ) が含まれているため、これを削除する必要があります。<br><br>DNS プロトコルでは 1 つの要求に複数のクエリを含めることができますが、このようなシナリオは行われるとしてもまれです。 要求に複数のクエリがある場合は、最初のものをこのフィールドに格納し、必要に応じて残りを [AdditionalFields](normalization-about-schemas.md#additionalfields) フィールドに保持します。<br><br>例: `www.malicious.com` |
| **Domain** | エイリアス | | [Query](#query) の別名。 |
| **DnsQueryType** | オプション | Integer | このフィールドには、[DNS のリソース レコードの種類コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)が含まれる場合があります。 <br><br>例: `28`|
| **DnsQueryTypeName** | 推奨 | Enumerated | このフィールドには、[DNS のリソース レコードの種類](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)の名前が含まれる場合があります。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では必要に応じて大文字と小文字を正規化する必要があります。 ソースで数値のクエリの種類コードのみが提供され、クエリの種類の名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。<br><br>例: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | オプション | String | レコードに含まれる応答の内容。<br> <br> DNS 応答データは、レポート デバイスの間で一貫性がなく、解析が複雑であり、ソースに依存しない分析にとっての価値は高くありません。 したがって、情報モデルでの解析と正規化は必要なく、Microsoft Sentinel では補助関数を使用して応答情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | [DNS 応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>**注**: IANA では値の大文字と小文字は定義されていないので、分析では大文字と小文字を正規化する必要があります。 ソースで数値の応答コードのみが提供され、応答コードの名前が提供されない場合は、パーサーでこの値をエンリッチするためにルックアップ テーブルを含める必要があります。 <br><br> このレコードが応答ではなく要求を表している場合は、**NA** に設定します。 <br><br>例: `NXDOMAIN` |
| **DnsResponseCode** | オプション | Integer | [DNS 数値応答コード](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。 <br><br>例: `3`|
| **TransactionIdHex** | 推奨 | String | DNS の一意の 16 進トランザクション ID。 |
| **NetworkProtocol** | オプション | Enumerated | ネットワーク解決イベントによって使用されるトランスポート プロトコル。 値は **UDP** または **TCP** であり、DNS の場合は **UDP** に設定されるのが普通です。 <br><br>例: `UDP`|
| **DnsQueryClass** | オプション | Integer | [DNS クラス ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。|
| **DnsQueryClassName** | オプション | String | [DNS クラスの名前](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)。<br> <br>実際には、**IN** クラス (ID 1) だけが使用されるので、このフィールドの価値は高くありません。 <br><br>例: `IN`|
| <a name=flags></a>**DnsFlags** | オプション | 文字列のリスト | レポート デバイスによって提供されるフラグ フィールド。 フラグ情報が複数のフィールドで提供されている場合は、区切り記号としてコンマを使用して連結します。 <br><br>DNS フラグは解析が複雑で、分析ではあまり使用されないので、解析と正規化は必要なく、Microsoft Sentinel では補助関数を使用してフラグ情報が提供されます。 詳細については、「[DNS の応答の処理](#handling-dns-response)」を参照してください。 <br><br>例: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  省略可能 | String | DNS イベント ソースで、要求されたドメインのカテゴリが検索されることもあります。 フィールドは、Microsoft Sentinel のネットワーク スキーマに合わせて **_UrlCategory_** と呼ばれます。 <br><br>**_DomainCategory_** は、DNS に適合する別名として追加されます。 <br><br>例: `Educational \\ Phishing` |
| **DomainCategory** | 省略可能 | エイリアス | [UrlCategory](#UrlCategory) の別名。 |
| **ThreatCategory** | 省略可能 | String | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントも評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、脅威カテゴリでドメインまたは IP アドレスに割り当られる場合があります。 |
| **EventSeverity** | オプション | String | DNS イベント ソースで DNS セキュリティも提供される場合は、DNS イベントが評価されている可能性があります。 たとえば、脅威インテリジェンス データベースで IP アドレスまたはドメインが検索され、評価に基づいて重大度が割り当られる場合があります。 <br><br>例: `Informational`|
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | オプション | Integer | DNS 要求の完了にかかる時間 (ミリ秒単位)。<br><br>例: `1500` |
| **Duration** | エイリアス | | [DnsNetworkDuration](#dnsnetworkduration) の別名 |
| **DnsFlagsAuthenticated** | 省略可能 | ブール型 | DNSSEC に関連する DNS の `AD` フラグは、応答において、応答の回答セクションと権限セクションに含まれるすべてのデータが、サーバーのポリシーに従ってそのサーバーによって検証されたことを示します。 詳細については、[RFC 3655 セクション 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) を参照してください。    |
| **DnsFlagsAuthoritative** | 省略可能 | ブール型 | DNS の `AA` フラグは、サーバーからの応答に権限があったかどうかを示します    |
| **DnsFlagsCheckingDisabled** | 省略可能 | ブール型 | DNSSEC に関連する DNS の `CD` フラグは、クエリにおいて、検証されていないデータがクエリを送信しているシステムで許容されることを示します。 詳細については、[RFC 3655 セクション 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) を参照してください。   |
| **DnsFlagsRecursionAvailable** | 省略可能 | ブール型 | DNS の `RA` フラグは、応答において、サーバーが再帰クエリをサポートすることを示します。   |
| **DnsFlagsRecursionDesired** | 省略可能 | ブール型 | DNS の `RD` フラグは、要求において、サーバーが再帰クエリを使用するのをクライアントが望むことを示します。   |
| **DnsFlagsTruncates** | 省略可能 | ブール型 | DNS の `TC` フラグは、最大応答サイズを超えたために応答が切り詰められたことを示します。  |
| **DnsFlagsZ** | 省略可能 | ブール型 | DNS の `Z` フラグは非推奨の DNS フラグであり、古い DNS システムによって報告される可能性があります。  |
|<a name="dnssessionid"></a>**DnsSessionId** | 省略可能 | string | レポート デバイスによって報告された DNS セッション識別子。 <br><br>例: `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | エイリアス | String | [DnsSessionId](#dnssessionid) の別名 |
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

### <a name="schema-updates"></a>スキーマの更新

スキーマのバージョン 0.1.2 の変更は次のとおりです。
- `EventSchema` フィールドの追加 - 現在は省略可能ですが、2022 年 1 月 1 日に必須になります。
- 結合された **[フラグ](#flags)** フィールドを拡張する専用フラグ フィールドの追加: `DnsFlagsAuthoritative`、`DnsFlagsCheckingDisabled`、`DnsFlagsRecursionAvailable`、`DnsFlagsRecursionDesired`、`DnsFlagsTruncates`、`DnsFlagsZ`。

スキーマのバージョン 0.1.3 の変更は次のとおりです。
- スキーマで `Src*`、`Dst*`、`Process*`、および `User*` のフィールドが明示的に文書化されました。
- 最新の共通フィールド定義と一致するように `Dvc*` フィールドが追加されました。 
- `Src` と `Dst` がソースと送信先のシステムの先頭の識別子に別名として追加されました。
- 省略可能な `DnsNetworkDuration` とその別名 `Duration` が追加されました。
- 省略可能な地域とリスク レベルのフィールドが追加されました。

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

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md)
- [Microsoft Sentinel データ正規化スキーマ リファレンス](normalization-schema.md)
- [Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Microsoft Sentinel プロセス イベント正規化スキーマ リファレンス](process-events-normalization-schema.md)
- [Microsoft Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)