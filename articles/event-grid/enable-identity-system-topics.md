---
title: Azure Event Grid のシステム トピックでマネージド ID を有効にする
description: この記事では、Azure Event Grid のシステム トピックに対してマネージド サービス ID を有効にする方法を説明します。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280515"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>システムマネージド ID を Event Grid のシステム トピックに割り当てる
この記事では、既存の Event Grid のシステム トピックに対してシステムマネージド ID を有効にする方法について説明します。 マネージド ID については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。  

> [!IMPORTANT]
> 現在、新しいシステム トピックを作成するとき、つまり、システム トピックをサポートする Azure リソースに対してイベント サブスクリプションを作成するときに、システムマネージド ID を有効にすることはできません。 


## <a name="use-azure-portal"></a>Azure Portal の使用
次の手順では、システム トピックに対してシステムマネージド ID を有効にする方法を示します。 

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 上部の検索バーで、**Event Grid システム トピック** を検索します。
3. マネージド ID を有効にする **システム トピック** を選択します。 
4. 左側のメニューの **[ID]** を選択します。 グローバル ロケーションにあるシステム・トピックに関しては、このオプションは表示されません。 
5. スイッチを **オン** にして、ID を有効にします。 
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="システム トピックの ID ページ"::: 
1. 確認メッセージで **[はい]** を選択します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="システム トピックへの ID の割り当て - 確認"::: 
1. システム割り当てのマネージド ID のオブジェクト ID が表示されていること、およびロールを割り当てるリンクが表示されていることを確認します。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="システム トピックへの ID の割り当て - 確認"::: 

## <a name="global-azure-sources"></a>グローバルな Azure ソース
システムマネージド ID は、リージョンの Azure リソースに対してのみ有効にすることができます。 Azure サブスクリプション、リソース グループ、Azure Maps など、グローバルな Azure リソースに関連付けられているシステム トピックに対して、これを有効にすることはできません。 これらのグローバル ソースのシステム トピックは、特定のリージョンにも関連付けられていません。 場所が **[GLOBAL]** に設定されているシステム トピックの **[ID]** ページは表示されません。 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="場所が [GLOBAL] に設定されたシステム トピック"::: 



## <a name="next-steps"></a>次の手順
配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。 詳細な手順については、「[マネージド ID に Event Grid の配信先へのアクセスを許可する](add-identity-roles.md)」を参照してください。 