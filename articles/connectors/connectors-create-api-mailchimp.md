<properties
pageTitle="MailChimp | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# MailChimp コネクタの使用

MailChimp は、電子メール マーケティング活動を管理および自動化するための企業向け SaaS サービスです。このサービスにより、マーケティング電子メールの送信、自動メッセージ、対象を絞ったキャンペーンなどを行うことができます。


>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

MailChimp コネクタは、アクションとして使用できます。MailChimp コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 MailChimp コネクタでは、次のアクションやトリガーを使用できます。

### MailChimp のアクション
実行できるアクションは以下のとおりです。

|アクション|Description|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from\_name、および reply\_to) に基づいて新しいキャンペーンを作成します|
|[newlist](connectors-create-api-mailchimp.md#newlist)|MailChimp アカウントで新しい一覧を作成します|
|[addmember](connectors-create-api-mailchimp.md#addmember)|一覧のメンバーを追加または更新します|
|[removemember](connectors-create-api-mailchimp.md#removemember)|一覧からメンバーを削除します|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|一覧の特定メンバーの情報を更新します|
### MailChimp のトリガー
次のイベントをリッスンできます。

|トリガー | Description|
|--- | ---|
|一覧へのメンバーの追加時|新しいメンバーが一覧に追加されたときにワークフローをトリガーします|
|新しい一覧の作成時|新しい一覧が作成されたときにワークフローをトリガーします|


## MailChimp への接続の作成
MailChimp を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|Description|
| ---|---|---|
|トークン|はい|MailChimp の資格情報を提供します|

>[AZURE.INCLUDE [MailChimp への接続を作成する手順](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## MailChimp のリファレンス
適用されるバージョン: 1.0

## newcampaign
新しいキャンペーン: キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from\_name、および reply\_to) に基づいて新しいキャンペーンを作成します

```POST: /campaigns```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|newCampaignRequest| |○|body|なし|新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト|

#### 応答

|Name|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## newlist
新しい一覧: MailChimp アカウントで新しい一覧を作成します

```POST: /lists```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|newListRequest| |○|body|なし|新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト|

#### 応答

|Name|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## addmember
一覧へのメンバーの追加: 一覧のメンバーを追加または更新します

```POST: /lists/{list_id}/members```

| Name| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|newMemberInList| |○|body|なし|新しいメンバー情報と共に本文で送信する Json オブジェクト|

#### 応答

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## removemember
一覧からのメンバーの削除: 一覧からメンバーを削除します。

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|member\_email|string|○|path|なし|削除するメンバーの電子メール アドレス|

#### 応答

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## updatemember
メンバー情報の更新: 一覧の特定メンバーの情報を更新します

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|member\_email|string|○|path|なし|更新するメンバーの一意の電子メール アドレス|
|updateMemberInListRequest| |○|body|なし|更新されたメンバー情報と共に本文で送信する Json オブジェクト|

#### 応答

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## OnMemberSubscribed
一覧へのメンバーの追加時: 新しいメンバーが一覧に追加されたときにワークフローをトリガーします

```GET: /trigger/lists/{list_id}/members```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|

#### 応答

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## OnCreateList
新しい一覧の作成時: 新しい一覧が作成されたときにワークフローをトリガーします

```GET: /trigger/lists```

この呼び出しには、パラメーターはありません
#### 応答

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## オブジェクト定義 

### NewCampaignRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|type|string|はい |
|recipients|未定義|はい |
|設定|未定義|はい |
|variate\_settings|未定義|なし |
|tracking|未定義|なし |
|rss\_opts|未定義|なし |
|social\_card|未定義|なし |



### Recipient


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|string|はい |
|segment\_opts|未定義|なし |



### Settings


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|subject\_line|string|はい |
|title|string|なし |
|from\_name|string|はい |
|reply\_to|string|はい |
|use\_conversation|boolean|なし |
|to\_name|string|なし |
|folder\_id|integer|なし |
|authenticate|boolean|なし |
|auto\_footer|boolean|なし |
|inline\_css|boolean|なし |
|auto\_tweet|boolean|なし |
|auto\_fb\_post|array|なし |
|fb\_comments|boolean|なし |



### Variate\_Settings


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|winner\_criteria|string|なし |
|wait\_time|integer|なし |
|test\_size|integer|なし |
|subject\_lines|array|なし |
|send\_times|array|なし |
|from\_names|array|なし |
|reply\_to\_addresses|array|なし |



### 追跡


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|opens|boolean|なし |
|html\_clicks|boolean|なし |
|text\_clicks|boolean|なし |
|goal\_tracking|boolean|なし |
|ecomm360|boolean|なし |
|google\_analytics|string|なし |
|clicktale|string|なし |
|salesforce|未定義|なし |
|highrise|未定義|なし |
|capsule|未定義|なし |



### RSS\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|feed\_url|string|なし |
|frequency|string|なし |
|constrain\_rss\_img|string|なし |
|schedule|未定義|なし |



### Social\_Card


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|image\_url|string|なし |
|description|string|なし |
|title|string|なし |



### Segment\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|saved\_segment\_id|integer|なし |
|match|string|なし |



### Salesforce


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|campaign|boolean|なし |
|notes|boolean|なし |



### Highrise


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|campaign|boolean|なし |
|notes|boolean|なし |



### Capsule


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|notes|boolean|なし |



### スケジュール


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|hour|integer|なし |
|daily\_send|未定義|なし |
|weekly\_send\_day|string|なし |
|monthly\_send\_date|number|なし |



### Daily\_Send


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sunday|boolean|なし |
|monday|boolean|なし |
|tuesday|boolean|なし |
|wednesday|boolean|なし |
|thursday|boolean|なし |
|friday|boolean|なし |
|saturday|boolean|なし |



### CampaignResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|なし |
|type|string|なし |
|create\_time|string|なし |
|archive\_url|string|なし |
|status|string|なし |
|emails\_sent|integer|なし |
|send\_time|string|なし |
|content\_type|string|なし |
|recipient|array|なし |
|設定|未定義|なし |
|variate\_settings|未定義|なし |
|tracking|未定義|なし |
|rss\_opts|未定義|なし |
|ab\_split\_opts|未定義|なし |
|social\_card|未定義|なし |
|report\_summary|未定義|なし |
|delivery\_status|未定義|なし |
|\_links|array|なし |



### AB\_Split\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|split\_test|string|なし |
|pick\_winner|string|なし |
|wait\_units|string|なし |
|wait\_time|integer|なし |
|split\_size|integer|なし |
|from\_name\_a|string|なし |
|from\_name\_b|string|なし |
|reply\_email\_a|string|なし |
|reply\_email\_b|string|なし |
|subject\_a|string|なし |
|subject\_b|string|なし |
|send\_time\_a|string|なし |
|send\_time\_b|string|なし |
|send\_time\_winner|string|なし |



### Report\_Summary


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|opens|integer|なし |
|unique\_opens|integer|なし |
|open\_rate|number|なし |
|clicks|integer|なし |
|subscriber\_clicks|number|なし |
|click\_rate|number|なし |



### Delivery\_Status


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|enabled|boolean|なし |
|can\_cancel|boolean|なし |
|status|string|なし |
|emails\_sent|integer|なし |
|emails\_canceled|integer|なし |



### リンク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|rel|string|なし |
|href|string|なし |
|静的メソッド|string|なし |
|targetSchema|string|なし |
|schema|string|なし |



### NewListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|name|string|はい |
|contact|未定義|はい |
|permission\_reminder|string|はい |
|use\_archive\_bar|boolean|なし |
|campaign\_defaults|未定義|はい |
|notify\_on\_subscribe|string|なし |
|notify\_on\_unsubscribe|string|なし |
|email\_type\_option|boolean|はい |
|visibility|string|なし |



### 連絡先


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|company|string|はい |
|address1|string|はい |
|address2|string|なし |
|city|string|はい |
|state|string|はい |
|zip|string|はい |
|country|string|はい |
|phone|string|はい |



### Campaign\_Defaults


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|from\_name|string|はい |
|from\_email|string|はい |
|subject|string|なし |
|言語|string|はい |



### CreateNewListResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|はい |
|name|string|はい |
|contact|未定義|はい |
|permission\_reminder|string|はい |
|use\_archive\_bar|boolean|なし |
|campaign\_defaults|未定義|はい |
|notify\_on\_subscribe|string|なし |
|notify\_on\_unsubscribe|string|なし |
|date\_created|string|なし |
|list\_rating|integer|なし |
|email\_type\_option|boolean|はい |
|subscribe\_url\_short|string|なし |
|subscribe\_url\_long|string|なし |
|beamer\_address|string|なし |
|visibility|string|なし |
|modules|array|なし |
|stats|未定義|なし |
|\_links|array|なし |



### 統計


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|member\_count|integer|なし |
|unsubscribe\_count|integer|なし |
|cleaned\_count|integer|なし |
|member\_count\_since\_send|integer|なし |
|unsubscribe\_count\_since\_send|integer|なし |
|cleaned\_count\_since\_send|integer|なし |
|campaign\_count|integer|なし |
|campaign\_last\_sent|integer|なし |
|merge\_field\_count|integer|なし |
|avg\_sub\_rate|number|なし |
|avg\_unsub\_rate|number|なし |
|target\_sub\_rate|number|なし |
|open\_rate|number|なし |
|click\_rate|number|なし |
|last\_sub\_date|string|なし |
|last\_unsub\_date|string|なし |



### GetListsResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|lists|array|なし |
|total\_items|integer|なし |



### NewMemberInListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|email\_type|string|なし |
|status|string|はい |
|merge\_fields|未定義|なし |
|interests|string|なし |
|言語|string|なし |
|vip|boolean|なし |
|location|未定義|なし |
|email\_address|string|はい |



### FirstAndLastName


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|FNAME|string|なし |
|LNAME|string|なし |



### 場所


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|latitude|number|なし |
|longitude|number|なし |



### MemberResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|なし |
|email\_address|string|なし |
|unique\_email\_id|string|なし |
|email\_type|string|なし |
|status|string|なし |
|merge\_fields|未定義|なし |
|interests|string|なし |
|stats|未定義|なし |
|ip\_signup|string|なし |
|timestamp\_signup|string|なし |
|ip\_opt|string|なし |
|timestamp\_opt|string|なし |
|member\_rating|integer|なし |
|last\_changed|string|なし |
|言語|string|なし |
|vip|boolean|なし |
|email\_client|string|なし |
|location|未定義|なし |
|last\_note|未定義|なし |
|list\_id|string|なし |
|\_links|array|なし |



### Last\_Note


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|note\_id|integer|なし |
|created\_at|string|なし |
|created\_by|string|なし |
|note|string|なし |



### GetAllMembersResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|members|array|なし |
|list\_id|string|なし |
|total\_items|integer|なし |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|



### UpdateMemberInListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|email\_address|string|なし |
|email\_type|string|なし |
|status|string|はい |
|merge\_fields|未定義|なし |
|interests|string|なし |
|言語|string|なし |
|vip|boolean|なし |
|location|未定義|なし |



### GetMembersResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|members|array|なし |
|list\_id|string|なし |
|total\_items|integer|なし |



### AddUserResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|はい |
|email\_address|string|はい |
|unique\_email\_id|string|なし |
|email\_type|string|なし |
|status|string|なし |
|merge\_fields|未定義|はい |
|interests|string|なし |
|stats|未定義|なし |
|ip\_signup|string|なし |
|timestamp\_signup|string|なし |
|ip\_opt|string|なし |
|timestamp\_opt|string|なし |
|member\_rating|integer|なし |
|last\_changed|string|なし |
|言語|string|なし |
|vip|boolean|なし |
|email\_client|string|なし |
|location|未定義|なし |
|last\_note|未定義|なし |
|list\_id|string|なし |
|\_links|array|なし |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->