---
title: Microsoft Azure Sentinel 認証正規化スキーマ リファレンス | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel 認証正規化スキーマについて説明します。
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
ms.openlocfilehash: 471c76128d0df5aef41e4d50f6ddcdf7f97f34ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722907"
---
# <a name="microsoft-sentinel-authentication-normalization-schema-reference-public-preview"></a>Microsoft Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

認証情報モデルは、ユーザー認証、サインイン、サインアウトに関連するイベントを記述するために使用されます。認証イベントは数多くのレポート デバイスによって、通常はイベント ストリームの一部として、他のイベントと共に送信されます。

たとえば、Windows は、いくつかの認証イベントを、他の OS アクティビティ イベントと共に送信します。 その結果、ほとんどの場合、認証イベントは異なる Microsoft Azure Sentinel テーブルに格納され、KQL 関数を使用して正規化され、さらに関連する認証イベントのみがフィルター処理されます。

認証イベントには、VPN ゲートウェイやドメイン コントローラーなどの認証に重点を置いたシステムからのイベントと、コンピューターやファイアウォールなどのエンド システムへの直接認証の両方が含まれます。

Microsoft Azure Sentinel での正規化の詳細については、「[正規化と Advanced SIEM 情報モデル (ASIM)](normalization.md)」を参照してください。

