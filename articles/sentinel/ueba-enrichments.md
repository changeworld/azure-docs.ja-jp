---
title: Microsoft Sentinel UEBA エンリッチメント リファレンス | Microsoft Docs
description: この記事では、Microsoft Sentinel のエンティティ行動分析によって生成されるエンティティ エンリッチメントを示します。
author: yelevin
manager: rkarlin
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 843e1eafa02687cefddf310c467cfd9a56717f5d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711402"
---
# <a name="microsoft-sentinel-ueba-enrichments-reference"></a>Microsoft Sentinel UEBA エンリッチメント リファレンス

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、 **[ログ]** 内にあり、[エンティティの詳細ページ](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)で言及される Microsoft Sentinel **BehaviorAnalytics** テーブルと、そのテーブル内のエンティティ エンリッチメント フィールドの詳細について説明します。この内容を活用することにより、セキュリティ インシデントの調査に焦点を当てて明確にすることができます。

BehaviorAnalytics テーブルに含まれる以下の 3 つの動的フィールドについて、[下の表](#entity-enrichments-dynamic-fields)で説明します。

[UsersInsights](#usersinsights-field) および [DevicesInsights](#devicesinsights-field) フィールドには、Active Directory または Azure AD および Microsoft 脅威インテリジェンス ソースのエンティティ情報が格納されます。

[ActivityInsights](#activityinsights-field) フィールドには、Microsoft Sentinel のエンティティ行動分析によって作成される行動プロファイルに基づくエンティティ情報が格納されます。 

<a name="baseline-explained"></a>ユーザー アクティビティは、使用されるたびに動的にコンパイルされるベースラインに対して分析されます。 各アクティビティには、この動的なベースラインの派生元であるルックバック期間が定義されています。 このルックバック期間は、この表の「[**ベースライン**](#activityinsights-field)」列で指定されています。

> [!NOTE]
> すべての [エンティティ エンリッチメント フィールド](#entity-enrichments-dynamic-fields)表の「**エンリッチメント名**」列には、2 行の情報が表示されます。 
>
> - 1 行目には、エンリッチメントの "フレンドリ名" が **太字** で示されています。
> - " *(斜体とかっこ)* " で示された 2 行目は、「[**行動分析テーブル**](#behavioranalytics-table)」に格納されるエンリッチメントのフィールド名です。

> [!IMPORTANT]
> 記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>
## <a name="behavioranalytics-table"></a>BehaviorAnalytics テーブル

次の表に、Microsoft Sentinel の各[エンティティの詳細ページ](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages)に表示される行動分析データを示します。

| フィールド                     | 型 | 説明                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | string | テナントの一意の ID 番号。                             |
| **SourceRecordId**        | string | EBA イベントの一意の ID 番号。                          |
| **TimeGenerated**         | DATETIME | アクティビティの発生のタイムスタンプ。                   |
| **TimeProcessed**         | DATETIME | EBA エンジンによるアクティビティの処理のタイムスタンプ。 |
| **ActivityType**          | string | アクティビティの高レベルのカテゴリ。                        |
| **ActionType**            | string | アクティビティの標準化名。                            |
| **UserName**              | string | アクティビティを開始したユーザーのユーザー名。           |
| **UserPrincipalName**     | string | アクティビティを開始したユーザーの完全なユーザー名。      |
| **EventSource**           | string | 元のイベントを提供したデータ ソース。               |
| **SourceIPAddress**       | string | アクティビティが開始された元の IP アドレス。               |
| **SourceIPLocation** | string | アクティビティが開始された元の国 (IP アドレスからエンリッチ処理済み)。 |
| **SourceDevice**          | string | アクティビティを開始したデバイスのホスト名。         |
| **DestinationIPAddress**  | string | アクティビティのターゲットの IP アドレス。                   |
| **DestinationIPLocation** | string | アクティビティのターゲットの国 (IP アドレスからエンリッチ処理済み)。 |
| **DestinationDevice**     | string | ターゲット デバイスの名前。                                  |
| **UsersInsights**         | 動的 | 関連するユーザーのコンテキスト エンリッチメント ([詳細は後述](#usersinsights-field))。 |
| **DevicesInsights**       | 動的 | 関連するデバイスのコンテキスト エンリッチメント ([詳細は後述](#devicesinsights-field))。 |
| **ActivityInsights**      | 動的 | プロファイリングに基づくアクティビティのコンテキスト分析 ([詳細は後述](#activityinsights-field))。 |
| **InvestigationPriority** | INT | 0 から 10 の異常スコア (0 = 無害、10 = きわめて異常)。   |



## <a name="entity-enrichments-dynamic-fields"></a>エンティティ エンリッチメントの動的フィールド

### <a name="usersinsights-field"></a>UsersInsights フィールド

次の表では、BehaviorAnalytics テーブルの **UsersInsights** 動的フィールドで使用されるエンリッチメントについて説明します。

| エンリッチメント名 | 説明 | 値の例 |
| --- | --- | --- |
| **Account display name** (アカウントの表示名)<br>*(AccountDisplayName)* | ユーザーのアカウント表示名。 | Admin、Hayden Cook |
| **アカウントのドメイン**<br>*(AccountDomain)* | ユーザーのアカウント ドメイン名。 |  |
| **Account object ID** (アカウント オブジェクト ID)<br>*(AccountObjectID)* | ユーザーのアカウント オブジェクト ID。 | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **爆発半径**<br>*(BlastRadius)* | 影響範囲は、組織ツリー内でのユーザーの位置や、ユーザーの Azure Active Directory のロールとアクセス許可など、いくつかの要因に基づいて計算されます。 | 低、中、高 |
| **Is dormant account** (非アクティブ アカウント)<br>*(IsDormantAccount)* | アカウントは過去 180 日間使用されていません。 | True、False |
| **Is local admin** (ローカル管理者)<br>*(IsLocalAdmin)* | アカウントにはローカル管理者特権があります。 | True、False |
| **Is new account** (新しいアカウント)<br>*(IsNewAccount)* | アカウントは過去 30 日以内に作成されました。 | True、False |
| **On premises SID** (オンプレミス SID)<br>*(OnPremisesSID)* | アクションに関連するユーザーのオンプレミスの SID。 | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>DevicesInsights フィールド

次の表では、BehaviorAnalytics テーブルの **DevicesInsights** 動的フィールドで使用されるエンリッチメントについて説明します。

| エンリッチメント名 | 説明 | 値の例 |
| --- | --- | --- |
| **ブラウザー**<br>*(Browser)* | アクションで使用されたブラウザー。 | Microsoft Edge、Chrome |
| **デバイス ファミリ**<br>*(DeviceFamily)* | アクションで使用されたデバイス ファミリ。 | Windows |
| **Device type** (デバイスの種類)<br>*(DeviceType)* | アクションで使用されたクライアント デバイスの種類 | デスクトップ |
| **ISP**<br>*(ISP)* | アクションで使用されたインターネット サービス プロバイダー。 |  |
| **オペレーティング システム**<br>*(OperatingSystem)* | アクションで使用されたオペレーティング システム。 | Windows 10 |
| **Threat intel indicator description** (脅威インテリジェンス インジケーターの説明)<br>*(ThreatIntelIndicatorDescription)* | アクションで使用された IP アドレスから解決された監視対象の脅威インジケーターの説明。 | Host is member of botnet: azorult (ホストはボットネット azorult のメンバーである) |
| **Threat intel indicator type** (脅威インテリジェンス インジケーターの種類)<br>*(ThreatIntelIndicatorType)* | アクションで使用された IP アドレスから解決された脅威インジケーターの種類。 | Botnet、C2、CryptoMining、Darknet、Ddos、MaliciousUrl、Malware、Phishing、Proxy、PUA、Watchlist |
| **User agent**<br>*(UserAgent)* | アクションで使用されたユーザー エージェント。 | Microsoft Azure Graph Client Library 1.0、<br>Swagger-Codegen/1.4.0.0/csharp、<br>EvoSTS |
| **User agent family** (ユーザー エージェント ファミリ)<br>*(UserAgentFamily)* | アクションで使用されたユーザー エージェント ファミリ。 | Chrome、Microsoft Edge、Firefox |
|

### <a name="activityinsights-field"></a>ActivityInsights フィールド

次の表では、BehaviorAnalytics テーブルの **ActivityInsights** 動的フィールドで使用されるエンリッチメントについて説明します。

#### <a name="action-performed"></a>実行されたアクション

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user performed action** (ユーザーによる初めてのアクションの実行)<br>*(FirstTimeUserPerformedAction)* | 180 | そのユーザーはそのアクションを初めて実行しました。 | True、False |
| **Action uncommonly performed by user** (ユーザーによってあまり実行されないアクション)<br>*(ActionUncommonlyPerformedByUser)* | 10 | そのユーザーはそのアクションをあまり実行しません。 | True、False |
| **Action uncommonly performed among peers** (同僚によってあまり実行されないアクション)<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | ユーザーの同僚はそのアクションをあまり実行しません。 | True、False |
| **First time action performed in tenant** (テナントでの初めてのアクションの実行)<br>*(FirstTimeActionPerformedInTenant)* | 180 | 組織内の誰かが、そのアクションを初めて実行しました。 | True、False |
| **Action uncommonly performed in tenant** (テナントであまり実行されないアクション)<br>*(ActionUncommonlyPerformedInTenant)* | 180 | その組織ではそのアクションをあまり実行しません。 | True、False |
|

#### <a name="app-used"></a>使用されたアプリ

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user used app** (ユーザーによる初めてのアプリの使用)<br>*(FirstTimeUserUsedApp)* | 180 | そのユーザーはそのアプリを初めて使用しました。 | True、False |
| **App uncommonly used by user** (ユーザーによってあまり使用されないアプリ)<br>*(AppUncommonlyUsedByUser)* | 10 | そのユーザーはそのアプリをあまり使用しません。 | True、False |
| **App uncommonly used among peers** (同僚によってあまり使用されないアプリ)<br>*(AppUncommonlyUsedAmongPeers)* | 180 | ユーザーの同僚はそのアプリをあまり使用しません。 | True、False |
| **First time app observed in tenant** (テナントでのアプリの初めての観察)<br>*(FirstTimeAppObservedInTenant)* | 180 | そのアプリは、その組織で初めて観察されました。 | True、False |
| **App uncommonly used in tenant** (テナントであまり使用されないアプリ)<br>*(AppUncommonlyUsedInTenant)* | 180 | そのアプリはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="browser-used"></a>使用されたブラウザー

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected via browser** (ユーザーによるブラウザーでの初めての接続)<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | そのユーザーによるそのブラウザーの使用が初めて観察されました。 | True、False |
| **Browser uncommonly used by user** (ユーザーによってあまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedByUser)* | 10 | そのユーザーはそのブラウザーをあまり使用しません。 | True、False |
| **Browser uncommonly used among peers** (同僚によってあまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | ユーザーの同僚はそのブラウザーをあまり使用しません。 | True、False |
| **First time browser observed in tenant** (テナントでのブラウザーの初めての観察)<br>*(FirstTimeBrowserObservedInTenant)* | 30 | そのブラウザーは、その組織で初めて観察されました。 | True、False |
| **Browser uncommonly used in tenant** (テナントであまり使用されないブラウザー)<br>*(BrowserUncommonlyUsedInTenant)* | 30 | そのブラウザーはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="country-connected-from"></a>接続元の国

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected from country** (ユーザーが初めて接続してきた国)<br>*(FirstTimeUserConnectedFromCountry)* | 90 | IP アドレスからの解決で、その地域のユーザーが初めて接続しました。 | True、False |
| **Country uncommonly connected from by user** (ユーザーがあまり接続してこない国)<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | IP アドレスからの解決で、その地域のユーザーはあまり接続してきません。 | True、False |
| **Country uncommonly connected from among peers** (同僚があまり接続してこない国)<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | IP アドレスからの解決で、その地域のユーザーの同僚はあまり接続してきません。 | True、False |
| **First time connection from country observed in tenant** (テナントで観察された初めて接続してきた国)<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | 組織内の誰かが、その国から初めて接続しました。 | True、False |
| **Country uncommonly connected from in tenant** (テナントであまり接続されない国)<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | IP アドレスからの解決で、テナントはその地域からあまり接続されません。 | True、False |
| 

#### <a name="device-used-to-connect"></a>接続に使用されたデバイス

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected from device** (ユーザーが初めて接続してきたデバイス)<br>*(FirstTimeUserConnectedFromDevice)* | 30 | そのユーザーはそのソース デバイスから初めて接続しました。 | True、False |
| **Device uncommonly used by user** (ユーザーによってあまり使用されないデバイス)<br>*(DeviceUncommonlyUsedByUser)* | 10 | そのユーザーはそのデバイスをあまり使用しません。 | True、False |
| **Device uncommonly used among peers** (同僚によってあまり使用されないデバイス)<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | ユーザーの同僚はそのデバイスをあまり使用しません。 | True、False |
| **First time device observed in tenant** (テナントでのデバイスの初めての観察)<br>*(FirstTimeDeviceObservedInTenant)* | 30 | そのデバイスは、その組織で初めて観察されました。 | True、False |
| **Device uncommonly used in tenant** (テナントであまり使用されないデバイス)<br>*(DeviceUncommonlyUsedInTenant)* | 180 | そのデバイスはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="other-device-related"></a>その他のデバイス関連

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user logged on to device** (ユーザーによるデバイスへの初めてのログオン)<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | そのユーザーはそのターゲット デバイスに初めて接続しました。 | True、False |
| **Device family uncommonly used in tenant** (テナントであまり使用されないデバイス ファミリ)<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | そのデバイス ファミリはその組織ではあまり使用されません。 | True、False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>接続に使用されたインターネット サービス プロバイダー

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user connected via ISP** (ユーザーによる ISP での初めての接続)<br>*(FirstTimeUserConnectedViaISP)* | 30 | そのユーザーによるその ISP の使用が初めて観察されました。 | True、False |
| **ISP uncommonly used by user** (ユーザーによってあまり使用されない ISP)<br>*(ISPUncommonlyUsedByUser)* | 10 | そのユーザーはその ISP をあまり使用しません。 | True、False |
| **ISP uncommonly used among peers** (同僚によってあまり使用されない ISP)<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | ユーザーの同僚はその ISP をあまり使用しません。 | True、False |
| **First time connection via ISP in tenant** (テナントでの ISP 経由による初めての接続)<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | その ISP は、その組織で初めて観察されました。 | True、False |
| **ISP uncommonly used in tenant** (テナントであまり使用されない ISP)<br>*(ISPUncommonlyUsedInTenant)* | 30 | その ISP はその組織ではあまり使用されません。 | True、False |
| 

#### <a name="resource-accessed"></a>アクセス先のリソース

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **First time user accessed resource** (ユーザーによるリソースへの初めてのアクセス)<br>*(FirstTimeUserAccessedResource)* | 180 | そのリソースはそのユーザーによって初めてアクセスされました。 | True、False |
| **Resource uncommonly accessed by user** (ユーザーがあまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedByUser)* | 10 | そのユーザーはそのリソースにあまりアクセスしません。 | True、False |
| **Resource uncommonly accessed among peers** (同僚があまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | ユーザーの同僚はそのリソースにあまりアクセスしません。 | True、False |
| **First time resource accessed in tenant** (テナントでのリソースへの初めてのアクセス)<br>*(FirstTimeResourceAccessedInTenant)* | 180 | 組織内の誰かが、そのリソースに初めてアクセスしました。 | True、False |
| **Resource uncommonly accessed in tenant** (テナントがあまりアクセスしないリソース)<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | その組織はそのリソースにあまりアクセスしません。 | True、False |
| 

#### <a name="miscellaneous"></a>その他

| エンリッチメント名 | [ベースライン](#baseline-explained) (日数) | 説明 | 値の例 |
| --- | --- | --- | --- |
| **Last time user performed action** (ユーザーによる最後のアクションの実行)<br>*(LastTimeUserPerformedAction)* | 180 | ユーザーが同じアクションを最後に実行した日時。 | \<Timestamp\> |
| **Similar action wasn't performed in the past** (過去に類似のものが実行されたことのないアクション)<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | 同じリソース プロバイダー内に、そのユーザーによって実行されたアクションはありません。 | True、False |
| **Source IP location** (ソース IP の場所)<br>*(SourceIPLocation)* | *N/A* | アクションのソース IP から解決された国。 | [Surrey、England] |
| **Uncommon high volume of operations** (一般的でない大量の操作)<br>*(UncommonHighVolumeOfOperations)* | 7 | ユーザーが同じプロバイダー内で類似の操作を大量に実行しました。 | True、False |
| **Unusual number of Azure AD conditional access failures** (Azure AD 条件付きアクセスの異常な回数の失敗)<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | 条件付きアクセスにより、異常な数のユーザーが認証に失敗しました | True、False |
| **Unusual number of devices added** (異常な数のデバイスの追加)<br>*(UnusualNumberOfDevicesAdded)* | 5 | ユーザーが異常な数のデバイスを追加しました。 | True、False |
| **Unusual number of devices deleted** (異常な数のデバイスの削除)<br>*(UnusualNumberOfDevicesDeleted)* | 5 | ユーザーが異常な数のデバイスを削除しました。 | True、False |
| **Unusual number of users added to group** (異常な数のユーザーのグループへの追加)<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | ユーザーが異常な数のユーザーをグループに追加しました。 | True、False |
|


## <a name="identityinfo-table-public-preview"></a>IdentityInfo テーブル (パブリック プレビュー)

Microsoft Sentinel ワークスペースの [UEBA を有効](enable-entity-behavior-analytics.md)にした後、Azure Active Directory のデータが、Microsoft Sentinel で使用するために Log Analytics の **IdentityInfo** テーブルに同期されます。 Azuer AD から同期されたユーザー データを分析ルールに埋め込み、ユース ケースに合うように分析を強化して、擬陽性を減らすことができます。

初期同期には数日かかる可能性がありますが、データは一度で完全に同期されます。

- Azure AD でユーザー プロファイルに対して行われた変更は、15 分以内に **IdentityInfo** テーブルで更新されます。

- グループとロールの情報は、**IdentityInfo** テーブルと Azure AD の間で毎日同期されます。

- 古いレコードが完全に更新されるように、Microsoft Sentinel では 21 日ごとに Azure AD 全体と同期し直します。

- **IdentityInfo** テーブルの既定の保有期間は 30 日です。


> [!NOTE]
> 現在サポートされているのは組み込みロールだけです。
>
> 削除されたグループ (ユーザーがグループから削除された場所) についてのデータは、現在サポートされていません。
>

次の表は、Log Analytics の **IdentityInfo** テーブルに含まれているユーザー ID データを示しています。

| フィールド                      | 型     | 説明                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | string   | アカウントの Azure AD セキュリティ識別子。       |
| **AccountCreationTime**         | DATETIME | ユーザー アカウントが作成された日付 (UTC)。    |
| **AccountDisplayName**          | string   | ユーザー アカウントの表示名。           |
| **AccountDomain**               | string   | ユーザー アカウントのドメイン名。  |
| **AccountName**                 | string   | ユーザー アカウントのユーザー名。      |
| **AccountObjectId**             | string   | ユーザー アカウントの Azure Active Directory オブジェクト ID。           |
| **AccountSID**                  | string   | ユーザー アカウントのオンプレミス セキュリティ識別子。      |
| **AccountTenantId**             | string   | ユーザー アカウントの Azure Active Directory テナント ID。    |
| **AccountUPN**                  | string   | ユーザー アカウントのユーザー プリンシパル名。     |
| **AdditionalMailAddresses**     | 動的  | ユーザーの追加のメール アドレス。   |
| **AssignedRoles**               | 動的  | ユーザー アカウントが割り当てられた Azure AD ロール。    |
| **City (市)**                        | string   | ユーザー アカウントの市。   |
| **Country (国)**                     | string   | ユーザー アカウントの国。   |
| **DeletedDateTime**             | DATETIME | ユーザーが削除された日時。       |
| **部門**                  | string   | ユーザー アカウントの部門。    |
| **GivenName**                   | string   | ユーザー アカウントの名。     |
| **GroupMembership**             | 動的  | ユーザー アカウントがメンバーになっている Azure AD グループ。      |
| **IsAccountEnabled**            | bool     | ユーザー アカウントが Azure AD で有効になっているかどうかの表示。    |
| **JobTitle**                    | string   | ユーザー アカウントの役職。    |
| **MailAddress**                 | string   | ユーザー アカウントのプライマリ メール アドレス。    |
| **マネージャー**                     | string   | ユーザー アカウントのマネージャーの別名。     |
| **OnPremisesDistinguishedName** | string   | Azure AD の識別名 (DN)。 識別名は、コンマで接続された相対識別名 (RDN) のシーケンスです。 |
| **電話**                       | string   | ユーザー アカウントの電話番号。       |
| **SourceSystem**                | string   | ユーザー データの生成元のシステム。   |
| **State**                       | string   | ユーザー アカウントの地理的な状態。  |
| **StreetAddress**               | string   | ユーザー アカウントのオフィスの番地。    |
| **Surname**                     | string   | ユーザーの姓名の姓。 アカウント。      |
| **TenantId**                    | string   |          ユーザーのテナント ID。   |
| **TimeGenerated**               | DATETIME | イベントが生成された時刻 (UTC)。       |
| **Type**                        | string   | テーブルの名前。          |
| **UserState**                   | string   | Azure AD におけるユーザー アカウントの現在の状態 (アクティブ、無効、休止、ロックアウト)。  |
| **UserStateChangedOn**          | DATETIME | アカウントの状態が最後に変更された日付 (UTC)。     |
| **UserType**                    | string   | ユーザーの種類。         |


## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel のエンティティ行動分析テーブルのスキーマについて説明しました。

- [エンティティ行動分析](identify-threats-with-entity-behavior-analytics.md)の詳細を確認する。
- 調査で [UEBA を使用](investigate-with-ueba.md)する。
