---
title: Azure マネージド ID を使用した環境の作成
description: Azure でマネージ ID を使用して、Azure DevTest Labs のラボに環境をデプロイする方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 29ebda2920dc6fce5596d9b8b535ef014fd2240e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595968"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure マネージド ID を使用してラボに環境をデプロイする 

ラボの所有者は、マネージド ID を使用してラボに環境をデプロイできます。 この機能は、環境にキー コンテナー、共有イメージ ギャラリー、ネットワークなど、環境のリソース グループの外部にある Azure リソースが含まれている場合、またはそれらのリソースを参照している場合に役立ちます。 これにより、その環境のリソース グループに限定されないサンドボックス環境を作成できるようになります。 

既定では、環境を作成すると、ラボは、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイするときに、ラボユーザーの代わりに Azure のリソースやサービスにアクセスするためのシステム割り当て ID が作成されます。 [ラボによりシステムによって割り当てられた ID が作成される理由](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity)について説明します。 新規および既存のラボでは、ラボ環境を初めて作成するときに、システムによって割り当てられた ID が既定で作成されます。  

ラボの所有者は、ラボのシステムによって割り当てられた ID アクセス許可にラボ外の Azure リソースへのアクセスを許可するか、ユーザーが自分で割り当てたシナリオの ID を使用するかを選択できます。 ラボのシステム割り当て ID は、ラボの有効期間中のみ有効です。 システムによって割り当てられた ID は、ラボを削除すると削除されます。 ID を使用する必要がある環境が複数のラボにある場合は、ユーザー割り当て ID の使用を検討してください。  

> [!NOTE]
> 現在、単一のユーザー割り当て ID がラボごとにサポートされています。 

## <a name="prerequisites"></a>前提条件

- [Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 
    
    ラボと同じリージョンおよびサブスクリプションでマネージド ID が作成されたことを確認します。 マネージド ID のリソース グループは同じである必要がありません。

## <a name="use-azure-portal"></a>Azure Portal の使用

このセクションでは、ラボの所有者として Azure portal を使用して、ユーザー マネージド ID をラボに追加します。 

1. [Azure portal](https://portal.azure.com) にサインインする
1. **DevTest Labs** を見つけます。
1. ラボの一覧から、目的のラボを選択します。
1. **[構成とポリシー]**  ->  **[ID (プレビュー)]** を選択します。 
1. ユーザー割り当て ID を追加するには、 **[ユーザー割り当て済み]** を選択します。
1. **[追加]** を押します。
1. 自分で作成した、あるいはアクセス権が与えられている既存ユーザーをドロップダウンから選択します。
 
    ![ユーザー マネージド ID の追加](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. ページ上部にある **[保存]** を押します。

    保存すると、ラボはすべてのラボ環境をデプロイするときにこの ID を使用します。 一覧から ID を選択することで、Azure の ID リソースにアクセスすることもできます。 

ラボに追加された ID が、環境でアクセスする必要がある外部リソースへのアクセス許可を持っている限り、ラボ所有者は、環境のデプロイ時に特別な操作を行う必要はありません。 

ラボに割り当てられているユーザー マネージド ID を変更するには、最初にラボに関連付けられている ID を削除してから、ラボに別の ID を追加します。 ラボに関連付けられている ID を削除するには、**[...] (省略記号)** を選択し、**[削除]** をクリックします。 

## <a name="use-api"></a>API を使用する

1. ID を作成したら、この ID のリソース ID をメモしておきます。 これは次のサンプルのようになります。 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. ラボ リソースに対して PUT HTTPS メソッドを実行し、ユーザー割り当ての ID を追加するか、ラボのシステムによって割り当てられた ID を有効にします。

   > [!NOTE]
   > ユーザー割り当て ID を作成するかどうかに関係なく、ラボでは、ラボ環境が初めて作成されたときに、システムによって割り当てられた ID が自動的に作成されます。 ただし、ユーザーが割り当てた ID がラボ用に既に構成されている場合、DevTest Lab サービスは引き続きその ID を使用してラボ環境をデプロイします。 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
ユーザー割り当て ID がラボに追加されると、Azure DevTest Labs サービスは Azure Resource Manager 環境をデプロイするときにそれを使用します。 たとえば、Resource Manager テンプレートで外部の共有イメージ ギャラリーのイメージにアクセスする必要がある場合は、ラボに追加した ID に、共有イメージ ギャラリー リソースに最低限必要なアクセス許可があることを確認してください。 
