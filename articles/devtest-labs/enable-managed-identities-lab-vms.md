---
title: ラボ VM でマネージド ID を有効にする
description: この記事では、ラボ所有者がラボ仮想マシン上でユーザー割り当てのマネージド ID を有効にする方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 824fc69fd7ff1b8d48de98b16daccc055aa34283
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624275"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs のラボ仮想マシン上でユーザー割り当てのマネージド ID を有効にする
ラボ所有者は、ラボ仮想マシン (VM) 上の Azure DevTest Labs でユーザー割り当てのマネージド ID を有効にすることができます。

マネージド ID を使用すると、コードで資格情報を渡さずに、Key Vault など、Azure Active Directory (AD) 認証をサポートする任意のサービスに対して認証を行うことができます。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

この機能を使用すると、ラボ ユーザーはラボのコンテキストで Azure SQL Database などの Azure リソースを共有できます。 リソースに対する認証は、ID 自体によって処理されます。 構成が完了すると、既存または新規に作成されたすべてのラボ VM がこの ID を使用して有効になります。 ラボ ユーザーは、マシンにログインした後にリソースにアクセスできるようになります。

> [!NOTE]
> ラボ VM では、複数のユーザー割り当て済みマネージド ID を有効にすることができます。

## <a name="use-azure-portal"></a>Azure Portal の使用
ラボ VM のユーザー割り当て済みマネージド ID を追加するには、次の手順を実行します。

1. [ユーザー割り当て済みマネージド ID をサブスクリプションに作成します](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. ラボの **[構成とポリシー]** ページに移動します。
1. 左側のメニューの **[ID (プレビュー)]** を選択します。
1. **[仮想マシン]** タブを選択します。
1. **[追加]** を選択して、事前設定されたドロップダウン リストから既存の ID を選択します。 

    > [!div class="mx-imgBorder"]
    > ![ID の[追加] ボタン](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. ドロップダウン リストから既存の **ユーザー マネージド ID** を選択し、 **[OK]** を選択します。 

    > [!div class="mx-imgBorder"]
    > ![ID を追加する](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API を使用する

1.  ID を作成したら、この ID のリソース ID をメモしておきます。 これは次のサンプルのようになります。 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.
    
2. ラボ リソースで PUT HTTPS メソッドを実行して、1 つまたは複数のユーザー割り当て ID を **managementIdentities** フィールドに追加します。


    ```json
    {
        "location": "southeastasia",
        "properties": {
        ...
            "managementIdentities": {
               "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}": {}
        },
        ...
        },
    ...
    }
    ```

## <a name="next-steps"></a>次のステップ
マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。
