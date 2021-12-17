---
title: ユーザーの登録と成果物のサブスクリプションを委任する方法
description: ユーザーの登録と製品のサブスクリプションを Azure API Management でサード パーティに委任する方法について説明します。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: danlep
ms.openlocfilehash: dbb948e45473bf091cb2d69e82ad2adf5fcfe8a2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058637"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>ユーザーの登録と成果物のサブスクリプションを委任する方法

委任することで、ご自身の Web サイトでユーザー データを保持し、カスタム検証を実行できます。 委任によって、開発者のサインイン/サインアップおよび製品のサブスクリプションを、開発者ポータルの組み込みの機能ではなく、お使いの既存の Web サイトを使用して処理することができます。 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>開発者のサインインおよびサインアップ処理の委任

開発者のサインインおよびサインアップ処理を既存の Web サイトに委任するには、サイト上で特殊な委任エンドポイントを作成します。 この特殊な委任は、このエンドポイントは、API Management 開発者ポータルから開始されたサインイン/サインアップ要求のエントリ ポイントとして機能します。

最終的なワークフローは次のようになります。

1. 開発者が、API Management 開発者ポータルのサインインまたはサインアップ リンクをクリックします。
2. ブラウザーが委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントにより、ユーザーがリダイレクトされるか、ユーザーにサインイン/サインアップが提示されます。 
4. サインイン/サインアップが成功すると、ユーザーが最初の API Management 開発者ポータルにリダイレクトされます。

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>委任エンドポイント経由で要求をルーティングするように API Management を設定する

1. Azure portal 内で、API Management リソース内で **開発者ポータル** を検索します。
2. **[委任]** 項目をクリックします。 
3. チェックボックスをクリックして、**サインインおよびサインアップの委任** を有効にします。

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="委任の概要":::

4. 特殊な委任エンドポイントの URL を決めて、 **[委任エンドポイント URL]** フィールドに入力します。 
5. **[委任の認証キー]** フィールドで、次のいずれかを行います。
    * 要求が API Management から送信されたものかをどうかを確かめるための署名のコンピューティングに使用するシークレットを入力します。 
    * API Management の **[生成]** ボタンをクリックします。これによりランダム キーが生成されます。
6. **[保存]** をクリックします。

### <a name="create-your-delegation-endpoint"></a>委任エンドポイントを作成する

>[!NOTE]
> 次の手順は **SignIn** 操作の例を示していますが、次の手順で使用可能な操作によってアカウント管理を実行できます。 

ご自身のサイト上で実装する新しい委任エンドポイントを作成するには、次の手順をお勧めします。

1. 次の形式の要求を受け取ります。
   
   > "*http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={元のページの URL}&salt={文字列}&sig={文字列}* "
   
    サインイン/サインアップ処理のためのクエリ パラメーター:

   | パラメーター | 説明 |
   | --------- | ----------- |
   | **operation** | 委任要求の種類を特定します。 使用できる操作: **SignIn**、**ChangePassword**、**ChangeProfile**、**CloseAccount**、**SignOut**。 |
   | **returnUrl** | ユーザーがサインインまたはサインアップ リンクをクリックした場所の URL。 |
   | **salt** | セキュリティ ハッシュの計算に使用される特殊な salt 文字列。 |
   | **sig** | 自分で計算したハッシュとの比較に使用された、計算によって求められたセキュリティ ハッシュ。 |
   
3. 要求の送信元が Azure API Management であることを確認します (省略できますが、セキュリティ上強く推奨されます)。
   
   * **returnUrl** クエリ パラメーターと **salt** クエリ パラメーターに基づいて、文字列の HMAC-SHA512 ハッシュを計算します。 詳細については、[コード例]を参照してください。
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * 上の計算によって求められたハッシュを **sig** クエリ パラメーターの値と比較します。 2 つのハッシュ値が等しい場合は、次の手順に移動します。 それ以外の場合は、要求を拒否します。
4. サインイン/サインアップの要求を受け取っていることを確認します。
    * **operation** クエリ パラメーターが "**SignIn**" に設定されます。
5. サインイン/サインアップ UI をユーザーに表示します。
    * ユーザー サインアップの場合は、API Management 内で対応するアカウントを作成します。 
      * [ユーザーを作成] します。 
      * ユーザー ID は、ユーザー ストア内のユーザー ID と同じ値、または追跡しやすい ID に設定してください。
6. ユーザーが正常に認証されたら、次の操作を行います。
   
   * API Management REST API を使用して、[共有アクセス トークンを要求]します。
   * 上記の API 呼び出しによって受け取った SSO URL に **returnUrl** クエリ パラメーターを付加します。 次に例を示します。
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * 上で生成された URL にユーザーをリダイレクトします。

