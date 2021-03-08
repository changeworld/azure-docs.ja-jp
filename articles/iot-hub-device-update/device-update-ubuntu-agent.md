---
title: Ubuntu Server 18.04 x64 Package agent を使用した Device Update for Azure IoT Hub のチュートリアル | Microsoft Docs
description: Ubuntu Server 18.04 x64 Package agent を使用して、Device Update for Azure IoT Hub を開始します。
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659055"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Ubuntu Server 18.04 x64 Package agent を使用した Device Update for Azure IoT Hub のチュートリアル

Device Update for IoT Hub では、イメージベースとパッケージベースの 2 つの形式の更新がサポートされています。

パッケージベースの更新は、デバイス上の特定のコンポーネントまたはアプリケーションのみを変更するターゲット指定の更新となります。 これにより、帯域幅の消費量が少なくなるほか、更新のダウンロードとインストールにかかる時間が短縮されます。 通常、パッケージ更新プログラムを使用すると、更新プログラムを適用する際のデバイスのダウンタイムを短縮し、イメージ作成のオーバーヘッドを回避できます。

このチュートリアルでは、Device Update for IoT Hub を通じて、エンド ツー エンドのパッケージベースの更新を行う手順について説明します。 このチュートリアルでは、Ubuntu Server 18.04 x64 用のサンプル パッケージ エージェントを使用します。 別の OS プラットフォーム構成の使用を予定している場合でも、このチュートリアルは Device Update for IoT Hub のツールと概念の学習に役立ちます。 このエンド ツー エンドの更新プロセスの概要を完了したら、使用する更新の形式や OS プラットフォームを選択して、詳細に進みます。 このチュートリアルを使用すれば、Device Update for IoT Hub で Azure IoT または Azure IoT Edge のデバイスを更新できます。 

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * デバイス更新プログラム パッケージ リポジトリを構成する
> * デバイス更新エージェントとその依存関係をダウンロードしてインストールする
> * IoT デバイスにタグを追加する
> * 更新プログラムをインポートする
> * デバイス グループを作成する
> * パッケージの更新をデプロイする
> * 更新プログラムのデプロイを監視する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
* IoT Hub へのアクセス。 S1 (Standard) レベル以上を使用することをお勧めします。
* Ubuntu Server 18.04 x64 を実行し、IoT Hub に接続されている Azure IoT または Azure IoT Edge デバイス。
   * Azure IoT Edge デバイスを使用している場合は、v1.2.0 以降の Edge ランタイムが実行されていることを確認してください 
