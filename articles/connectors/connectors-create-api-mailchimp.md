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
ms.date="05/17/2016"
ms.author="deonhe"/>

# MailChimp コネクタの使用



MailChimp コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [フロー](http://flows.microsoft.com)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

MailChimp コネクタは、アクションとして使用できます。MailChimp コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 MailChimp コネクタでは、次のアクションやトリガーを使用できます。

### MailChimp のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from\_name、および reply\_to) に基づいて新しいキャンペーンを作成します|
|[newlist](connectors-create-api-mailchimp.md#newlist)|MailChimp アカウントで新しい一覧を作成します|
|[addmember](connectors-create-api-mailchimp.md#addmember)|一覧のメンバーを追加または更新します|
|[removemember](connectors-create-api-mailchimp.md#removemember)|一覧からメンバーを削除します|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|一覧の特定メンバーの情報を更新します|
### MailChimp のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|一覧へのメンバーの追加時|新しいメンバーが一覧に追加されたときにワークフローをトリガーします|
|新しい一覧の作成時|新しい一覧が作成されたときにワークフローをトリガーします|


## MailChimp への接続の作成
MailChimp を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|はい|MailChimp の資格情報を提供します|

>[AZURE.INCLUDE [MailChimp への接続を作成する手順](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## MailChimp のリファレンス
適用されるバージョン: 1.0

## newcampaign
新しいキャンペーン: キャンペーンの種類、受信者の一覧、およびキャンペーンの設定 (subject line、title、from\_name、および reply\_to) に基づいて新しいキャンペーンを作成します

```POST: /campaigns```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|newCampaignRequest| |○|body|なし|新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト|

#### Response

|名前|説明|
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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|newListRequest| |○|body|なし|新しいキャンペーンの要求パラメーターと共に本文で送信する Json オブジェクト|

#### Response

|名前|説明|
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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|newMemberInList| |○|body|なし|新しいメンバー情報と共に本文で送信する Json オブジェクト|

#### Response

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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|member\_email|string|○|path|なし|削除するメンバーの電子メール アドレス|

#### Response

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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|
|member\_email|string|○|path|なし|更新するメンバーの一意の電子メール アドレス|
|updateMemberInListRequest| |○|body|なし|更新されたメンバー情報と共に本文で送信する Json オブジェクト|

#### Response

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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|list\_id|string|○|path|なし|一覧の一意の ID|

#### Response

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
#### Response

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
|recipients|未定義|あり |
|設定|未定義|あり |
|variate\_settings|未定義|いいえ |
|tracking|未定義|いいえ |
|rss\_opts|未定義|いいえ |
|social\_card|未定義|いいえ |



### Recipient


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|list\_id|string|はい |
|segment\_opts|未定義|いいえ |



### 設定


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|subject\_line|string|あり |
|title|string|いいえ |
|from\_name|string|はい |
|reply\_to|string|あり |
|use\_conversation|boolean|いいえ |
|to\_name|string|いいえ |
|folder\_id|integer|いいえ |
|authenticate|boolean|いいえ |
|auto\_footer|boolean|いいえ |
|inline\_css|boolean|いいえ |
|auto\_tweet|boolean|いいえ |
|auto\_fb\_post|array|いいえ |
|fb\_comments|boolean|いいえ |



### Variate\_Settings


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|winner\_criteria|string|いいえ |
|wait\_time|integer|いいえ |
|test\_size|integer|いいえ |
|subject\_lines|array|いいえ |
|send\_times|array|いいえ |
|from\_names|array|いいえ |
|reply\_to\_addresses|array|いいえ |



### 追跡


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|opens|boolean|いいえ |
|html\_clicks|boolean|いいえ |
|text\_clicks|boolean|いいえ |
|goal\_tracking|boolean|いいえ |
|ecomm360|boolean|いいえ |
|google\_analytics|string|いいえ |
|clicktale|string|いいえ |
|salesforce|未定義|いいえ |
|highrise|未定義|いいえ |
|capsule|未定義|いいえ |



### RSS\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|feed\_url|string|いいえ |
|frequency|string|いいえ |
|constrain\_rss\_img|string|いいえ |
|schedule|未定義|いいえ |



### Social\_Card


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|image\_url|string|いいえ |
|description|string|いいえ |
|title|string|いいえ |



### Segment\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|saved\_segment\_id|integer|いいえ |
|match|string|いいえ |



### Salesforce


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|campaign|boolean|いいえ |
|notes|boolean|いいえ |



### Highrise


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|campaign|boolean|いいえ |
|notes|boolean|いいえ |



### Capsule


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|notes|boolean|いいえ |



### スケジュール


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|hour|integer|いいえ |
|daily\_send|未定義|いいえ |
|weekly\_send\_day|string|いいえ |
|monthly\_send\_date|number|いいえ |



### Daily\_Send


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|sunday|boolean|いいえ |
|monday|boolean|いいえ |
|tuesday|boolean|いいえ |
|wednesday|boolean|いいえ |
|thursday|boolean|いいえ |
|friday|boolean|いいえ |
|saturday|boolean|いいえ |



### CampaignResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|type|string|いいえ |
|create\_time|string|いいえ |
|archive\_url|string|いいえ |
|status|string|いいえ |
|emails\_sent|integer|いいえ |
|send\_time|string|いいえ |
|content\_type|string|いいえ |
|recipient|array|いいえ |
|設定|未定義|いいえ |
|variate\_settings|未定義|いいえ |
|tracking|未定義|いいえ |
|rss\_opts|未定義|いいえ |
|ab\_split\_opts|未定義|いいえ |
|social\_card|未定義|いいえ |
|report\_summary|未定義|いいえ |
|delivery\_status|未定義|いいえ |
|\_links|array|いいえ |



### AB\_Split\_Opts


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|split\_test|string|いいえ |
|pick\_winner|string|いいえ |
|wait\_units|string|いいえ |
|wait\_time|integer|いいえ |
|split\_size|integer|いいえ |
|from\_name\_a|string|いいえ |
|from\_name\_b|string|いいえ |
|reply\_email\_a|string|いいえ |
|reply\_email\_b|string|いいえ |
|subject\_a|string|いいえ |
|subject\_b|string|いいえ |
|send\_time\_a|string|いいえ |
|send\_time\_b|string|いいえ |
|send\_time\_winner|string|いいえ |



### Report\_Summary


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|opens|integer|いいえ |
|unique\_opens|integer|いいえ |
|open\_rate|number|いいえ |
|clicks|integer|いいえ |
|subscriber\_clicks|number|いいえ |
|click\_rate|number|いいえ |



### Delivery\_Status


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|enabled|boolean|いいえ |
|can\_cancel|boolean|いいえ |
|status|string|いいえ |
|emails\_sent|integer|いいえ |
|emails\_canceled|integer|いいえ |



### リンク


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|rel|string|いいえ |
|href|string|いいえ |
|静的メソッド|string|いいえ |
|targetSchema|string|いいえ |
|schema|string|いいえ |



### NewListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|name|string|あり |
|contact|未定義|あり |
|permission\_reminder|string|はい |
|use\_archive\_bar|boolean|いいえ |
|campaign\_defaults|未定義|はい |
|notify\_on\_subscribe|string|いいえ |
|notify\_on\_unsubscribe|string|いいえ |
|email\_type\_option|boolean|あり |
|visibility|string|いいえ |



### 連絡先


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|company|string|あり |
|address1|string|はい |
|address2|string|いいえ |
|city|string|あり |
|state|string|はい |
|zip|string|はい |
|country|string|はい |
|phone|string|あり |



### Campaign\_Defaults


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|from\_name|string|あり |
|from\_email|string|あり |
|subject|string|いいえ |
|言語|string|あり |



### CreateNewListResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|あり |
|name|string|あり |
|contact|未定義|あり |
|permission\_reminder|string|はい |
|use\_archive\_bar|boolean|いいえ |
|campaign\_defaults|未定義|はい |
|notify\_on\_subscribe|string|いいえ |
|notify\_on\_unsubscribe|string|いいえ |
|date\_created|string|いいえ |
|list\_rating|integer|いいえ |
|email\_type\_option|boolean|はい |
|subscribe\_url\_short|string|いいえ |
|subscribe\_url\_long|string|いいえ |
|beamer\_address|string|いいえ |
|visibility|string|いいえ |
|modules|array|いいえ |
|stats|未定義|いいえ |
|\_links|array|いいえ |



### 統計


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|member\_count|integer|いいえ |
|unsubscribe\_count|integer|いいえ |
|cleaned\_count|integer|いいえ |
|member\_count\_since\_send|integer|いいえ |
|unsubscribe\_count\_since\_send|integer|いいえ |
|cleaned\_count\_since\_send|integer|いいえ |
|campaign\_count|integer|いいえ |
|campaign\_last\_sent|integer|いいえ |
|merge\_field\_count|integer|いいえ |
|avg\_sub\_rate|number|いいえ |
|avg\_unsub\_rate|number|いいえ |
|target\_sub\_rate|number|いいえ |
|open\_rate|number|いいえ |
|click\_rate|number|いいえ |
|last\_sub\_date|string|いいえ |
|last\_unsub\_date|string|いいえ |



### GetListsResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|lists|array|いいえ |
|total\_items|integer|いいえ |



### NewMemberInListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|email\_type|string|いいえ |
|status|string|はい |
|merge\_fields|未定義|いいえ |
|interests|string|いいえ |
|言語|string|いいえ |
|vip|boolean|いいえ |
|location|未定義|いいえ |
|email\_address|string|あり |



### FirstAndLastName


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|FNAME|string|いいえ |
|LNAME|string|いいえ |



### Location (場所)


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|latitude|number|いいえ |
|longitude|number|いいえ |



### MemberResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|email\_address|string|いいえ |
|unique\_email\_id|string|いいえ |
|email\_type|string|いいえ |
|status|string|いいえ |
|merge\_fields|未定義|いいえ |
|interests|string|いいえ |
|stats|未定義|いいえ |
|ip\_signup|string|いいえ |
|timestamp\_signup|string|いいえ |
|ip\_opt|string|いいえ |
|timestamp\_opt|string|いいえ |
|member\_rating|integer|いいえ |
|last\_changed|string|いいえ |
|言語|string|いいえ |
|vip|boolean|いいえ |
|email\_client|string|いいえ |
|location|未定義|いいえ |
|last\_note|未定義|いいえ |
|list\_id|string|いいえ |
|\_links|array|いいえ |



### Last\_Note


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|note\_id|integer|いいえ |
|created\_at|string|いいえ |
|created\_by|string|いいえ |
|note|string|いいえ |



### GetAllMembersResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|members|array|いいえ |
|list\_id|string|いいえ |
|total\_items|integer|いいえ |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|



### UpdateMemberInListRequest


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|email\_address|string|いいえ |
|email\_type|string|いいえ |
|status|string|はい |
|merge\_fields|未定義|いいえ |
|interests|string|いいえ |
|言語|string|いいえ |
|vip|boolean|いいえ |
|location|未定義|いいえ |



### GetMembersResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|members|array|いいえ |
|list\_id|string|いいえ |
|total\_items|integer|いいえ |



### AddUserResponseModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|はい |
|email\_address|string|あり |
|unique\_email\_id|string|いいえ |
|email\_type|string|いいえ |
|status|string|いいえ |
|merge\_fields|未定義|あり |
|interests|string|いいえ |
|stats|未定義|いいえ |
|ip\_signup|string|いいえ |
|timestamp\_signup|string|いいえ |
|ip\_opt|string|いいえ |
|timestamp\_opt|string|いいえ |
|member\_rating|integer|いいえ |
|last\_changed|string|いいえ |
|言語|string|いいえ |
|vip|boolean|いいえ |
|email\_client|string|いいえ |
|location|未定義|いいえ |
|last\_note|未定義|いいえ |
|list\_id|string|いいえ |
|\_links|array|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->