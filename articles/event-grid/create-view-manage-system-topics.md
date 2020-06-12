---
title: Azure Event Grid でのシステム トピックの作成、表示、管理
description: この記事では、Azure portal を使用して既存のシステム トピックを表示したり、Azure Event Grid のシステム トピックを作成したりする方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: ac22afb351973397960e66c2a8fe86031e0b213a
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316343"
---
# <a name="create-view-and-manage-system-topics-in-azure-event-grid"></a>Azure Event Grid でのシステム トピックの作成、表示、管理
この記事では、次のタスクの手順について説明します。

- システム トピックを作成する
- すべての既存のシステム トピックを表示する 
- システム トピックを削除します
- システム トピックのイベント サブスクリプションを作成する


## <a name="create-a-system-topic"></a>システム トピックを作成する
Azure リソースのシステム トピックは、次の 2 つの方法で作成できます。

- [ストレージ アカウント] ページや [Event Hubs 名前空間] ページなどのリソース ページを使用する。 
- **[Event Grid System Topics]\(Event Grid システム トピック\)** ページを使用する。 

リソース ページを使用してシステム トピックを作成する例については、こちらの[クイックスタート](blob-event-quickstart-portal.md)を参照してください (Azure portal のリソース ページの **[イベント]** タブ)。 次の手順は、 **[Event Grid System Topics]\(Event Grid システム トピック\)** ページを使用してシステムトピックを作成するためのものです。 

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部の検索ボックスに「**Event Grid System Topics**」と入力し、**ENTER** キーを押します。 

    ![システム トピックの検索](./media/create-view-manage-system-topics/search-system-topics.png)
3. **[Event Grid System Topics]\(Event Grid システム トピック\)** ページで、ツールバーの **[+ 追加]** を選択します。

    ![システム トピックの追加 - ツールバー ボタン](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. **[Create Event Grid System Topic]\(Event Grid システム トピックの作成\)** ページで、次の手順を実行します。
    1. **トピックの種類**を選択します。 次の例では、**ストレージ アカウント**のオプションが選択されています。 
    2. 対象のストレージ アカウント リソースがある **Azure サブスクリプション**を選択します。 
    3. そのストレージ アカウントがある**リソース グループ**を選択します。 
    4. **ストレージ アカウント**を選択します。 
    5. 作成されるシステム トピックの**名前**を入力します。 
    
        > [!NOTE]
        > このシステム トピック名を使用して、メトリックと診断ログを検索できます。
    6. **[Review + create]\(レビュー + 作成\)** を選択します。

        ![システム トピックを作成する](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. 設定を確認し、 **[作成]** を選択します。 
        
        ![システム トピックを確認して作成する](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. デプロイが正常に完了したら、 **[リソースに移動]** を選択して、作成したシステム トピックの **[Event Grid System Topic]\(Event Grid システム トピック\)** ページを表示します。 

        ![システム トピック ページ](./media/create-view-manage-system-topics/system-topic-page.png)

## <a name="view-all-system-topics"></a>すべてのシステム トピックを表示する
既存のすべての Event Grid システム トピックを表示するには、次の手順に従います。 

> [!NOTE]
> 以前は、Azure のソースによって生成されるイベントのサブスクリプションを作成すると、Event Grid サービスによって、ランダムに生成された名前を持つシステム トピックが自動的に作成されていました。 今では、トピックの作成中にシステム トピックの名前を指定できるようになっています。 このシステム トピック リソースを使用して、メトリックと診断ログを検出できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部の検索ボックスに「**Event Grid System Topics**」と入力し、**ENTER** キーを押します。 

    ![システム トピックの検索](./media/create-view-manage-system-topics/search-system-topics.png)
3. **[Event Grid System Topics]\(Event Grid システム トピック\)** ページに、すべてのシステム トピックが表示されます。 

    ![システム トピックの一覧](./media/create-view-manage-system-topics/list-system-topics.png)
4. 一覧から**システム トピック**を選択すると、その詳細が表示されます。 

    ![システム トピックの詳細](./media/create-view-manage-system-topics/system-topic-details.png)

    このページには、システム トピックに関する次のような情報が表示されます。 
    - ソース。 システム トピックの作成元のリソースの名前。
    - ソースの種類。 リソースの種類。 たとえば、`Microsoft.Storage.StorageAccounts`、`Microsoft.EventHub.Namespaces`、`Microsoft.Resources.ResourceGroups` などです。
    - システム トピックに対して作成されたすべてのサブスクリプション。

    このページでは、次のような操作を実行できます。
    - イベント サブスクリプションを作成する。ツールバーの **[+ イベント サブスクリプション]** を選択します。 
    - イベント サブスクリプションを削除する。 ツールバーの **[削除]** を選択します。 
    - システム トピックのタグを追加する。 左側のメニューの **[タグ]** を選択し、タグの名前と値を指定します。 


## <a name="delete-a-system-topic"></a>システム トピックを削除します
1. [システム トピックの表示](#view-all-system-topics)に関するセクションの指示に従って、すべてのシステム トピックを表示し、一覧から削除するシステム トピックを選択します。 
2. **[Event Grid System Topics]\(Event Grid システム トピック\)** ページで、ツールバーの **[削除]** を選択します。 

    ![システム トピック - [削除] ボタン](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. 確認ページで、 **[OK]** を選択して削除を確定します。 そのシステム トピックとそのシステム トピックのすべてのイベント サブスクリプションが削除されます。  

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成
1. [システム トピックの表示](#view-all-system-topics)に関するセクションの指示に従って、すべてのシステム トピックを表示し、一覧から削除するシステム トピックを選択します。 
2. **[Event Grid System Topics]\(Event Grid システム トピック\)** ページで、ツールバーの **[+ イベント サブスクリプション]** を選択します。 

    ![システム トピック - [+ イベント サブスクリプション] ボタン](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. **[トピックの種類]** 、 **[ソース リソース]** 、 **[トピック名]** が自動的に入力されていることを確認します。 名前を入力し、 **[エンドポイントのタイプ]** を選択して、**エンドポイント**を指定します。 これで、 **[作成]** を選択し、イベント サブスクリプションを作成します。 

    ![システム トピック - イベント サブスクリプションの作成](./media/create-view-manage-system-topics/create-event-subscription.png)


## <a name="next-steps"></a>次のステップ
Azure Event Grid でサポートされるシステム トピックとトピックの種類の詳細については、「[Azure Event Grid でのシステム トピック](system-topics.md)」セクションを参照してください。 
