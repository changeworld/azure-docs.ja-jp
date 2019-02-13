---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823327"
---
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで **[すべてのサービス]** を選択し、**[モバイル]** セクションの **[Notification Hubs]** を選択します。 サービス名の横にある星印 [`*`] を選択して、左側のメニューの **[お気に入り]** セクションに追加します。 **Notification Hubs** が **[お気に入り]** に追加された後、左側のメニューでそれを選択します。 

      ![Azure portal - Notification Hubs を選択する](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. **[Notification Hubs]** ページで、ツール バーの **[追加]** を選択します。 

      ![Notification Hubs - [追加] ツール バー ボタン](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. **[Notification Hub]** ページで、次の手順のようにします。 
    1. 通知**ハブ**の**名前**を指定します。  
    2. **名前空間**の **名前** を指定します。
    3. 通知ハブを作成する**場所**を選択します。 
    4. 既存のリソース グループを選択するか、新しい**リソース グループ**の名前を入力します。
    5. **作成**を選択します。 

        ![Azure Portal - 通知ハブのプロパティを設定](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. **[通知]** (ベル アイコン) を選択して、**[リソースに移動]** を選択するか、または **[Notification Hubs]** ページで一覧を更新して、お使いの通知ハブを選択します。 

      ![Azure Portal > [通知] > [リソースに移動]](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. リストから **[アクセス ポリシー]** を選択します。 使用可能な 2 つの接続文字列を書き留めてください。 これらの接続文字列は、後でプッシュ通知を処理するために必要になります。

      >[!IMPORTANT]
      >DefaultFullSharedAccessSignature はアプリケーションで使用**しない**でください。 これはバックエンドのみで使用します。
      >

      ![Azure Portal - 通知ハブの接続文字列](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
