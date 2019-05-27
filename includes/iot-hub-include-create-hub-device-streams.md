---
title: インクルード ファイル (デバイス ストリーム プレビュー)
description: インクルード ファイル (デバイス ストリーム プレビュー)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ede897054a6cbef254c06bd1d810b933ec09016a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158667"
---
ここでは、[Azure portal](https://portal.azure.com) を使用して IoT ハブを作成する方法について説明します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. [+ **リソースの作成]**、**[モノのインターネット]** の順に選択します。

3. 右側のリストにある **[Iot Hub]** をクリックします。 IoT ハブを作成するための最初の画面が表示されます。

   ![Azure Portal での Hub の作成を示したスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   フィールドに入力します。

   **サブスクリプション**:IoT ハブで使用するサブスクリプションを選択します。

   **リソース グループ**:新しいリソース グループを作成するか、既存のリソース グループを使用することができます。 新しいものを作成するには、**[新規作成]** をクリックして、使用する名前を入力します。 既存のグループを使用するには、**[既存のものを使用]** をクリックし、ドロップダウン リストからリソース グループを選択します。 詳しくは、[「Manage Azure Resource Manager resource groups (Azure Resource Manager のリソース グループの管理)](../articles/azure-resource-manager/manage-resource-groups-portal.md)」をご覧ください。

   **[リージョン]**:ハブを配置するリージョンです。 IoT Hub デバイス ストリーム (プレビュー) がサポートされているリージョンとして、米国中部または米国中部 EUAP を選択します。

   **[IoT Hub 名]**: IoT Hub の名前を入力します。 この名前はグローバルに一意である必要があります。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. **[次へ: Size and scale]\(次へ: サイズとスケール\)** をクリックして、IoT ハブの作成を続けます。

   ![Azure portal を使用して新しい IoT ハブ用のサイズとスケールを設定する様子を示すスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   この画面では、既定値を使用して、下部にある **[確認および作成]** をクリックするだけです。

   **[価格とスケールティア]**: Standard (S1、S2、S3) のいずれかのレベルまたは Free (F1) レベルを選択します。 この選択は、フリートのサイズと、ハブで予想されるストリーミング以外のワークロード (テレメトリ メッセージなど) によっても左右されます。 たとえば、無料レベルは、テストおよび評価用です。 IoT Hub に接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、無料レベルの IoT Hub を 1 つ作成できます。 

   **[IoT Hub ユニット]**: この選択は、ハブで予想されるストリーミング以外のワークロードによって決まります。ここでは 1 を選択することができます。

   他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

5. **[確認および作成]** をクリックして、選択内容を確認します。 次の画面のようになります。

   ![新しい IoT ハブを作成するための情報を確認するスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

6. **[作成]** をクリックして、新しい IoT ハブを作成します。 ハブの作成には数分かかります。
