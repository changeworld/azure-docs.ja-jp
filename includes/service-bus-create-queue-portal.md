---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702638"
---
[こちら][namespace-how-to]の説明に従って、Service Bus 名前空間を既に作成していることを確認してください。

1. [Azure Portal][azure-portal] にサインインします。
2. ポータルの左側のナビゲーション ウィンドウで、**[Service Bus]** をクリックします (**[Service Bus]** が表示されていない場合は **[すべてのサービス]** をクリックします)。
3. キューを作成する名前空間をクリックします。 この例では、**sbnstest1** です。
   
    ![キューを作成する][createqueue1]
4. 名前空間ウィンドウで **[キュー]** をクリックし、**[キュー]** ウィンドウの **[+ キュー]** をクリックします。
   
    ![Select Queues][createqueue2]
5. **[キュー名]** に入力し、他の値は既定値のままにします。
   
    ![Select New][createqueue3]
6. ウィンドウの下部にある **[作成]** をクリックします。

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
