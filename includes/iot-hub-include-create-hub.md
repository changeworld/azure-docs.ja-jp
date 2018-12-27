---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b1f1f8144ec5b65c21c5ddcecd278a018260047c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515999"
---
ここでは、[Azure portal](https://portal.azure.com) を使用して IoT ハブを作成する方法について説明します。

1. [Azure Portal](https://portal.azure.com) にログインします。 

2. [+ **リソースの作成]**、**[モノのインターネット]** の順に選択します。

3. 右側のリストにある **[Iot Hub]** をクリックします。 IoT ハブを作成するための最初の画面が表示されます。

   ![Azure Portal での Hub の作成を示したスクリーンショット](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   フィールドに入力します。

   **サブスクリプション**: IoT ハブで使用するサブスクリプションを選択します。

   **リソース グループ**: 新しいリソース グループを作成することも、既存のものを使用することもできます。 新しいものを作成するには、**[新規作成]** をクリックして、使用する名前を入力します。 既存のグループを使用するには、**[既存のものを使用]** をクリックし、ドロップダウン リストからリソース グループを選択します。 詳細については、[リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-portal.md)に関するページを参照してください。

   **リージョン**: ハブを配置するリージョンです。 最も近い場所をドロップダウン リストから選択します。

   **Iot Hub 名**: IoT Hub の名前を入力します。 この名前はグローバルに一意である必要があります。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. **[Next: Size and scale]\(次へ: サイズとスケール\)** をクリックして、IoT ハブの作成を続けます。

   ![Azure portal を使用して新しい IoT ハブ用のサイズとスケールを設定する様子を示すスクリーンショット](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   この画面では、既定値を使用して、下部にある **[確認および作成]** をクリックするだけです。 

   **価格とスケール ティア**: 必要な機能の数とソリューションで 1 日に送信するメッセージの数に応じて、複数のレベルから選ぶことができます。 無料レベルは、テストおよび評価用です。 IoT Hub に接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、無料レベルの IoT Hub を 1 つ作成できます。 

   **IoT Hub ユニット**: ユニットごとに許可される 1 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、IoT Hub で 700,000 件の受信メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。

   他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

   **高度なパーティションと Device-to-cloud パーティション**: このプロパティでは、device-to-cloud メッセージがそのメッセージの同時閲覧者数に関連付けられます。 ほとんどの IoT ハブでは、4 つのパーティションのみが必要となります。 

5. **[確認および作成]** をクリックして、選択内容を確認します。 次の画面のようになります。

   ![新しい IoT ハブを作成するための情報を確認するスクリーンショット](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. **[作成]** をクリックして、新しい IoT ハブを作成します。 ハブの作成には数分かかります。
