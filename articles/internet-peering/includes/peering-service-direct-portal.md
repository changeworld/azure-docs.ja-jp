---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129939"
---
1. Peering Service で有効にするピアリング接続をクリックし、 **[...]**  >  **[接続の編集]** ボタンをクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の編集](../media/setup-direct-modify-editconnection.png)
1. ***[Use for Peering Service]\(Peering Service に使用\)*** で、 **[Enabled]\(有効\)** と **[保存]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続で Peering Service を有効にする](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. [概要] 画面にデプロイの詳細が表示されます。 デプロイが完了したら、 **[リソースに移動]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![デプロイが完了しました](../media/setup-direct-modify-overview-deployment-complete.png)

1. [設定] の下に **[Registered Prefixes]/(登録されたプレフィックス/)** が表示されます。 **[Add Registered Prefix]/(登録されたプレフィックスの追加/)** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![登録されたプレフィックスと接続](../media/setup-direct-modify-add-registered-prefix.png)
1. プレフィックスを登録するには、 **[名前]** と **[プレフィックス]** を選択し、 **[保存]** をクリックします
    > [!div class="mx-imgBorder"]
    >  ![プレフィックスを登録する](../media/setup-direct-modify-register-a-prefix.png) 

1. プレフィックスが作成されると、登録されたプレフィックスの一覧に表示されます。 プレフィックスの **[名前]** をクリックすると、詳細が表示されます。
    > [!div class="mx-imgBorder"]
    > ![登録されたプレフィックスと接続](../media/setup-direct-modify-registered-prefixes.png)
1. 登録されたプレフィックスのページで、プレフィックスごとの **[Prefix key]/(プレフィックス キー/)** を含んだ完全な詳細が表示されます。 このキーは、このプレフィックスが割り当てられた顧客に、そのプロバイダー ISP から提供する必要があります。 顧客は、このキーを使用してサブスクリプション内にプレフィックスを登録できます。
    > [!div class="mx-imgBorder"]
    > ![プレフィックス キーを持つプレフィックス](../media/setup-direct-modify-registered-prefix-detail.png)