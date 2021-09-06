---
title: ポータルでの Azure Purview アカウントの作成に関してよく寄せられる質問 (FAQ)
description: この記事では、ポータルを使用した Purview アカウントの作成に関してよく寄せられる質問に回答します。
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398201"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>ポータルを使用した Purview アカウントの作成に関する FAQ

この記事では、Azure portal を使用した Purview アカウントの作成 ([Purview アカウントの作成](create-catalog-portal.md)) に関して顧客や現場のチームからよく寄せられる一般的な質問に回答します。

## <a name="common-questions"></a>一般的な質問

次の一般的な質問に対する回答を確認してください。

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>ストレージとイベント ハブ名前空間の作成を禁止する Azure Policy 

* **ストレージ アカウント** および **EventHub 名前空間** の作成をすべてのアプリケーションに禁止する **Azure Policy** がある場合は、タグを使用してポリシーの例外を作成する必要があります。これは、Purview アカウントを作成する過程で入力できます。 その主な理由は、作成した各 Purview アカウントでマネージド リソース グループを作成し、また、そのリソース グループ内にストレージ アカウントと EventHub 名前空間を作成する必要があるためです。

   >[!IMPORTANT]
   >Azure Policy をお持ちでない場合や、Azure Policy はあるものの、**ストレージ アカウント** と **EventHub 名前空間** の作成が禁止されていない場合、この手順を実行する必要はありません。

    1. Azure portal に移動し、 **[ポリシー]** を検索します。
    1. [カスタム ポリシー定義の作成](../governance/policy/tutorials/create-custom-policy-definition.md)に関するチュートリアルに従うか、`not` 演算子と `resourceBypass` タグを使用して既存のポリシーに 2 つの例外を追加します。

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > ポリシーでタグを検出できさえすれば、タグは `resourceBypass` 以外のものでもかまいません。後続の手順で Purview を作成する際に、ご自身で値を定義してください。

        :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="ポリシーの定義の作成方法を示すスクリーンショット。":::

    1. 作成したカスタム ポリシーを使用して、[ポリシーの割り当てを作成](../governance/policy/assign-policy-portal.md)します。

       :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="ポリシーの割り当ての作成方法を示すスクリーンショット" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> **Azure Policy** をご利用の場合で、かつ「**前提条件**」に記載したような例外を追加する必要がある場合は、適切なタグを追加する必要があります。 たとえば、`resourceBypass` タグを追加することができます。:::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="Purview アカウントにタグを追加する。":::

## <a name="next-steps"></a>次の手順

Private Link を使用して Azure Purview を設定するには、[Azure Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。
