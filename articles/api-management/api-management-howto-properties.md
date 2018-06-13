---
title: Azure API Management ポリシーのプロパティの利用方法
description: Azure API Management ポリシーのプロパティの利用方法に関する説明
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197114"
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Azure API Management ポリシーのプロパティの利用方法
API Management のポリシーは、Azure Portal がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 ポリシー ステートメントは、リテラル テキストの値、ポリシーの式、プロパティを使用して構築できます。 

それぞれの API Management サービス インスタンスには、サービス インスタンスにグローバルなキー/値ペアのプロパティ コレクションがあります。 これらのプロパティを利用し、すべての API の構成とポリシーを対象に、定数文字列値を管理できます。 各プロパティは次の属性を持つことができます。

| Attribute | type | [説明] |
| --- | --- | --- |
| 表示名 |文字列 |ポリシーのプロパティを参照する際に使用する英数字の文字列。 |
| 値 |文字列 |プロパティの値。 空にすることはできません。スペースのみで構成することはできません。 |
|シークレット|ブール値|値がシークレットかどうかと暗号化する必要があるかどうかを決定します。|
| タグ |文字列の配列 |任意のタグ。指定されている場合、プロパティの一覧のフィルター処理に利用できます。 |

![名前付きの値](./media/api-management-howto-properties/named-values.png)

プロパティ値にはリテラル文字列と[ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx)を含めることができます。 たとえば、`ExpressionProperty` の値は、現在の日時を含む文字列を返すポリシー式です。 プロパティ `ContosoHeaderValue` はシークレットとして設定されています。そのため、その値は表示されていません。

| Name | 値 | シークレット | タグ |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>プロパティを追加および編集するには

![プロパティの追加](./media/api-management-howto-properties/add-property.png)

1. **[API Management]** で **[API]** を選びます。
2. **[名前付きの値]** を選択します。
3. **[+ 追加]** を押します。

  [名前] と [値] は必須値です。 このプロパティの値がシークレットの場合、[これはシークレットです] チェック ボックスをオンします。 プロパティの整理に役立つ任意のタグを 1 つまたは複数入力し、[保存]をクリックします。
4. **Create** をクリックしてください。

プロパティが作成されたら、そのプロパティをクリックすることで編集できます。 プロパティ名を変更すると、そのプロパティを参照するポリシーも、その新しい名前を使用するように自動的に更新されます。

REST API を利用し、プロパティを編集する方法については、「 [Edit a property using the REST API (REST API を利用してプロパティを編集する)](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)」を参照してください。

## <a name="to-delete-a-property"></a>プロパティを削除するには

プロパティを削除するには、削除するプロパティの横にある **[削除]** をクリックします。

> [!IMPORTANT]
> プロパティがポリシーに参照される場合、プロパティを使用するすべてのポリシーからプロパティを削除するまで削除は完了しません。
> 
> 

REST API を利用し、プロパティを削除する方法については、「 [Delete a property using the REST API (REST API を利用してプロパティを作成する)](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)」を参照してください。

## <a name="to-search-and-filter-properties"></a>プロパティを検索し、フィルター処理するには

**[名前付きの値]** タブには、プロパティの管理に役立つ検索とフィルター処理の機能があります。 プロパティ名でプロパティの一覧をフィルター処理するには、 **[検索プロパティ]** テキストボックスに検索語句を入力します。 すべてのプロパティを表示するには、 **[検索プロパティ]** テキストボックスを消去し、Enter を押します。

タグ値でプロパティの一覧をフィルター処理するには、 **[タグでフィルター]** テキストボックスに 1 つまたは複数のタグを入力します。 すべてのプロパティを表示するには、 **[タグでフィルター]** テキストボックスを消去し、Enter を押します。

## <a name="to-use-a-property"></a>プロパティを使用するには

ポリシーでプロパティを使用するには、`{{ContosoHeader}}` のように、二重の括弧の中にプロパティ名を置きます。次に例を示します。

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

この例では、`ContosoHeader` は `set-header` ポリシーのヘッダー名として使用されており、`ContosoHeaderValue` はそのヘッダーの値として使用されています。 このポリシーが API Management ゲートウェイの要求または応答で評価されるとき、`{{ContosoHeader}}` と `{{ContosoHeaderValue}}` はそれぞれのプロパティ値で置換されます。

プロパティは前の例のように完全な属性または要素値として使用できますが、例 `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

プロパティにはポリシー式を含めることもできます。 次の例では、`ExpressionProperty` が使用されます。

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

このポリシーが評価されるとき、`{{ExpressionProperty}}` はその値 `@(DateTime.Now.ToString())` で置換されます。 値がポリシー式であるため、式が評価され、ポリシーがその実行に進みます。

プロパティが範囲内にあるポリシーを持つ操作を呼び出すことで、開発者ポータルでこれを試すことができます。 次の例では、前の 2 つのサンプル `set-header` ポリシーとプロパティで操作が呼び出されています。 ポリシーとプロパティを利用して構成された 2 つのカスタム ヘッダーが応答に含まれることに注意してください。

![[開発者ポータル]][api-management-send-results]

[API Inspector トレース](api-management-howto-api-inspector.md)を見て、前の 2 つのサンプル ポリシーとプロパティが含まれる呼び出しを探すと、ポリシー式が含まれるプロパティのポリシールール評価に加え、プロパティが挿入された 2 つの `set-header` ポリシーを確認できます。

![API Inspector トレース][api-management-api-inspector-trace]

プロパティ値にはポリシー式を含めることができますが、プロパティ値に他のプロパティを含めることはできません。 `Property value text {{MyProperty}}`のように、プロパティ参照を含むテキストが使用されている場合、そのプロパティ参照は置換されず、プロパティ値の一部として追加されます。

## <a name="next-steps"></a>次の手順
* ポリシーの使用に関する説明
  * [API Management のポリシー](api-management-howto-policies.md)
  * [Policy reference (ポリシー リファレンス)](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

