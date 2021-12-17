---
title: Azure Purview の Azure Policy 例外を作成する
description: このアーティクルでは、セキュリティを維持するために既存のポリシーをそのまま残しながら、Purview の Azure Policy 例外を作成する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111916"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>Purview の Azure Policy 例外を作成する

多くのサブスクリプションには、一部のリソースの作成を制限する [Azure Policies](../governance/policy/overview.md) が設定されています。 これは、サブスクリプションのセキュリティとクリーンさを維持するためです。 ただし、Purview アカウントは、2 つの Azure リソース (Azure Storage アカウントとイベント ハブ名前空間) を作成したときにデプロイします。 [Purview アカウントを作成](create-catalog-portal.md)すると、これらのリソースがデプロイされます。 これらは Azure によって管理されるため、管理する必要はありませんが、デプロイする必要があります。

サブスクリプションでポリシーを維持しながら、これらのマネージド リソースを作成できるようにするには、ポリシーの例外を作成します。

## <a name="create-a-policy-exception-for-purview"></a>Purview のポリシー例外を作成する

1. [Azure portal](https://portal.azure.com) に移動し、 **[ポリシー]** を検索します

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="Azure portal の検索バーで、ポリシー キーワードを検索しているスクリーンショット。":::

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

## <a name="next-steps"></a>次のステップ

Private Link を使用して Azure Purview を設定するには、[Azure Purview アカウントにプライベート エンドポイントを使用する](./catalog-private-link.md)方法に関する記事を参照してください。
