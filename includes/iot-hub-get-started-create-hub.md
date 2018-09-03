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
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111953"
---
## <a name="create-an-iot-hub"></a>IoT Hub の作成
シミュレーション対象デバイス アプリの接続先となる IoT Hub を作成します。 次の手順では、この作業を Azure ポータルで行う方法を示しています。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[リソースの作成]** > **[モノのインターネット]** > **[IoT Hub]** を選択します。
   
    ![Azure portal Jumpbar](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   * **[サブスクリプション]**: この IoT ハブを作成するために使用するサブスクリプションを選択します。

   * **[リソース グループ]**: IoT Hub をホストするリソース グループを作成するか、既存のリソース グループを使用します。 詳細については、[リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-portal.md)に関するページを参照してください。

   * **[リージョン]**: 最も近い場所を選択します。

   * **[名前]**: IoT ハブの名前を作成します。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT ハブの基本ウィンドウ](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. **[Next: Size and scale]\(次へ: サイズとスケール\)** を選択して、IoT ハブの作成を続けます。 

5. **[価格とスケールティア]** を選択します。 この記事では、**[F1 - Free]** レベルを選択します (サブスクリプションで引き続き使用可能な場合)。 詳細については、[料金とスケール レベル](https://azure.microsoft.com/pricing/details/iot-hub/)に関するページを参照してください。

   ![IoT ハブのサイズとスケールのウィンドウ](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. **[Review + create]\(レビュー + 作成\)** を選択します。

7. IoT ハブの情報を確認してから、**[作成]** をクリックします。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を監視できます。

8. 新しい IoT ハブの準備ができたら、Azure Portal でそのタイルをクリックしてプロパティ ウィンドウを開きます。 IoT Hub を作成したら、その IoT Hub にデバイスとアプリケーションを接続するために必要な重要な情報を把握します。 **[共有アクセス ポリシー]** をクリックします。
   
9. **[共有アクセス ポリシー]** から **[iothubowner]** ポリシーを選びます。 後で使用するために IoT Hub の **[接続文字列 --- 主キー]** をコピーします。 詳細については、「IoT Hub 開発者ガイド」の[アクセス制御](../articles/iot-hub/iot-hub-devguide-security.md)に関するページを参照してください。
   
    ![共有アクセス ポリシー](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)