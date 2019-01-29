---
title: インクルード ファイル (デバイス ストリーム プレビュー)
description: インクルード ファイル (デバイス ストリーム プレビュー)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 53d8df7e2366cfbf2f62e79fc99c8ef2f9b48ba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830813"
---
ここでは、[Azure portal](https://portal.azure.com) を使用して IoT ハブを作成する方法について説明します。

1. [Azure Portal](https://portal.azure.com) にログインします。 

2. [+ **リソースの作成]**、**[モノのインターネット]** の順に選択します。

3. 右側のリストにある **[Iot Hub]** をクリックします。 IoT ハブを作成するための最初の画面が表示されます。

   ![Azure Portal での Hub の作成を示したスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   フィールドに入力します。

   **サブスクリプション**:IoT ハブで使用するサブスクリプションを選択します。

   **リソース グループ**:新しいリソース グループを作成するか、既存のリソース グループを使用することができます。 新しいものを作成するには、**[新規作成]** をクリックして、使用する名前を入力します。 既存のグループを使用するには、**[既存のものを使用]** をクリックし、ドロップダウン リストからリソース グループを選択します。 詳細については、[リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-portal.md)に関するページを参照してください。

   **[リージョン]**:ハブを配置するリージョンです。 必ず、サポートされている地域 (米国中部、米国中部 EUAP など) を選択します。

   **[IoT Hub 名]**: IoT Hub の名前を入力します。 この名前はグローバルに一意である必要があります。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. **[Next: Size and scale]\(次へ: サイズとスケール\)** をクリックして、IoT ハブの作成を続けます。

   ![Azure portal を使用して新しい IoT ハブ用のサイズとスケールを設定する様子を示すスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   この画面では、既定値を使用して、下部にある **[確認および作成]** をクリックするだけです。 

   **[価格とスケールティア]**: Standard (S1、S2、S3) のいずれかのレベルまたは Free (F1) レベルを選択します。 この選択は、フリートのサイズと、ハブで予想されるストリーミング以外のワークロード (テレメトリ メッセージなど) によっても左右されます。 たとえば、無料レベルは、テストおよび評価用です。 IoT Hub に接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、無料レベルの IoT Hub を 1 つ作成できます。 

   **[IoT Hub ユニット]**: この選択は、ハブで予想されるストリーミング以外のワークロードによって決まります。ここでは 1 を選択することができます。

   他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](../articles/iot-hub/iot-hub-scaling.md)に関するページをご覧ください。

5. **[確認および作成]** をクリックして、選択内容を確認します。 次の画面のようになります。

   ![新しい IoT ハブを作成するための情報を確認するスクリーンショット](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. **[作成]** をクリックして、新しい IoT ハブを作成します。 ハブの作成には数分かかります。
