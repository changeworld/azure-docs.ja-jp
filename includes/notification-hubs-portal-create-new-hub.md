---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19ea9c749b58f6f81dc2087caa77573062d883b5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39485957"
---
1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]** > **[モバイル]** > **[通知ハブ]** を選択します。
   
      ![Azure Portal - 通知ハブを作成](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
1. **[Notification Hub]** ボックスに一意の名前を入力します。 **リージョン**、**サブスクリプション**、**リソース グループ**を選択します (既にある場合)。 
   
      まだ Service Bus 名前空間を用意していない場合、ハブ名に基づいて作成される既定の名前を使用できます (その名前空間名が使用できる場合)。
    
      ハブを作成する Service Bus 名前空間を既に用意してある場合は、次の手順に従います。

    a. **[名前空間]** 領域で、**[既存項目の選択]** リンクを選択します。 
   
    b. **作成**を選択します。
   
      ![Azure Portal - 通知ハブのプロパティを設定](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. **[通知]** (ベルのアイコン)、**[リソースに移動]** の順に選択します。 

      ![Azure Portal > [通知] > [リソースに移動]](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
1. リストから **[アクセス ポリシー]** を選択します。 使用可能な 2 つの接続文字列を書き留めてください。 これらの接続文字列は、後でプッシュ通知を処理するために必要になります。

      >[!IMPORTANT]
      >DefaultFullSharedAccessSignature はアプリケーションで使用**しない**でください。 これはバックエンドのみで使用します。
      >
   
      ![Azure Portal - 通知ハブの接続文字列](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

