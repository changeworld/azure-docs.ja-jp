---
title: Azure IoT Edge デバイスを Azure IoT Central に追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Edge デバイスを Azure IoT Central に追加します
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892638"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>チュートリアル:Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、*Azure IoI Edge デバイス*を Microsoft Azure IoT Central アプリケーションに追加して構成する方法を示します。 このチュートリアルでは、Azure Marketplace から Azure IoT Edge 対応の Linux VM を選択しました。

このチュートリアルは次の 2 つの部分で構成されています。

* まず、オペレーターとして、Azure IoT Edge デバイスのクラウド ファースト プロビジョニングを実行する方法について説明します。
* 次に、Azure IoT Edge デバイスのデバイス ファースト プロビジョニングを実行する方法を学習します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい Azure IoT Edge デバイスを追加する
> * プロビジョニングに役立つように SAS キーを使用して Azure IoT Edge デバイスを構成する
> * IoT Central でダッシュボードとモジュールの正常性を表示する
> * Azure IoT Edge デバイスで実行されているモジュールにコマンドを送信する
> * Azure IoT Edge デバイスで実行されているモジュールのプロパティを設定する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure IoT Central アプリケーションが必要です。 このクイックスタートに従って [Azure IoT Central アプリケーションを作成](./quick-deploy-iot-central.md)します。

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge 登録グループを有効にする
[管理] ページから、Azure IoT Edge 登録グループの SAS キーを有効にします。

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>クラウド ファーストの Azure IoT Edge デバイス プロビジョニング   
このセクションでは、**環境センサー テンプレート**を使用して新しい Azure IoT Edge デバイスを作成し、デバイスをプロビジョニングします。 左側のナビゲーションで [デバイス] をクリックし、[Environment Sensor Template] (環境センサー テンプレート) をクリックします。 

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

**[+ 新規]** をクリックし、適切なデバイス ID と名前を入力します。 

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

デバイスのモードが **[Registered] (登録済み)** になります。

![デバイス テンプレート - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Azure IoT Edge 対応の Linux VM をデプロイする

>注:任意のコンピューターまたはデバイスを使用することを選択できます。 OS: Linux または Windows

このチュートリアルでは、Azure 上に作成できる Azure IoT 対応の Linux VM を選択しました。 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) が表示されるので、 **[今すぐ入手する]** ボタンをクリックします。 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

**[Continue]\(続行\)** をクリックします

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Azure portal に移動されます。 **[作成]** ボタンをクリックします

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

[サブスクリプション] を選択し、できれば新しいリソース グループを作成します。VM の可用性のために米国西部 2 を選択し、ユーザーとパスワードを入力します。 この先の手順で必要になるユーザーとパスワードを覚えておきます。 **[確認と作成]** をクリックします

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

検証されたら **[作成]** をクリックします

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

リソースの作成には数分かかります。 **[リソースに移動]** をクリックします

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Azure IoT Edge デバイスとして VM をプロビジョニングする 

左側のナビゲーションの [サポート + トラブルシューティング] で、[シリアル コンソール] をクリックしします

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

次のような画面が表示されます

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Enter キーを押し、プロンプトに従ってユーザー名とパスワードを入力して、Enter キーを押します。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

管理者/root としてコマンドを実行するには、**sudo su –** コマンドを実行します

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Azure IoT Edge ランタイムのバージョンをチェックします。 最新の GA バージョンは 1.0.8 です

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

vim エディターをインストールか、好みに応じて nano を使用します。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Azure IoT Edge の config.yaml ファイルを構成します

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

下にスクロールし、yaml ファイルの接続文字列の部分をコメント アウトします。 

**変更前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**変更後** (Esc キーを押し、小文字の a を押して、編集を開始します)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

yaml ファイルの対称キーの部分をコメント解除します。 

**変更前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**変更後**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

IoT Central に移動し、Azure IoT Edge デバイスのスコープ ID、デバイス ID、および対称キーを取得します ![デバイス接続](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Linux ボックスにアクセスし、スコープ ID と登録 ID をデバイス ID と対称キーに置き換えます

**Esc** キーを押し、「 **: wq!** 」と入力して、 **Enter** キーを押して変更を保存します

Azure IoT Edge を再起動して変更を処理し、**Enter** キーを押します

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

「**iotedge list**」と入力します。数分経過すると、3 つのモジュールがデプロイされているのを確認できます

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central デバイス エクスプローラー 

IoT Central で、デバイスはプロビジョニング状態に移行します

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

[モジュール] タブに、IoT Central のデバイスとモジュールの状態が表示されます 

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


クラウド プロパティが (前の手順で作成したデバイス テンプレートから) フォームに表示されます。 値を入力し、 **[保存]** をクリックします。 

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

ダッシュボード タイル

![デバイス接続](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

このチュートリアルでは、以下の内容を学習しました。

* 新しい Azure IoT Edge デバイスを追加する
* プロビジョニングに役立つように SAS キーを使用して Azure IoT Edge デバイスを構成する
* IoT Central でダッシュボードとモジュールの正常性を表示する
* Azure IoT Edge デバイスで実行されているモジュールにコマンドを送信する
* Azure IoT Edge デバイスで実行されているモジュールのプロパティを設定する

## <a name="next-steps"></a>次の手順

ここでは、IoT Central で Azure IoT Edge デバイスを管理する方法について学習しました。推奨される次の手順は以下のとおりです。

<!-- Next how-tos in the sequence -->

透過的なゲートウェイを構成する方法。次のチュートリアルに従ってください

> [!div class="nextstepaction"]
> [透過的なゲートウェイを構成する](../../iot-edge/how-to-create-transparent-gateway.md)