>[!NOTE]
> アカウント管理操作 (**ChangePassword**、**ChangeProfile**、**CloseAccount**) については、次のクエリ パラメータを渡します。
> 
>    | パラメーター | 説明 |
>    | --------- | ----------- |
>    | **operation** | 委任要求の種類を特定します。 |
>    | **userId** | 管理するアカウントのユーザー ID。 |
>    | **salt** | セキュリティ ハッシュの計算に使用される特殊な salt 文字列。 |
>    | **sig** | 自分で計算したハッシュとの比較に使用された、計算によって求められたセキュリティ ハッシュ。 |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>製品のサブスクリプション処理の委任

製品のサブスクリプション処理を委任するしくみは、ユーザーのサインイン/サインアップ処理の委任と似ています。 最終的なワークフローは次のようになります。

1. 開発者が API Management 開発者ポータルで製品を選択し、 **[サブスクライブ]** ボタンをクリックします。
2. ブラウザーが委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントで、必要な製品のサブスクリプション手順が実行されます。この手順の設計は、お客様に任されています。 これには次が含まれます。 
   * 別のページへのリダイレクトして課金情報を要求する。
   * 追加の質問を行う。
   * 情報を保存するだけでユーザーのアクションを求めない。

### <a name="enable-the-api-management-functionality"></a>API Management 機能を有効にする

**[委任]** ページで **[製品のサブスクリプションの委任]** をクリックします。

### <a name="create-your-delegation-endpoint"></a>委任エンドポイントを作成する

ご自身のサイト上で実装する新しい委任エンドポイントを作成するには、次の手順をお勧めします。

1. 次の形式で要求を受け取ります。
   
   > "*http:\//www.yourwebsite.com/apimdelegation?operation={操作}&productId={サブスクライブする成果物}&userId={要求元のユーザー}&salt={文字列}&sig={文字列}* "
   >
   
    成果物のサブスクリプション処理のためのクエリ パラメーター:

   | パラメーター | 説明 |
   | --------- | ----------- |
   | **operation** | 委任要求の種類を特定します。 有効な製品サブスクリプション要求のオプションを次に示します。 <ul><li>**Subscribe**: 提供された ID (以下を参照) を持つ特定の製品をユーザーがサブスクライブするための要求。</li><li>**Unsubscribe**: ユーザーの製品のサブスクリプションを解除するための要求。</li><li>**Renew**: (たとえば、有効期限が近づいている) サブスクリプションを更新するための要求</li></ul> |
   | **productId** | "*サブスクライブ*" 時、ユーザーがサブスクリプションを要求した製品の ID。 |
   | **subscriptionId** | "*登録解除*" および "*更新*" 時の製品のサブスクリプション。 |
   | **userId** | "*サブスクライブ*" 時の要求側ユーザーの ID。 |
   | **salt** | セキュリティ ハッシュの計算に使用される特殊な salt 文字列。 |
   | **sig** | 自分で計算したハッシュとの比較に使用された、計算によって求められたセキュリティ ハッシュ。 |

2. 要求の送信元が Azure API Management であることを確認します (省略できますが、セキュリティ上強く推奨されます)。
   
   * **productId**、**userId**、および **salt** のクエリ パラメーターに基づいて、文字列の HMAC-SHA512 を計算します。
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * 上の計算によって求められたハッシュを **sig** クエリ パラメーターの値と比較します。 2 つのハッシュ値が等しい場合は、次の手順に移動します。 それ以外の場合は、要求を拒否します。
3. **operation** で要求された操作の種類 (課金、追加の質問など) に基づいて、製品のサブスクリプションを処理します。
4. お客様の側で製品へのユーザー サブスクリプションを正常に処理できたら、[サブスクリプションのための REST API を呼び出し]て、API Management 製品へのユーザーのサブスクリプション処理を行います。

## <a name="example-code"></a><a name="delegate-example-code"> </a>コード例

以下のコード サンプルは、次の操作を行う方法を示しています｡

* "*委任検証キー*" を取得する。このキーは、Azure ポータルの **[委任]** 画面で設定されます。
* HMAC を作成する。この HMAC は、署名の検証のために使用されます。これにより、渡された returnUrl の有効性が証明されます。

このコードは、少し変更すれば、**productId** と **userId** に対して使用できます。

### <a name="c-code-to-generate-hash-of-returnurl"></a>returnUrl のハッシュを生成する C# のコード

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>returnUrl のハッシュを生成する NodeJS のコード

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 委任変更を有効にするには、[開発者ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

## <a name="next-steps"></a>次のステップ
- [開発者ポータルの詳細を確認する。](api-management-howto-developer-portal.md)
- [Azure AD を使用して認証する](api-management-howto-aad.md)。または [Azure AD B2C](api-management-howto-aad-b2c.md) を使って認証する。
- 開発者ポータルに関する質問が他にある場合は、 [FAQ で回答を確認する](developer-portal-faq.md)。

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[共有アクセス トークンを要求する]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[ユーザーを作成]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[サブスクリプションのための REST API を呼び出します]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[コード例]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
