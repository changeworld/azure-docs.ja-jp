---
title: 管理グループを Microsoft Defender for Cloud にオンボードする
description: 提供されている Azure Policy 定義を使用して、管理グループ内のすべてのサブスクリプションで Azure Security Center を有効にする方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 88e57e988efbaaf0472bf809aeecb5ddeb12445c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422408"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>管理グループ内のすべてのサブスクリプションでDefenderforCloudを有効にする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Policy を使用すると、同じ管理グループ (MG) 内のすべての Azure サブスクリプションで Azure Security Center を有効にすることができます。 これは、ポータルから個別にアクセスするよりも便利であり、サブスクリプションが異なる所有者に属している場合でも機能します。 

管理グループとそのすべてのサブスクリプションをオンボードするには:

1. **セキュリティ管理者** のアクセス許可を持つユーザーとして Azure Policy を開き、 **[Enable Azure Security Center on your subscription]\(サブスクリプションで Azure Security Center を有効にする\)** 定義を検索します。

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="サブスクリプションAzure Policy Defender for Cloud を有効にするの定義です。":::

1. **[割り当て]** を選択し、スコープが MG レベルに設定されていることを確認します。

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="サブスクリプションAzure Policy Defender for Cloud を有効にするの定義です。":::

    > [!TIP]
    > スコープ以外に必要なパラメーターはありません。

1. **[Create a remediation task]\(修復タスクを作成する\)** を選択して、Security Center が有効になっていない既存のサブスクリプションすべてがオンボードされるようにします。

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="Azureポリシー定義の修復タスクを作成するサブスクリプションでDefenderforCloudを有効にします。":::

1. 定義が割り当てられると、次のようになります。

    1. Security Center にまだ登録されていない、MG 内のすべてのサブスクリプションが検出されます。
    1. それらのサブスクリプションが "非準拠" としてマークされます。
    1. すべての登録済みサブスクリプションを "準拠" としてマークします (Defender for Cloud の強化されたセキュリティ機能のオンとオフに関係なく)。

    修復タスクにより、非準拠のサブスクリプションでDefender forCloudが無料で有効になります。

> [!IMPORTANT]
> このポリシー定義で有効になるのは、**既存の** サブスクリプションの Security Center のみです。 新しく作成されたサブスクリプションを登録するには、[コンプライアンス] タブを開き、該当する非準拠サブスクリプションを選択して、修正タスクを作成します。Security Center で監視する新しいサブスクリプションが 1 つ以上ある場合は、この手順を繰り返してください。

## <a name="optional-modifications"></a>省略可能な変更

Azure Policy の定義を変更するには、さまざまな方法があります。 

- **コンプライアンスをさまざまな方法で定義する** - 提供されるポリシーでは、Security Center にまだ登録されていない、MG 内のすべてのサブスクリプションが "非準拠" として分類されます。 Defender for Cloud の強化されたセキュリティ機能が有効になっていないすべてのサブスクリプションに設定できます。

    提供されている定義では、以下の "価格" 設定の "*いずれか*" が準拠として定義されます。 これは、"standard" または "free" に設定されているサブスクリプションが準拠していることを意味します。

    > [!TIP]
    > Azure Defender プランが有効な場合は、"Standard" 設定としてポリシー定義に記述されます。 無効な場合は、"Free" になります。 これらのプランの違いについては、「Microsoft Defender for Cloud の強化されたセキュリティ機能 [」を参照してください](enhanced-security-features-overview.md)。 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    これを次のように変更すると、"standard" に設定されたサブスクリプションのみが準拠として分類されます。

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Defender for Cloud** を有効にするときに適用する Microsoft Defender プランの一部を定義する - 指定されたポリシーを使用すると、オプションの強化されたセキュリティ機能を使用せずに Defender for Cloud を有効にできます。 1 つ以上の Microsoft Defender プランを有効にできます。

    提供されている定義の `deployment` セクションには、`pricingTier` パラメーターがあります。 これは既定で `free` に設定されていますが、変更できます。 


## <a name="next-steps"></a>次のステップ:

管理グループ全体をオンボードしたので、強化されたセキュリティ機能を有効にしてください。 

> [!div class="nextstepaction"]
> [保護の強化を有効にする](enable-enhanced-security.md)