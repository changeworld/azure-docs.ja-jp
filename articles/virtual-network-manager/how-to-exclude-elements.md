---
title: Azure Virtual Network Manager での動的メンバーシップからの要素の除外 (プレビュー)
description: Azure Virtual Network Manager での動的メンバーシップからの要素を除外する方法を学習します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 318a2eba29ee7641219e8c970c3dd3a51407ddf3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091905"
---
# <a name="excluding-elements-from-dynamic-membership-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager での動的メンバーシップからの要素の除外 (プレビュー)

このアーティクルでは、動的メンバーシップから仮想ネットワークを除外するために、条件付きステートメントの使用法を学習します。 これらの条件付きのステートメントは、ドロップダウン メニューからパラメーターと演算子を選択する基本エディターの使用で作成します。 既存のネットワーク グループの条件付きステートメントを更新するための詳細エディターの使用法も学習します。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="parameters-and-operators"></a><a name="parameters"></a> パラメーターと演算子

動的メンバーシップを持つ仮想ネットワークは、条件付きステートメントの使用で選択されます。 選択した仮想ネットワークをさらに絞り込む必要があるシナリオでは、*AND* や **OR** などの **論理演算子** を使用して、複数の条件付きステートメントを定義できます。 

サポートされているパラメーターのリスト:

| パラメーター | [詳細エディター] フィールド |
| ---------- | ------------------------- |
| 名前 | `Name` |
| id | `Id` |
| タグ| `tag['tagName']` |
| サブスクリプション名 | `[subscription().Name]` |
| サブスクリプション ID | `[subscription().Id]` |
| サブスクリプションのタグ | `[subscription().tags['tagName']]` |
| リソース グループ名 | `[resourceGroup().Name]` |
| リソース グループ ID | `[resourceGroup().Id]` |
| リソース グループのタグ | `[resourceGroup().tags['tagName']]` |

サポートされている演算子のリスト:

| 演算子 | 詳細エディター |
| --------- | --------------- |
| Contains | `"contains": <>` |
| [次の値を含まない] | `"notcontains": <>` |
| / | `"in": <>` |
| 含まれない | `"notin": <>` |
| 等しい | `"equals": <>` |
| 次の値と等しくない | `"notequals": <>` |
| いくらかを含みます | `"contains": <>` |
| すべてを含みます | `"contains": <>` |
| いくらかを含みません | `"notcontains": <>` |
| Exists | `"exists": true` |
| 存在しません | `"exists": false` |

> [!NOTE]
> *存在* 演算子と *存在しない* 演算子は、 **タグ** パラメーターでのみ使用されます。

## <a name="basic-editor"></a>基本エディター

サブスクリプションに以下の仮想ネットワークがあると仮定します。 各仮想ネットワークには、 *実稼働* タグか *テスト* タグのいずれかが関連付けされています。 実稼働タグを持って、名前が **VNet-A** を含む仮想ネットワークのみを選択したいです。

* VNet-A-EastUS - *実稼働*
* VNet-A-WestUS - *実稼働*
* VNet-B-WestUS - *テスト*
* VNet-C-WestUS - *テスト*
* VNetA - *実稼働*
* VNetB - *テスト*

基本エディターの使用で条件付きステートメントを作成するために、新しいネットワーク グループを作成する必要があります。

1. Azure Virtual Network Manager インスタンスに移動して、 **[ネットワーク グループ]** を *設定* から選択します。 次に、 **[+ 追加]** を選択して、新しいネットワーク グループを作成します。

1. ネットワーク グループの **[名前]** を入力します。 次に、 **[条件付きステートメント]** タブを選択します。

1. **[パラメーター]** のドロップダウンから *[タグ]* を選択してから、 **[演算子]** のドロップダウンから *[存在]* を選択します。

1. **[条件]** に *「Prod」* と入力してから、 **[評価]** を選択します。 VNet-A-EastUS、VNet-B-WestUS、およびVNetA だけがリストに表示されます。

1. **AND** 論理演算子の選択で別の条件付きステートメントを追加します。 **[パラメーター]** に *[名前]* を選択し、 **[演算子]** に *[含む]* を選択します。 **[条件]** フィールドに *「VNet-A」* と入力します。 **[評価]** を選択して、どの仮想ネットワークがリストに表示されると確認します。 VNet-A-EastUS と VNet-A-WestUS のみが表示されます。

1. **[レビューと作成]** を選択してから、検証に成功したら **[作成]** を選択します。

> [!NOTE] 
> **基本エディター** は、ネットワーク グループの作成時のみに使用できます。 

## <a name="advanced-editor"></a>詳細エディター

ネットワーク グループの作成時または既存のネットワーク グループの更新時に詳細エディターを使用して、仮想ネットワークを選択できます。 

1. 前のセクションで作成したネットワーク グループを選択します。 次に、 **[条件付きステートメント]** タブを選択します。

1. 以下のように、[詳細エディター] ビューにネットワーク グループの条件付きステートメントが表示されます。

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          }
       ]
    }
    ```

    `"allOf"`パラメーターには、**AND** 論理演算子で分離された両方の条件付きステートメントが含まれています。

1. *WestUS* を *含まない***名前** フィールドに別の条件付きステートメントを追加するには、詳細エディターに以下を入力します：

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "notcontains": "WestUS"
          }
       ]
    }
    ```

1. 次に、  **[評価]** を選択します。 リストに VNet-A-EastUS 仮想ネットワークのみを見ます。

1. **[レビューと保存]** を選択してから、検証に成功したら **[保存]** を選択します。

詳細エディターで使用できるパラメーターと演算子の完全なリストについては、[「パラメーターと演算子」](#parameters) を参照してください。 その他の例については、以下を参照してください：

## <a name="more-examples"></a>その他の例

### <a name="example-1-or-operator-only"></a>例１: OR 演算子のみ

この例は、 **OR** 論理演算子の使用で二つの条件付きステートメントを分離します。

* 基本エディター:

    :::image type="content" source="./media/how-to-exclude-elements/or-operator.png" alt-text="OR 論理演算子を使用したネットワーク グループの条件付きステートメントのスクリーンショット。":::

* 詳細演算子:

    ```json
    {
       "anyOf": [
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "contains": "VNetA"
          }
       ]
    }
    ```

パラメーター `"anyOf"` には、**OR** 論理演算子で分離された両方の条件付きステートメントが含まれています。

### <a name="example-2-and-and-or-operator-at-the-same-time"></a>例２: AND 演算子と OR 演算子を同時に実行します

* 基本エディター:

    :::image type="content" source="./media/how-to-exclude-elements/both-operator.png" alt-text="OR と AND 論理演算子の両方を使用したネットワーク グループの条件付きステートメントのスクリーンショット。":::

* 詳細エディター:

```json
{
   "allOf": [
      {
         "anyOf": [
            {
               "field": "Name",
               "contains": "VNet-A"
            },
            {
               "field": "Name",
               "contains": "VNetA"
            }
         ]
      },
      {
         "field": "Name",
         "notcontains": "West"
      }
   ]
}
```

`"allOf"` と `"anyOf"` の両方がコードで使用されます。 **AND** 演算子はリストの最後にあるので、 **OR** 演算子を含む２つの条件付きステートメントを含むコードの外側の部分に含まれる。

## <a name="next-steps"></a>次の手順

- [ネットワーク グループ](concept-network-groups.md)について学びます。
- [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
