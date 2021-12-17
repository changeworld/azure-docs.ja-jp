---
title: Azure Event Grid のシステム トピックでマネージド ID を有効にする
description: この記事では、Azure Event Grid のシステム トピックに対してマネージド サービス ID を有効にする方法を説明します。
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 825b1d22568b110f22e596d424a2f781b65de625
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157146"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>システムマネージド ID を Event Grid のシステム トピックに割り当てる
この記事では、Event Grid システムのトピックに、システム割り当てまたはユーザー割り当ての ID を割り当てる方法について説明します。 通常、マネージド ID については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。  

> [!NOTE]
> - システム トピックには、1 つのシステム割り当て ID と最大 2 つのユーザー割り当て ID を割り当てることができます。 
> - Azure サブスクリプション、リソース グループ、Azure Maps など、グローバルな Azure リソースに関連付けられているシステム トピックに対して、ID を有効にすることができます。 これらのグローバル ソースのシステム トピックは、特定のリージョンにも関連付けられていません。

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>既存のシステム トピックのマネージド ID を有効にする
このセクションでは、既存のシステム トピックに対してマネージド ID を有効にする方法について説明します。 

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 上部の検索バーで、**Event Grid システム トピック** を検索します。
3. マネージド ID を有効にする **システム トピック** を選択します。 
4. 左側のメニューの **[ID]** を選択します。  

### <a name="enable-system-assigned-identity"></a>システム割り当て ID を有効にする
1. スイッチを **オン** にして、ID を有効にします。 
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="システム トピックの ID ページ。"::: 
1. 確認メッセージで **[はい]** を選択します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="システム トピックへの ID の割り当て - 確認。"::: 
1. システム割り当てのマネージド ID のオブジェクト ID が表示されていること、およびロールを割り当てるリンクが表示されていることを確認します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="システム トピックへの ID の割り当て - 完了。"::: 

### <a name="enable-user-assigned-identity"></a>ユーザー割り当て ID を有効にする

1. まず、「[ユーザー割り当てマネージド ID の管理](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)」の記事の手順に従って、ユーザー割り当て ID を作成します。 
1. **[ID]** ページで、右ペインの **[ユーザー割り当て済み]** タブに切り替え、ツール バーの **[+ 追加]** を選択します。

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="[ID] ページの [ユーザー割り当て済み] タブで選択された [追加] ボタンを示す画像。":::
1. **[ユーザー マネージド ID の追加]** ウィンドウで、次の手順を実行します。
    1. ユーザー割り当て ID を持つ **Azure サブスクリプション** を選択します。 
    1. **ユーザー割り当て ID** を選択します。 
    1. **[追加]** を選択します。 
1. **[ユーザー割り当て済み]** タブの一覧を更新し、追加されたユーザー割り当て ID を確認します。

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>システム トピックの作成時にマネージド ID を有効にする

1. Azure portal の検索バーで、**Event Grid システム トピック** を検索して選択します。 
1. **[Event Grid System Topics]\(Event Grid システム トピック\)** ページで、ツールバーの **[作成]** を選択します。 
1. 作成ウィザードの **[基本]** ページで、次の手順を実行します。 
    1. **[Topic Types]\(トピックの種類\)** で、システム トピックをサポートするトピックの種類を選択します。 次の例では、 **[ストレージ アカウント]** が選択されています。 
    2. **[サブスクリプション]** で、Azure リソースを含む Azure サブスクリプションを選択します。 
    1. **[リソース グループ]** で、Azure リソースを含むリソース グループを選択します。 
    1. **[リソース]** で、リソースを選択します。 
    1. システム トピックの **名前** を指定します。
    1. マネージド ID を有効にします。
        1. システム割り当て ID を有効にするには、 **[Enable system assigned identity]\(システム割り当て ID を有効にする\)** を選択します。 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="システム割り当て ID オプションが選択されたシステム トピックの作成ウィザードのスクリーンショットを示す画像。":::            
        1. ユーザー割り当て ID を有効にするには: 
            1. **[ユーザー割り当て ID]** を選択し、 **[Add user ID]\(ユーザー ID の追加\)** を選択します。 
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="[ユーザー割り当て ID] オプションが選択されたシステム トピックの作成ウィザードのスクリーンショットを示す画像。":::            
            1. **[ユーザー マネージド ID の追加]** ウィンドウで、次の手順を実行します。
                1. ユーザー割り当て ID を持つ **Azure サブスクリプション** を選択します。 
                1. **ユーザー割り当て ID** を選択します。 
                1. **[追加]** を選択します。                         

> [!NOTE]
> - 現在、Azure portal では、システム トピックを作成するときに、システム割り当て ID とユーザー割り当て ID の両方を割り当てることはできません。 システム トピックの作成後に両方を割り当てることができます。 
> - 現在、システム トピックをサポートする Azure リソースに対してイベント サブスクリプションを作成するときに、新しいシステム トピックのマネージド ID を有効にすることはできません。 


## <a name="next-steps"></a>次の手順
配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細な手順については、「[マネージド ID に Event Grid の配信先へのアクセスを許可する](add-identity-roles.md)」を参照してください。 