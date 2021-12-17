---
title: Azure API Management のポリシー | Microsoft Docs
description: API Management のポリシーを作成、編集、構成する方法について説明します。 コード例を参照し、使用可能なリソースを確認してください。
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: danlep
ms.openlocfilehash: 37e25908bd08ca9618bc56bb2e10ff8e48a46fc6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649680"
---
# <a name="policies-in-azure-api-management"></a>Azure API Management のポリシー

Azure API Management API パブリッシャーは、ポリシーを使用した構成で API の動作を変更できます。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 これらのステートメントには、次のものがあります。

* XML から JSON への形式変換。
* 開発者からの着信数を制限する呼び出しレート リミッター。 

他にも多数のポリシーが標準で提供されています。

ポリシーは、API コンシューマーとマネージド API の間に配置されたゲートウェイ内で適用されます。 ゲートウェイが要求を受信し、変更されていない要求を基になる API に転送する間、ポリシーは受信要求と送信応答の両方に変更を適用できます。

ポリシー式は、ポリシーで特に指定されていない限り、任意の API Management ポリシーで属性値またはテキスト値として使用できます。 ポリシーの中には、[制御フロー][Control flow]や[変数の設定][Set variable]のように、ポリシー式に基づいたものがあります。 詳細は、「[高度なポリシー][Advanced policies]」と、「[ポリシー式][Policy expressions]」を参照してください。

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>ポリシー構成について

ポリシー定義は、一連の受信および送信ステートメントが記述された簡単な XML ドキュメントです。 XML は定義ウィンドウで直接編集できます。このウィンドウには、次の機能も用意されています。
* 右側にステートメント一覧を表示。
* 現在のスコープに適用可能なステートメントは有効になり、強調表示されます。

有効なステートメントをクリックすると、定義ビュー内のカーソル位置に適切な XML が追加されます。 

> [!NOTE]
> 追加するポリシーが有効になっていない場合、そのポリシー用の正しいスコープが選択されていることを確認してください。 各ポリシー ステートメントは、特定のスコープおよびポリシー セクション内で使用するように設計されています。 ポリシーのセクションとスコープを確認するには、[ポリシー リファレンス][Policy Reference]の **使用例** セクションを参照してください。

構成は `inbound`、`backend`、`outbound`、および `on-error` に分かれます。 要求および応答に対して、指定された一連のポリシー ステートメントが順に実行されます。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

要求の処理中にエラーが発生した場合:
* `inbound`、`backend`、`outbound` セクションの残りのステップはすべてスキップされます。
* 実行は `on-error` セクションのステートメントにジャンプします。

ポリシー ステートメントを `on-error` セクションに配置することで、以下が可能になります。
* `context.LastError` プロパティを使用してエラーを確認します。
* `set-body` ポリシーを使用して、エラー応答を検査し、カスタマイズできます。
* エラーが発生した場合の処理を構成します。 

詳細は、 [API Management のポリシーにおけるエラー処理](./api-management-error-handling-policies.md)を参照してください。
* プラグイン手順
* ポリシー ステートメントの処理中に発生する可能性があるエラー。 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>ポリシーの構成方法

ポリシーの構成方法については、[ポリシーの設定と編集](set-edit-policies.md)に関する記事をご覧ください。

## <a name="policy-reference"></a>ポリシー リファレンス

ポリシー ステートメントとその設定の一覧については、[ポリシー リファレンス](./api-management-policies.md)に関する記事をご覧ください。

## <a name="policy-samples"></a>ポリシーのサンプル

コード サンプルの詳細については、[ポリシーのサンプル](./policy-reference.md)に関する記事をご覧ください。

## <a name="examples"></a>例

### <a name="apply-policies-specified-at-different-scopes"></a>さまざまなスコープで指定されたポリシーを適用する

グローバル レベルのポリシーと、特定の API に対して設定されたポリシーがある場合、その特定の API が使用されるたびに両方のポリシーが適用されます。 API Management では、`base` 要素を介してポリシー ステートメントの組み合わせの順序を指定できます。 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

上記のポリシー定義の例では、次のようになります。
* `cross-domain` ステートメントは、それより高いポリシーの前に実行されます。
* `find-and-replace` ポリシーは、それより高いポリシーの前に実行されます。 

>[!NOTE]
> API スコープで `<base />` タグを削除すると、API スコープで構成されたポリシーだけが適用されます。 製品とグローバル スコープのどちらのポリシーも適用されません。

### <a name="restrict-incoming-requests"></a>受信要求を制限する

新しいステートメントを追加して受信要求を指定された IP アドレスに制限するには、`inbound` XML 要素内にカーソルを置き、**[Restrict caller IPs]\(呼び出し元 IP の制限\)** ステートメントをクリックします。

![制限ポリシー][policies-restrict]

これで、ステートメントを構成する方法を示す XML スニペットが `inbound` 要素に追加されます。

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

受信要求を制限して、IP アドレス 1.2.3.4 からの要求のみを受け付けるには、次のように XML を変更します。

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
+ [ポリシーのサンプル](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
