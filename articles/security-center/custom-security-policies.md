---
title: Microsoft Defender for Cloud でカスタムセキュリティポリシーを作成する |Microsoft Docs
description: Microsoft Defender for Cloud によって監視される Azure カスタムポリシー定義。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: b63529ca18f1b0bc09a173c814ede9cc71a5a7ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422541"
---
# <a name="create-custom-security-initiatives-and-policies"></a>カスタム セキュリティ イニシアチブとポリシーを作成する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

システムと環境をセキュリティで保護するために、Microsoft Defender for Cloud はセキュリティに関する推奨事項を生成します。 これらの推奨事項は業界のベスト プラクティスに基づいており、すべてのお客様に提供される既定の汎用セキュリティ ポリシーに組み込まれています。 また、業界および規制標準に関するクラウドの知識についても、Defender から入手できます。

この機能を使用して、独自の "*カスタム*" イニシアティブを追加できます。 作成したポリシーに環境が従っていない場合は、推奨事項が提供されます。 作成したカスタム イニシアチブは、「[規制に対するコンプライアンスの向上](regulatory-compliance-dashboard.md)」チュートリアルで説明されている規制コンプライアンス ダッシュボードの組み込みイニシアチブと一緒に表示されます。

[Azure Policy のドキュメント](../governance/policy/concepts/definition-structure.md#definition-location)で説明されているように、カスタム イニシアチブの場所を指定するとき、その場所は管理グループまたはサブスクリプションである必要があります。 

> [!TIP]
> このページの主要な概念の概要については、「[セキュリティポリシー、イニシアチブ、推奨事項とは](security-policy-concept.md)」を参照してください。

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>イニシアティブをサブスクリプションに追加するには 

1. Defender for Cloud のメニューから、[セキュリティ ポリシー] **を選択します**。

1. カスタム イニシアティブを追加するサブスクリプションまたは管理グループを選択します。

    [![カスタム ポリシーを作成するサブスクリプションの選択。](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > カスタムイニシアチブをクラウドの Defender で評価して表示するには、サブスクリプションレベル (またはそれ以降) でカスタムイニシアチブを追加する必要があります。 使用可能な最も広いスコープを選択することをお勧めします。

1. [セキュリティ ポリシー] ページの [Your custom initiatives]\(カスタム イニシアティブ\) で、 **[カスタム イニシアティブの追加]** をクリックします。

    [![[カスタム イニシアティブの追加] をクリック。](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    次のページが表示されます。

    ![ポリシーを作成または追加。](media/custom-security-policies/create-or-add-custom-policy.png)

1. [カスタム イニシアティブの追加] ページで、組織で既に作成されているカスタム ポリシーの一覧を確認します。 サブスクリプションに割り当てるイニシアティブが表示されている場合は、 **[追加]** をクリックします。 ニーズに合ったイニシアティブが一覧にない場合は、この手順をスキップしてください。

1. 新しいカスタム イニシアティブを作成するには、次の手順に従います。

    1. **[新規作成]** をクリックします。
    1. 定義の場所と名前を入力します。
    1. 含めるポリシーを選択し、 **[追加]** をクリックします。
    1. 必要なパラメーターを入力します。
    1. **[保存]** をクリックします。
    1. [カスタム イニシアティブの追加] ページで、更新をクリックします。 新しいイニシアティブが使用可能として表示されます。
    1. **[追加]** をクリックして、サブスクリプションに割り当てます。

    > [!NOTE]
    > 新しいイニシアティブを作成するには、サブスクリプション所有者の資格情報が必要です。 Azure ロールの詳細については、「 [Microsoft Defender For Cloud のアクセス許可](permissions.md)」を参照してください。

    新しいイニシアチブが有効になり、次の 2 つの方法で影響を確認できます。

    * Defender for Cloud メニューの  **規制遵守** を選択します。 コンプライアンス ダッシュボードが開き、組み込みイニシアチブと一緒に新しいカスタム イニシアチブが表示されます。
    
    * 定義したポリシーに環境が従っていない場合は、推奨事項を受け取り始めます。

1. ポリシーの生成された推奨事項を表示するには、サイドバーの **[推奨事項]** をクリックして推奨事項ページを開きます。 推奨事項は [カスタム] ラベル付きで表示され、約 1 時間以内に提供されます。

    [![カスタム推奨事項。](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>REST API を使用して Azure Policy でセキュリティ ポリシーを構成する

Microsoft Defender for Cloudでは、Azure Policyとのネイティブな統合の一環として、Azure PolicyのREST APIを利用してポリシー割り当てを作成することができます。 次の手順では、ポリシー割り当ての作成と既存の割り当てのカスタマイズの手順について説明します。 

Azure Policy で重要な概念: 

- **ポリシー定義** はルールです 

- **イニシアティブ** はポリシー定義 (ルール) のコレクションです 

- **割り当て** は、特定のスコープ (管理グループ、サブスクリプションなど) にイニシアティブまたはポリシーを適用することです 

Defender for Cloud には、すべてのセキュリティポリシーを含む組み込みのイニシアチブ、 [Azure セキュリティベンチマーク](/security/benchmark/azure/introduction)があります。 Azureリソースに対するDefender for Cloudのポリシーを評価するには、評価したい管理グループ、またはサブスクリプションに割り当てを作成する必要があります。

ビルトインの取り組みでは、Defender for Cloudのすべてのポリシーがデフォルトで有効になっています。 組み込みのイニシアチブから特定のポリシーを無効にすることもできます。 たとえば、**Web アプリケーション ファイアウォール** を除くすべての Security Center のポリシーを適用するには、ポリシーの effect パラメーターの値を **Disabled** に変更します。

## <a name="api-examples"></a>API の例

以下の例では、次のように変数を置き換えてください。

- **{scope}** には、ポリシーを適用する管理グループまたはサブスクリプションの名前を入力します
- **{poicyAssignmentName}** には、関連するポリシー割り当ての名前を入力します
- **{name}** には、名前、またはポリシーの変更を承認した管理者の名前を入力します

この例では、サブスクリプションまたは管理グループに組み込みの Defender for Cloud イニシアチブを割り当てる方法を示します。
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

この例では、次のポリシーを無効にして、サブスクリプションに組み込みの Defender for Cloud イニシアチブを割り当てる方法を示します。 

- システムの更新プログラム ("systemUpdatesMonitoringEffect") 

- セキュリティ構成 ("systemConfigurationsMonitoringEffect") 

- エンドポイント保護 ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Microsoft Defender for Cloud", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
この例では、割り当てを削除する方法を示します。
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>詳細情報でのカスタム推奨事項の拡張

Microsoft Defender for Cloud に用意されている組み込みの推奨事項には、重大度レベルや修復手順などの詳細が含まれています。 この種の情報をカスタム推奨事項に追加して、Azure portal や、推奨事項にアクセスする場所で表示されるようにする場合は、REST API を使用する必要があります。 

追加できる情報は次の 2 種類です。

- **RemediationDescription** – 文字列
- **Severity** – 列挙 [Low、Medium、High]

カスタム イニシアティブの一部であるポリシーのポリシー定義に、メタデータを追加する必要があります。 次のように、"securityCenter" プロパティに含める必要があります。

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

次に示すのは、metadata と securityCenter プロパティが含まれカスタム ポリシーの例です。

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

securityCenter プロパティを使用する別の例については、[REST API のドキュメントのこちらのセクション](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)をご覧ください。


## <a name="next-steps"></a>次のステップ

この記事では、カスタム セキュリティ ポリシーの作成方法について説明しました。 

その他の関連資料については、次の記事を参照してください。 

- [セキュリティ ポリシーの概要](tutorial-security-policy.md)
- [組み込みのセキュリティ ポリシーの一覧](./policy-reference.md)