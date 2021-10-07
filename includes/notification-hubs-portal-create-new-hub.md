---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/07/2021
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d33df05e54d9cdeb97631ca479dd8ac9869343c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "126057231"
---
1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[モバイル]** セクションの **[Notification Hubs]** を選択します。 サービス名の横にある星のアイコンを選択して、左側のメニューにある **[お気に入り]** セクションにサービスを追加します。 **Notification Hubs** を **[お気に入り]** に追加した後、左側のメニューでそれを選択します。

      ![Azure portal - Notification Hubs を選択する](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. **[Notification Hubs]** ページで、ツール バーの **[作成]** を選択します。

      ![Notification Hubs - [追加] ツール バー ボタン](./media/notification-hubs-portal-create-new-hub/create-toolbar-button.png)

1. **[Notification Hub]** ページの **[基本]** タブで、次の手順を行います。

    1. **[サブスクリプション]** で、使用する Azure サブスクリプションの名前を選択し、既存のリソース グループを選択するか、新しいリソース グループを作成します。  

    1. **[名前空間の詳細]** に新しい名前空間の一意の名前を入力します。 
    
    1. 名前空間には 1 つ以上の通知ハブが含まれているので、 **[通知ハブの詳細]** にそのハブの名前を入力します。 または、ドロップダウンから既存の名前空間を選択します。

    1. **[場所]** ドロップダウン リスト ボックスで値を選択します。 この値には、ハブを作成する場所を指定します。

    1. **作成** を選択します。

        ![Azure Portal - 通知ハブのプロパティを設定](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. **[通知]** (ベルのアイコン) を選択し、 **[リソースに移動]** を選択します。 **[Notification Hubs]** ページの一覧を最新の情報に更新して、お使いの通知ハブを選択することもできます。

      ![Azure portal - リソースに移動](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. リストから **[アクセス ポリシー]** を選択します。 使用可能な 2 つの接続文字列を書き留めてください。 これらは、後でプッシュ通知を処理するために必要になります。

      >[!IMPORTANT]
      >**DefaultFullSharedAccessSignature** ポリシーはアプリケーションで使用 "*しない*" でください。 これの使用が意図されているのはバックエンドだけです。
      >

      ![Azure Portal - 通知ハブの接続文字列](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
