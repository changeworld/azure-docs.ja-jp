---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371152"
---
1. [Azure Portal][lnk-portal] にサインインします。
1. **[リソースの作成]** > **[モノのインターネット]** > **[IoT Hub]** を選択します。
   
    ![Azure Portal での IoT Hub へのナビゲーションのスクリーン ショット][1]

1. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   * **[サブスクリプション]**: この IoT Hub を作成するために使用するサブスクリプションを選択します。

   * **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。

   * **[リージョン]**: 最も近い場所を選択します。

   * **[名前]**: IoT Hub の名前を作成します。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT Hub の基本ウィンドウ][2]

2. **[Next: Size and scale]\(次へ: サイズとスケール\)** を選択して、IoT Hub の作成を続けます。 

3. **[価格とスケールティア]** を選択します。 この記事では、**[F1 - Free]** レベルを選択します (サブスクリプションで引き続き使用可能な場合)。 詳細については、[価格とスケールティア][lnk-pricing]に関するページを参照してください。

   ![IoT Hub のサイズとスケール ウィンドウ][3]

4. **[Review + create]\(レビュー + 作成\)** を選択します。

1. IoT Hub 情報を確認してから、**[作成]** をクリックします。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を監視できます。
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md