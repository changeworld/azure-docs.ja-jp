---
title: Azure IoT Edge デバイスを Azure IoT Central に追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加します
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406345"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

このチュートリアルでは、Azure IoT Edge デバイスをお使いの Azure IoT Central アプリケーションに追加して構成する方法について説明します。 このチュートリアルでは、Azure Marketplace から IoT Edge 対応の Linux VM を選択しました。

このチュートリアルは次の 2 つの部分で構成されています。

* まず、オペレーターとして、IoT Edge デバイスのクラウド ファースト プロビジョニングを実行する方法について説明します。
* 次に、IoT Edge デバイスの "デバイス ファースト" プロビジョニングを実行する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい IoT Edge デバイスを追加する
> * Shared Access Signature (SAS) キーを使用してプロビジョニングに役立つよう IoT Edge デバイスを構成する
> * IoT Central でダッシュボードとモジュールの正常性を表示する
> * IoT Edge デバイスで実行されているモジュールにコマンドを送信する
> * IoT Edge デバイスで実行されているモジュールのプロパティを設定する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure IoT Central アプリケーションが必要です。 [こちらのクイックスタートに従って、Azure IoT Central アプリケーションを作成](./quick-deploy-iot-central.md)します。

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge 登録グループを有効にする
**[管理]** ページから、Azure IoT Edge 登録グループの SAS キーを有効にします。

![デバイス接続が強調表示されている [管理] ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>"クラウド ファースト" の Azure IoT Edge デバイスをプロビジョニングする  
このセクションでは、環境センサー テンプレートを使用して新しい IoT Edge デバイスを作成し、デバイスをプロビジョニングします。 **[デバイス]**  >  **[Environment Sensor Template]\(環境センサー テンプレート\)** の順に選択します。 

![[Environment Sensor Template]\(環境センサー テンプレート\) が強調表示されている [デバイス] ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

**[+ 新規]** を選択し、任意のデバイス ID と名前を入力します。 **作成** を選択します。

![[デバイス ID] と [作成] が強調表示されている [新しいデバイスの作成] ダイアログ ボックスのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

デバイスのモードが **[登録済み]** になります。

![[Device status]\(デバイスの状態\) が強調表示されている [Environment Sensor Template]\(環境センサー テンプレート\) ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>IoT Edge 対応の Linux VM をデプロイする

> [!NOTE]
> 任意のコンピューターまたはデバイスを選択して使用できます。 オペレーティング システムは Linux または Windows になります。

このチュートリアルでは、Azure 上に作成された Azure IoT 対応の Linux VM を使用しています。 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) で、 **[今すぐ入手する]** を選択します。 

![[今すぐ入手する] が強調表示されている Azure Marketplace のスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

**[続行]** をクリックします。

![[続行] が強調表示されている [Azure でこのアプリを作成する] ダイアログ ボックスのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Azure portal が表示されます。 **作成** を選択します。

![[作成] が強調表示されている Azure portal のスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

**[サブスクリプション]** を選択し、新しいリソース グループを作成します。VM の可用性に対して **[(米国) 米国西部 2]** を選択します。 次に、ユーザーとパスワードの情報を入力します。 これらはこの先の手順で必要になるため、覚えておいてください。 **[Review + create]\(レビュー + 作成\)** を選択します。

![さまざまなオプションが強調表示されている [Create a virtual machine]\(仮想マシンの作成\) 詳細ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

検証後、 **[作成]** を選択します。

![[検証に成功しました] と [作成] が強調表示されている [Create a virtual machine]\(仮想マシンの作成\) ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

リソースの作成には数分かかります。 **[リソースに移動]** を選択します。

![[リソースに移動] が強調表示されている、デプロイの完了ページのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>IoT Edge デバイスとして VM をプロビジョニングする 

**[サポート + トラブルシューティング]** で、 **[シリアル コンソール]** を選択します。

![[シリアル コンソール] が強調表示されている [サポート + トラブルシューティング] のオプションのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

次のような画面が表示されます。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Enter キーを押します。プロンプトに従ってユーザー名とパスワードを入力し、もう一度 Enter キーを押します。 

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

管理者 (ユーザー "root") としてコマンドを実行するには、「**sudo su -** 」と入力します。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

IoT Edge ランタイムのバージョンを確認します。 この記事の執筆時点では、最新の GA バージョンは 1.0.8 です。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

vim エディターをインストールするか、必要に応じて nano を使用します。 

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

IoT Edge の config.yaml ファイルを編集します。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

下へスクロールし、yaml ファイルの接続文字列部分をコメントアウトします。 

**変更前**

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**変更後** (Esc キーを押し、小文字の a キーを押すと、編集が開始されます。)

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

yaml ファイルの対称キーの部分をコメント解除します。 

**変更前**

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**変更後**

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

IoT Central に移動します。 IoT Edge デバイスのスコープ ID、デバイス ID、および対称キーを取得します。
![各種デバイス接続オプションが強調表示されている IoT Central のスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Linux コンピューターにアクセスし、スコープ ID と登録 ID を、デバイス ID と対称キーに置き換えます。

Esc キーを押し、「 **:wq!** 」と入力します。 Enter キーを押して変更を保存します。

変更を処理するために IoT Edge を再起動し、Enter キーを押します。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

「**iotedge list**」と入力します。 数分後、3 つのモジュールがデプロイされていることがわかります。

![コンソールのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central デバイス エクスプローラー 

IoT Central で、デバイスがプロビジョニング済み状態に移行します。

![[Device status]\(デバイスの状態\) が強調表示されている、IoT Central デバイスのオプションのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

**[モジュール]** タブには、IoT Central 上のデバイスとモジュールの状態が表示されます。 

![IoT Central の [モジュール] タブのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


前の手順で作成したデバイス テンプレートから、クラウド プロパティがフォームに表示されます。 値を入力し、 **[保存]** を選択します。 

![[My Linux Edge Device]\(自分の Linux Edge デバイス\) フォームのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

ダッシュボード タイルの形式で表示されるビューを次に示します。

![[My Linux Edge Device]\(自分の Linux Edge デバイス\) ダッシュボード タイルのスクリーンショット](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>次の手順

ここでは、IoT Central で IoT Edge デバイスを操作および管理する方法について学習しました。推奨される次の手順は以下のとおりです。

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [透過的なゲートウェイを構成する](../../iot-edge/how-to-create-transparent-gateway.md)