* Azure IoT Edge デバイスを使用していない場合は、[IoT デバイスに最新の `aziot-identity-service` パッケージ (プレビュー) をインストールします ](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [デバイス更新アカウントと、上記と同じ IoT Hub にリンクされているインスタンス。](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>デバイス更新プログラム パッケージ リポジトリを構成する

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。 このチュートリアルでは、Ubuntu Server 18.04 になります。 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Microsoft GPG 公開キーをインストールします。

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Device Update .deb エージェント パッケージをインストールします

1. デバイスのパッケージ リストを更新します

   ```shell
      sudo apt-get update
   ```

2. deviceupdate-agent パッケージとその依存関係をインストールします

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Device Update for Azure IoT Hub ソフトウェア パッケージには、次のライセンス条項が適用されます。
   * [Device Update for IoT Hub ライセンス](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [配信の最適化のクライアント ライセンス](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、そのパッケージを使用しないでください。

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Azure IoT ID サービスを使用して Device Update エージェントを構成する (プレビュー)

必要なパッケージをインストールしたら、クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

1. 構成ファイルを開きます

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. ファイルのプロビジョニング構成セクションを見つけます。 「接続文字列を使用した手動プロビジョニング」セクションをコメント解除します。 connection_string の値をご自分の IoT (Edge) デバイスの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Ctrl + X、Y を使用して、ファイルを保存して閉じます

4. 構成を適用します。 

   IoT Edge デバイスを使用している場合は、次のコマンドを使用します。 
   
   ```shell
      sudo iotedge config apply
   ```
   
   `aziot-identity-service` パッケージがインストールされている IoT デバイスを使用している場合は、次のコマンドを使用します。 
      
   ```shell
      sudo aziotctl config apply
   ```

5. 必要に応じて、サービスが実行されていることを確認できます

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    出力は次のようになります。

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>デバイスにタグを追加する

1. [Azure portal](https://portal.azure.com) にログインし、IoT Hub に移動します。

2. 左側のナビゲーション ウィンドウの [IoT デバイス] または [IoT Edge] から、対象の IoT デバイスを見つけて、[デバイス ツイン] に移動します。

3. デバイス ツインで、Device Update の既存のタグ値を null に設定することで削除します。

4. 次に示すように、Device Update の新しいタグ値を追加します。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>更新プログラムをインポートする

1. 次の [apt マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json)と[インポート マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json)をダウンロードします。 この apt マニフェストにより、使用可能な最新バージョンの `libcurl4-doc package` が IoT デバイスにインストールされます。 

   または、この [apt マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json)と[インポート マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json)をダウンロードすることもできます。 これにより、特定バージョン (v7.58.0) の `libcurl4-doc package` が IoT デバイスにインストールされます。 

2. Azure portal で、IoT Hub の左側のナビゲーション バーから [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

3. [更新プログラム] タブを選択します。

4. [+ 新しい更新プログラムのインポート] を選択します。

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 以前にダウンロードしたインポート マニフェストを選択します。 次に、[1 つまたは複数の更新ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 以前にダウンロードした apt マニフェスト更新ファイルを選択します。
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="更新ファイルの選択を示すスクリーンショット。" lightbox="media/import-update/select-update-files.png":::

6. [ストレージ コンテナーの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 次に、適切なストレージ アカウントを選択します。

7. コンテナーを既に作成してある場合は、再利用できます (そうでない場合は、[+ コンテナー] を選択して、更新用の新しいストレージ コンテナーを作成します)。  使用するコンテナーを選択し、[選択] をクリックします。

   :::image type="content" source="media/import-update/container.png" alt-text="コンテナーの選択を示すスクリーンショット。" lightbox="media/import-update/container.png":::

8. [送信] を選択してインポート プロセスを開始します。

9. インポート プロセスが開始され、画面が [インポートの履歴] セクションに変わります。 [更新] を選択すると、インポート プロセスが完了するまで進行状況が表示されます。 更新プログラムのサイズに応じて、これは数分で完了する場合も、それよりも長くかかる場合もあります。
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="更新プログラムのインポート シーケンスを示すスクリーンショット。" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. [状態] 列にインポートが正常に完了したと表示されたら、[デプロイの準備完了] ヘッダーを選択します。 インポートされた更新プログラムが一覧に表示されるはずです。

更新プログラムのインポートについて詳しくは、[こちら](import-update.md)をご覧ください。

## <a name="create-update-group"></a>更新プログラム グループを作成する

1. 以前に Device Update インスタンスに接続した IoT Hub にアクセスします。

2. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

3. ページの上部にある [グループ] タブを選択します。 

4. [追加] ボタンを選択して、新しいグループを作成します。

5. 前の手順で作成した IoT Hub のタグを一覧から選択します。 [更新プログラム グループの作成] を選択します。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="タグの選択を示すスクリーンショット。" lightbox="media/create-update-group/select-tag.PNG":::

タグの追加と更新プログラム グループの作成に関する[詳細情報](create-update-group.md)


## <a name="deploy-update"></a>更新プログラムをデプロイする

1. グループが作成されると、デバイス グループで利用可能な新しい更新プログラムが表示され、[保留中の更新プログラム] の下にその更新プログラムへのリンクが表示されます。 場合によっては、1 回更新する必要があります。 

2. 利用可能な更新プログラムをクリックします。

3. 対象のグループとして、適切なグループが選択されていることを確認します。 展開をスケジュールし、[Deploy update]\(更新プログラムの展開\) を選択します。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="更新プログラムを選択する" lightbox="media/deploy-update/select-update.png":::

4. コンプライアンス チャートを表示します。 更新が進行中であると表示されます。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="更新が進行中です" lightbox="media/deploy-update/update-in-progress.png":::

5. デバイスが正常に更新されると、コンプライアンス チャートと展開の詳細が、同じ内容を反映するように更新されていることがわかります。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新に成功しました" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>更新プログラムの展開を監視する

1. ページの上部にある [展開] タブを選択します。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="[展開] タブ" lightbox="media/deploy-update/deployments-tab.png":::

2. 作成した展開を選択して、展開の詳細を表示します。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

3. [更新] を選択し、最新の状態の詳細を表示します。 状態が [成功] に変わるまで、このプロセスを続行します。

これで、Ubuntu Server 18.04 x64 デバイス上で Device Update for IoT Hub を使用して、エンド ツー エンドのパッケージ更新が正常に完了しました。 

## <a name="bonus-steps"></a>ボーナス ステップ

1. 次の [apt マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json)と[インポート マニフェスト ファイル](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json)をダウンロードします。 この apt マニフェストによって、インストールされている `libcurl4-doc package` が IoT デバイスから削除されます。 

2. 「更新プログラムをインポートする」セクションと「更新プログラムをデプロイする」セクションを繰り返します

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、デバイス更新アカウント、インスタンス、IoT Hub、IoT デバイスをクリーンアップします。 これを行うには、個々のリソースに移動し、[削除] を選択します。 デバイス更新アカウントをクリーンアップする前に、デバイス更新インスタンスをクリーンアップする必要があることに注意してください。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+ でのイメージの更新のチュートリアル](device-update-raspberry-pi.md)