> [!IMPORTANT]
> 現在、認証正規化スキーマはプレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。
>
> [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="parsers"></a>パーサー

Microsoft Azure Sentinel には、次の組み込みの製品固有の認証イベント パーサーが用意されています。 

- セキュリティ イベント (4624、4625、4634、4647) として報告される **Windows サインイン**。Log Analytics エージェントまたは Azure Monitor エージェントを使用して収集されます。
- Microsoft 365 Defender for Endpoint によって報告される **Windows サインイン**。Microsoft 365 Defender コネクタを使用して収集されます。
- **Azure Active Directory サインイン**。Azure Active Directory コネクタを使用して収集されます。 標準、非対話型、マネージド ID、サービス プリンシパルの各サインインには、個別のパーサーが用意されています。
- **AWS サインイン**。AWS CloudTrail コネクタを使用して収集されます。
- **Okta 認証**。Okta コネクタを使用して収集されます。

一覧表示されたすべてのパーサーを統一する、ソースに依存しないパーサーを使用して、構成済みのソース全体で確実にデータを分析するには、クエリで **imAuthentication** をテーブル名として使用します。

[Microsoft Azure Sentinel の GitHub リポジトリ](https://aka.ms/AzSentinelAuth)から、[ソースに依存しないパーサーとソース固有のパーサー](normalization-about-parsers.md)をデプロイします。



## <a name="normalized-content"></a>正規化されたコンテンツ

認証 ASIM スキーマのサポートには、正規化された認証パーサーを使用した次の組み込み分析規則のサポートも含まれています。 Microsoft Sentinel GitHub リポジトリへのリンクは下に参照として掲載していますが、これらのルールは [Microsoft Sentinel Analytics ルール ギャラリー](detect-threats-built-in.md)でも見つけることができます。 リンクされた GitHub ページを使用して、一覧のルールに関連するハンティング クエリをコピーします。

- [Potential Password Spray Attack (Uses Authentication Normalization) (潜在的なパスワード スプレー攻撃 (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Brute force attack against user credentials (Uses Authentication Normalization) (ユーザー資格情報に対するブルート フォース攻撃 (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [User login from different countries within 3 hours (Uses Authentication Normalization) (さまざまな国からの 3 時間以内のユーザー ログイン (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Sign-ins from IPs that attempt sign-ins to disabled accounts (Uses Authentication Normalization) (無効なアカウントへのサインインを試行する IP からのサインイン (認証正規化を使用))](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


正規化された認証分析規則は、複数のソースにまたがる攻撃を検出する点において、独特です。 そのため、たとえば、ユーザーが別の国から、関連性のない異なるシステムにログインした場合、Microsoft Azure Sentinel によってこの脅威が検出されるようになります。

## <a name="schema-details"></a>スキーマの詳細

認証情報モデルは、[OSSEM ログオン エンティティ スキーマ](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md)に合わせて調整されています。

以降の表では、"*型*" は論理型を指しています。 詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。

### <a name="log-analytics-fields"></a>Log Analytics のフィールド

次のフィールドは、各レコードごとに Log Analytics によって生成されますが、カスタム コネクタの作成時にオーバーライドできます。

|フィールド  |型  |説明  |
|---------|---------|---------|
|<a name ="timegenerated"></a>**TimeGenerated**     |  DATETIME       |イベントがレポート デバイスによって生成された時刻。         |
|**_ResourceId**     | guid        |  レポート デバイスまたはサービスの Azure リソース ID。Syslog、CEF、WEF を使用して転送されたイベントの場合はログ フォワーダー リソース ID。       |
| **Type** | String | レコードがフェッチされた元のテーブル。 このフィールドは、同じイベントを複数のチャネルを通じて異なるテーブルで受信できるときに、同じ EventVendor 値と EventProduct 値を設定する場合に役に立ちます。<br><br>たとえば、Sysmon イベントを、イベント テーブルにも WindowsEvent テーブルにも収集できます。 |
|     |         |         |

> [!NOTE]
> Log Analytics では、セキュリティのユース ケースとあまり関連しない他のフィールドも追加します。 詳細については、「[Azure Monitor ログ内の標準列](../azure-monitor/logs/log-standard-columns.md)」を参照してください。
>

### <a name="event-fields"></a>イベント フィールド

イベント フィールドは、すべてのスキーマに共通であり、アクティビティ自体とレポート デバイスを記述します。

| フィールド               | クラス       | 型       |  説明        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | オプション    | String     |     レコードに含まれるか、レコードから生成された一般的なメッセージまたは説明。   |
| **EventCount**          | Mandatory   | Integer    |     レコードによって記述されるイベントの数。 <br><br>この値は、ソースが集計に対応しており、1 つのレコードが複数のイベントを表す場合があるときに使用されます。 <br><br>その他のソースの場合は、`1` に設定します。 <br><br>**注**: このフィールドは一貫性のために含まれていますが、通常は認証イベントには使用されません。  |
| **EventStartTime**      | Mandatory   | 日付/時刻  |      ソースが集計に対応しており、レコードが複数のイベントを表す場合、このフィールドでは最初にイベントが生成された時間を指定します。 それ以外の場合、このフィールドは [TimeGenerated](#timegenerated) フィールドの別名になります。<br><br>**注**: このフィールドは一貫性のために含まれていますが、通常は認証イベントには使用されません。  |
| **EventEndTime**        | Mandatory   | エイリアス      |      [TimeGenerated](#timegenerated) フィールドの別名。    |
| **EventType**           | Mandatory   | Enumerated |    レコードによってレポートされる操作を記述します。 <br><br>認証レコードの場合、サポートされる値は次のとおりです。 <br>- `Logon` <br>- `Logoff`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 元の値は [EventOriginalType](#eventoriginaltype) フィールドに格納する必要があります。|
| <a name ="eventoriginaltype"></a>**EventOriginalType**           | オプション   | String |    ソース レコードに提供されているイベントの種類 (ID)。 <br><br>例: `4625`|
| **EventResult**         | Mandatory   | Enumerated |  イベントの結果を記述し、次のサポートされている値の 1 つに正規化されます。 <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (適用外) <br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。 <br><br>ソースから [EventResultDetails](#eventresultdetails) フィールドの値のみが提供される場合もあり、それを分析しなければ **EventResult** の値は取得できません。 |
| <a name ="eventresultdetails"></a>**EventResultDetails**         | オプション   | String |  次のいずれかの値です。 <br><br>-  `No such user or password`. この値は、元のイベントが、パスワードへの参照なしでそのようなユーザーがいないことを報告するときにも使用する必要があります。 <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. この値は、元のイベントが、たとえば、MFA が必要なこと、勤務時間外のログオン、条件付きアクセス制限、過剰な試行回数などを報告するときに使用する必要があります。<br>-  `Session expired`<br>-  `Other`<br><br>**注**: 値は、異なる用語を使用してソース レコードに提供されている場合があり、これらの値に正規化する必要があります。|
| **EventSubType**    | オプション    | String     |   サインインの種類。通常は、Windows ログオンの種類に使用します。 <br><br>例: `Interactive`|
| **EventOriginalResultDetails**    | オプション    | String     |  ソースによって提供されている場合、[EventResultDetails](#eventresultdetails) の元のレコードに提供された値。|
| **EventOriginalUid**    | オプション    | String     |   ソースによって提供されている場合、元のレコードの一意の ID。|
| **EventOriginalType**   | オプション    | String     |   元のイベントの種類または ID (ソースによって提供されている場合)。<br><br>例: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             イベントを生成している製品。 <br><br>例: `Windows`<br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。           |
| **EventProductVersion** | オプション    | String     | イベントを生成している製品のバージョン。 <br><br>例: `10` <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。     |
| **EventVendor**         | Mandatory   | String     |           イベントを生成している製品のベンダー。 <br><br>例: `Microsoft`  <br><br>**注**: このフィールドはソース レコードでは使用できない場合があります。 その場合、このフィールドはパーサーによって設定される必要があります。  |
| **EventSchemaVersion**  | Mandatory   | String     |    スキーマのバージョン。 ここに記載されているスキーマのバージョンは `0.1` です         |
| **EventReportUrl**      | オプション    | String     | あるリソースのイベントに提供された、そのイベントに関する追加情報を提供する URL。|
| <a name ="dvc"></a>**Dvc** | Mandatory       | String     |              イベントが発生したデバイスの一意の識別子。 <br><br>このフィールドの別名は、[DvcId](#dvcid)、[DvcHostname](#dvchostname)、または [DvcIpAddr](#dvcipaddr) フィールドになる場合があります。 明確なデバイスがないクラウド リソースの場合は、[Event Product](#eventproduct) フィールドと同じ値を使用します。             |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 推奨 | IP アドレス |         プロセス イベントが発生したデバイスの IP アドレス。  <br><br>例: `45.21.42.12`  <br><br>**注**: 識別子が使用可能である一方で型が不明な場合は、このフィールドを使用しないでください。 詳しくは、[Dvc](#dvc) を参照してください  |
| <a name ="dvchostname"></a>**DvcHostname**         | 推奨 | Hostname (ホスト名)   |               プロセス イベントが発生したデバイスのホスト名。 <br><br>例: `ContosoDc.Contoso.Azure`      <br><br>**注**: 識別子が使用可能である一方で型が不明な場合は、このフィールドを使用しないでください。 詳しくは、[Dvc](#dvc) を参照してください          |
| <a name ="dvcid"></a>**DvcId**               | オプション    | String     |  プロセス イベントが発生したデバイスの一意の ID。 <br><br>例: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | オプション    | 動的    | ソースから保持する必要のある追加情報が提供される場合は、元のフィールド名をそのまま使用するか、動的な **AdditionalFields** フィールドを作成し、追加情報をキーと値のペアとして追加します。    |
| | | | |


## <a name="authentication-specific-fields"></a>認証固有のフィールド

以下の表に示すフィールドは認証イベントに固有ですが、他のスキーマのフィールドに類似しており、同様の名前付け規則に従います。

認証イベントは、次のエンティティを参照します。

- **俳優**
- **ActingApp**
- **SrcDvc**
- **TargetUser**
- **TargetApp**
- **TargetDvc**

これらのエンティティ間のリレーションシップは、次のように示されます。

"*ソース デバイス*" (**SrcDvc**) 上の "*代理アプリケーション*" (**ActingApp**) を実行している **アクター** は、"*ターゲット デバイス*" (**TargetDvc**) 上の "*ターゲット アプリケーション*" (**TargetApp**) に対する **TargetUser** の認証を試みます。

| フィールド          | クラス        | 型       | 説明   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |オプション |String |認証を実行するために使用されるメソッド。 <br><br>例: `Username & Password` |
|**LogonProtocol** |オプション |String |認証を実行するために使用されるプロトコル。 <br><br>例: `NTLM` |
| <a name="actoruserid"></a>**ActorUserId**    | オプション  | UserId     |   コンピューターが判読できる、英数字で、アクターを一意に表現したもの。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。  <br><br>例: `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| 省略可能  | UserIdType     |  [ActorUserId](#actoruserid) フィールドに格納されている ID の種類。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。         |
| <a name="actorusername"></a>**ActorUsername**  | 省略可能    | ユーザー名     | アクターのユーザー名 (使用可能な場合はドメイン情報を含む)。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。<br><br>例: `AlbertE`     |
| **ActorUsernameType**              | オプション    | UsernameType |   [ActorUsername](#actorusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。 <br><br>例: `Windows`       |
| **ActorUserType** | オプション | String | アクターの種類。 <br><br>例: `Guest` |
| **ActorSessionId** | オプション     | String     |   アクターのサインイン セッションの一意の ID。  <br><br>例: `102pTUgC3p8RIqHvzxLCHnFlg`  |
| **ActingAppId** | オプション | String | アクターに代わって承認するアプリケーション (プロセス、ブラウザー、またはサービスを含む) の ID。 <br><br>例: `0x12ae8` |
| **ActiveAppName** | オプション | String | アクターに代わって承認するアプリケーション (プロセス、ブラウザー、またはサービスを含む) の名前。 <br><br>例: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | オプション | Enumerated | 代理アプリケーションの種類。 サポートされている値は次のとおりです。 <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |   オプション    | String |  HTTP または HTTPS で認証が行われる場、このフィールドの値は、認証を実行するときに代理アプリケーションによって提供される user_agent HTTP ヘッダーです。<br><br>例: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a> **TargetUserId**   | オプション | UserId     | コンピューターが判読できる、英数字で、ターゲット ユーザーを一意に表現したもの。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。            <br><br> 例: `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType**               | 省略可能 | UserIdType     | [TargetUserId](#targetuserid) フィールドに格納されているユーザー ID の種類。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。            <br><br> 例: `SID`  |
| <a name="targetusername"></a>**TargetUsername** | オプション | ユーザー名     | ターゲット ユーザーのユーザー名 (使用可能な場合はドメイン情報を含む)。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。  <br><br>例: `MarieC`      |
| **TargetUsernameType**             |オプション  | UsernameType | [TargetUsername](#targetusername) フィールドに格納されているユーザー名の種類を指定します。 詳細については、「[ユーザー エンティティ](normalization-about-schemas.md#the-user-entity)」を参照してください。          |
| **TargetUserType** | オプション | String | ターゲット ユーザーの型です。 <br><br>例: `Member` |
| **TargetSessionId** | オプション | String | ソース デバイス上の TargetUser のサインイン セッション識別子。 |
| **User**           | エイリアス        |     String       | [TargetUsername](#targetusername) の別名。または、[TargetUsername](#targetusername) が定義されていない場合は [TargetUserId](#targetuserid) の別名。 <br><br>例: `CONTOSO\dadmin`     |
|**SrcDvcId** |オプション |String |レコードで報告されるソース デバイスの ID。 <br><br>例: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>**SrcDvcHostname** |オプション | Hostname (ホスト名)| ソース デバイスのホスト名 (使用可能な場合はドメイン情報を含む)。 詳細については、「[デバイス エンティティ](normalization-about-schemas.md#the-device-entity)」を参照してください。 <br><br>例: `Constoso\DESKTOP-1282V4D`|
| **SrcDvcHostnameType**|省略可能 |HostnameType |[SrcDvcHostname](#srcdvchostname) の型 (既知の場合)。 詳細については、「[デバイス エンティティ](normalization-about-schemas.md#the-device-entity)」を参照してください。 |
|**SrcDvcType** |オプション |Enumerated |ソース デバイスの種類。 次の値を指定できます。 <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|推奨 |IP アドレス |ソース デバイスの IP アドレス。 <br><br>例: `185.175.35.214` |
| **SrcDvcOs**|オプション |String |ソース デバイスの OS。 <br><br>例: `Windows 10` |
|**SrcIsp** | オプション|String |ソース デバイスがインターネットに接続するために使用するインターネット サービス プロバイダー (ISP)。 <br><br>例: `corpconnect` |
| **SrcGeoCountry**|オプション |国 |例: `Canada` <br><br>詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。 |
| **SrcGeoCity**|オプション |City |例: `Montreal` <br><br>詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。 |
|**SrcGeoRegion** | 省略可能|リージョン | 例: `Quebec` <br><br>詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。|
| **SrcGeoLongtitude**|省略可能 |Longitude  | 例: `-73.614830` <br><br>詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。|
| **SrcGeoLatitude**|省略可能 |Latitude |例: `45.505918` <br><br>詳細については、「[論理型](normalization-about-schemas.md#logical-types)」を参照してください。 |
|**TargetAppId** |オプション | String| 認可を必要とするアプリケーションの ID。多くの場合、レポート デバイスによって割り当てられます。 <br><br>例: `89162` |
|<a name="targetappname"></a>**TargetAppName** |オプション |String |認可を必要とするアプリケーションの名前 (サービス、URL、SaaS アプリケーションなど)。 <br><br>例: `Saleforce` |
| **TargetAppType**|オプション |String |アクターに代わって承認するアプリケーションの種類。 サポートされている値は次のとおりです。  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|**TargetUrl** |オプション |String |ターゲット アプリケーションに関連する URL。 <br><br>例: `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| エイリアス| |[TargetAppName](#targetappname)、*URL*、または [TargetDvcHostname](#targetdvchostname) のいずれかで、認証ターゲットを最もよく表すフィールドの別名。 |
|**TargetDvcId** |オプション | String|レコードで報告されるターゲット デバイスの ID。 <br><br> 例: `2739` |
|<a name="targetdvchostname"></a>**TargetDvcHostname** | 推奨| String|ターゲット デバイスのホスト名 (使用可能な場合はドメイン情報を含む)。 詳細については、「[デバイス エンティティ](normalization-about-schemas.md#the-device-entity)」を参照してください。 |
| **TargetDvcHostnameType**|推奨 | String|[TargetDvcHostname](#targetdvchostname) の型。 詳細については、「[デバイス エンティティ](normalization-about-schemas.md#the-device-entity)」を参照してください。 |
|**TargetDvcType** |オプション | Enumerated|ターゲット デバイスの種類。 サポートされている値は次のとおりです。 <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>**TargetDvcIpAddr** |オプション | IP アドレス|ターゲット デバイスの IP アドレス。 <br><br>例: `2.2.2.2` |
|**TargetDvc** |エイリアス | |   ターゲット デバイスの一意の識別子。 <br><br>特定のソースに対して最も適切な値の別名として、[TargetDvcHostname](#targetdvchostname)、 [TargetDvcIpAddr](#targetdvcipaddr)、または別の ID (より適切な場合) を選択します。 |
| **TargetDvcOs**| オプション| String| ターゲット デバイスの OS。 <br><br>例: `Windows 10`|
| **TargetPortNumber**|オプション |Integer |ターゲット デバイスのポート。|
| | | | |




## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [Microsoft Sentinel での正規化](normalization.md)
- [Microsoft Azure Sentinel DNS 正規化スキーマ リファレンス](dns-normalization-schema.md)
- [Microsoft Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [Microsoft Azure Sentinel ネットワーク正規化スキーマ リファレンス](./network-normalization-schema.md)
- [Microsoft Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)
