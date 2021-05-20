---
title: VS Code 用の Azure IoT Tools を使用した Azure IoT ハブの作成 | Microsoft Docs
description: Visual Studio Code 用の Azure IoT Tools を使用して、リソース グループに Azure IoT ハブを作成する方法について説明します。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: e8d6bebffc46b0b158aeb975ae1e32a863b956d2
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109655878"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Visual Studio Code 用の Azure IoT Tools を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、[Visual Studio Code 用の Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して Azure IoT ハブを作成する方法について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事を完了するには、以下が必要です。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- [Visual Studio Code](https://code.visualstudio.com/)

- Visual Studio Code 用にインストールされている [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。


## <a name="create-an-iot-hub-and-device-in-an-iot-project"></a>IoT プロジェクトに IoT ハブとデバイスを作成する

次の手順では、Visual Studio Code で IoT プロジェクト内に IoT Hub を作成し、デバイスをハブに登録する方法を示します。

Azure IoT Hub とデバイスを Azure portal からプロビジョニングする代わりに、 開発環境を離れずに、VS Code 内でそれを実行できます。 このセクションの手順では、この実行方法を示します。

1. 新しく開いたプロジェクト ウィンドウで、`F1` をクリックしてコマンド パレットを開き、 **[Azure IoT Device Workbench: Azure サービスのプロビジョニング...]** を入力して選択します。ステップバイステップ ガイドに従って、Azure IoT Hub のプロビジョニングと IoT Hub デバイスの作成を完了します。

    ![プロビジョニング コマンド](media/iot-hub-create-use-iot-toolkit/provision.png)

    > [!NOTE]
    > Azure にサインインしていない場合。 ポップアップ通知に従い、サインインします。

1. 使用するサブスクリプションを選択します。

    ![サブスクリプションの選択](media/iot-hub-create-use-iot-toolkit/select-subscription.png)

1. 次に、既存のリソース グループを選択するか、新しい[リソース グループ](../azure-resource-manager/management/overview.md#terminology)を作成します。

    ![リソース グループの選択](media/iot-hub-create-use-iot-toolkit/select-resource-group.png)

1. 指定したリソース グループで、プロンプトに従って既存の IoT Hub を選択するか、新しい Azure IoT Hub を作成します。

    ![IoT Hub 選択手順](media/iot-hub-create-use-iot-toolkit/iot-hub-provision.png)

    ![IoT Hub の選択](media/iot-hub-create-use-iot-toolkit/select-iot-hub.png)

    ![選択された IoT Hub](media/iot-hub-create-use-iot-toolkit/iot-hub-selected.png)

1. 出力ウィンドウで Azure IoT Hub がプロビジョニングされたことを確認できます。

    ![IoT Hub がプロビジョニングされた](media/iot-hub-create-use-iot-toolkit/iot-hub-provisioned.png)

1. プロビジョニングした Azure IoT Hub で IoT Hub デバイスを選択するか新しく作成します。

    ![IoT デバイスの選択手順](media/iot-hub-create-use-iot-toolkit/iot-device-provision.png)

    ![プロビジョニングされた IoT デバイスを選択する](media/iot-hub-create-use-iot-toolkit/select-iot-device.png)

1. これで Azure IoT Hub がプロビジョニングされ、その中にデバイスが作成されました。 また、デバイス接続文字列が VS Code に保存されます。

    ![プロビジョニング完了](media/iot-hub-create-use-iot-toolkit/provision-done.png)



## <a name="create-an-iot-hub-without-an-iot-project"></a>IoT プロジェクトを使用せずに IoT ハブを作成する

次の手順では、Visual Studio Code で IoT プロジェクトを使用せずに IoT Hub を作成する方法を示します。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。

2. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。 

   ![Azure IoT Hub デバイスを展開する](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをポイントします。 

4. **[Create IoT Hub]\(IoT ハブを作成する\)** を選択します。

5. 右下隅にポップアップが表示され、Azure への初めてのサインインを行うことができます。

6. Azure サブスクリプションを選択します。 

7. リソース グループを選択します。

8. 場所を選択します。

9. 価格レベルを選択します。

10. IoT ハブのグローバルに一意の名前を入力します。

11. IoT ハブが作成されるまで数分待ちます。

## <a name="next-steps"></a>次のステップ

ここでは、Visual Studio Code 用の Azure IoT Tools を使用して IoT ハブをデプロイしました。 次の記事に進んでください。

* [Visual Studio Codes 用の Azure IoT Tools を使用してデバイスと IoT ハブの間のメッセージを送受信する](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

* [Azure IoT Hub デバイスの管理に Visual Studio Code 用の Azure IoT Tools を使用する](iot-hub-device-management-iot-toolkit.md)

* [VS Code 用 Azure IoT Hub の Wiki ページを参照する](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
