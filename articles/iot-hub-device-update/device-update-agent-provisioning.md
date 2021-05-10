---
title: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120184"
---
# <a name="device-update-agent-provisioning"></a>デバイス更新エージェントのプロビジョニング

デバイス更新モジュール エージェントは、他のシステム プロセス、および同じ論理デバイスの一部として IoT Hub に接続されている [IoT Edge モジュール](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)と平行して実行できます。 このセクションでは、デバイス更新エージェントをモジュール ID としてプロビジョニングする方法について説明します。 


## <a name="module-identity-vs-device-identity"></a>モジュール ID とデバイス ID

IoT Hub では、デバイス ID ごとに最大 50 のモジュール ID を作成できます。 モジュールの ID ごとに、モジュール ツインは暗黙的に生成されます。 デバイス側では、IoT Hub デバイス SDK を使用して、それぞれが IoT Hub への独立した接続を確立するモジュールを作成できます。 モジュール ID とモジュール ツインは、デバイス ID とデバイス ツインと同様の機能を、より細かい粒度で提供します。 [IoT Hub でのモジュール ID の詳細を参照してください](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>デバイス更新のサポート

現在、デバイス更新では、次の種類の IoT デバイスがサポートされています。

* Linux デバイス (IoT Edge および非 IoT Edge のデバイス):
    * イメージ A/B の更新:
        - Yocto - ARM64 (参照イメージ)。必要に応じて、他のアーキテクチャ用の[独自のイメージをビルド](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent)するために、オープン ソースを通じて拡張できます。
        - Ubuntu 18.04 シミュレーター
       
    * 次のプラットフォームまたはアーキテクチャ用のパッケージ エージェントでサポートされるビルド:
        - Ubuntu Server 18.04 x64 パッケージ エージェント 
        - Debian 9 
        
* 制約のあるデバイス:
    * Azure RTOS デバイス更新エージェントのサンプル: [Azure リアルタイム オペレーティング システムでの Device Update for Azure IoT Hub のチュートリアル](device-update-azure-real-time-operating-system.md)

* 切断されたデバイス: 
    * [切断されたデバイスの更新のサポートについて](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>前提条件  

[パッケージ ベースの更新](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)用に IoT デバイスまたは IoT Edge デバイスを設定する場合は、次の手順に従って packages.microsoft.com をコンピューターのリポジトリに追加します。

1. デバイス更新エージェントをインストールするコンピューターまたは IoT デバイスにログオンします。

1. ターミナル ウィンドウを開きます。

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. 生成されたリストを sources.list.d ディレクトリにコピーします。
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Microsoft GPG 公開キーをインストールします。
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>デバイス更新エージェントをモジュール ID としてプロビジョニングする方法

このセクションでは、IoT Edge 対応デバイス、Edge IoT 以外のデバイス、その他の IoT デバイスでデバイス更新エージェントをモジュール ID としてプロビジョニングする方法について説明します。


### <a name="on-iot-edge-enabled-devices"></a>IoT Edge 対応デバイスの場合

[IoT Edge 対応デバイス](https://docs.microsoft.com/azure/iot-edge)でデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1. 手順に従って [Azure IoT Edge ランタイムをインストールしてプロビジョニング](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)します。

1. 次に、[成果物](https://github.com/Azure/iot-hub-device-update/releases)からデバイス更新エージェントをインストールします。これで、IoT Edge デバイスでデバイス更新エージェントを開始する準備が整いました。


### <a name="on-non-edge-iot-linux-devices"></a>Edge IoT 以外の Linux デバイスの場合

IoT Linux デバイスでデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1. IoT ID サービスをインストールし、最新バージョンを IoT デバイスに追加します。 
    1. コンピューターまたは IoT デバイスにログオンします。
    1. ターミナル ウィンドウを開きます。
    1.  次のコマンドを使用して、最新の [IoT ID サービス](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) を IoT デバイスにインストールします。
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. IoT ID サービスをプロビジョニングして IoT デバイス情報を取得します。
    * 構成テンプレートのカスタム コピーを作成して、プロビジョニング情報を追加できるようにします。 ターミナルに次のコマンドを入力します。
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. 次に、構成ファイルを編集して、このデバイスまたはコンピューターのプロビジョナーとして機能させるデバイスの接続文字列を含めます。 ターミナルに次のコマンドを入力します。

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. 次の例のようなメッセージが表示されます。

    :::image type="content" source="media/understand-device-update/config.png" alt-text="IoT ID サービスの構成ファイルの図。" lightbox="media/understand-device-update/config.png":::

    1. 同じ nano ウィンドウで、"Manual provisioning with connection string" を含むブロックを見つけます。
    1. このウィンドウで、'provisioning' の前の "#" 記号を削除します
    1. このウィンドウで、'source' の前の "#" 記号を削除します 
    1. このウィンドウで、'connection_string' の前にある "#" 記号を削除します
    1. このウィンドウで、' connection_string ' の右側にある引用符で囲まれた文字列を削除し、そこにご自身の接続文字列を追加します 
    1. 'Ctrl+X'、次に 'Y' を使用してファイルへの変更を保存し、'enter' キーを押して変更内容を保存します。 
    
1.  この時点で、次のコマンドを使用して IoT ID サービスを適用し、再起動します。 これで、"Done!" と出力されます。これは、IoT ID サービスが正しく構成されたことを示しています。

    > [!Note]
    > 現在 IoT ID サービスでは、対称キーを使用してモジュール ID が IoT Hub に登録されます。
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  最後に、[成果物](https://github.com/Azure/iot-hub-device-update/releases)からデバイス更新エージェントをインストールします。これで、IoT Edge デバイスでデバイス更新エージェントを開始する準備が整いました。


### <a name="other-iot-devices"></a>その他の IoT デバイス

テスト用または制約のあるデバイスの場合、デバイス更新エージェントを IoT ID サービスを使用せずに構成することもできます。 (モジュールまたはデバイスから) 接続文字列を使用してデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1.  [成果物](https://github.com/Azure/iot-hub-device-update/releases)からデバイス更新エージェントをインストールします。

1.  コンピューターまたは IoT Edge デバイス/IoT デバイスにログオンします。
    
1.  ターミナル ウィンドウを開きます。

1.  [デバイス更新構成ファイル](device-update-configuration-file.md)に接続文字列を追加します。
    1. ターミナル ウィンドウに以下を入力します。
        - [パッケージの更新](device-update-ubuntu-agent.md)では、sudo nano/etc/adu/adu-conf.txt を使用
        - [イメージの更新](device-update-raspberry-pi.md)では、sudo nano /adu/adu-conf.txt を使用
       
    1. テキストを含むウィンドウが開きます。 IoT デバイスでデバイス更新エージェントを初めてプロビジョニングするときに、'connection_String=' の後の文字列全体を削除します。 これは単なるプレース ホルダーのテキストです。
    
    1. ターミナルで、<your-connection-string> を、ご自身のデバイス更新エージェント インスタンスのデバイスの接続文字列に置き換えます。
    
        > [!Important]
        > 接続文字列の前後に引用符を追加しないでください。
        
        - connection_string=<your-connection-string>
       
    1. Enter キーを押して保存します。
    
1.  これで、IoT Edge デバイスでデバイス更新エージェントを開始する準備ができました。 


## <a name="how-to-start-the-device-update-agent"></a>デバイス更新エージェントを開始する方法

このセクションでは、IoT デバイスで正常に実行されているモジュール ID としてデバイス更新エージェントを開始および検証する方法について説明します。

1.  デバイス更新エージェントがインストールされているコンピューターまたはデバイスにログインします。

1.  ターミナル ウィンドウを開き、次のコマンドを入力します。
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  次のコマンドを使用して、エージェントの状態を確認できます。 問題が発生した場合は、こちらの[トラブルシューティング ガイド](troubleshoot-device-update.md)を参照してください。
    ```shell
    sudo systemctl status adu-agent
    ```
    
    状態 OK が表示されます。

1.  IoT Hub ポータルで、IoT デバイスまたは IoT Edge デバイスに移動し、デバイス更新エージェントを使用して構成したデバイスを見つけます。 そこで、デバイス更新エージェントがモジュールとして実行されていることが確認されます。 次に例を示します。

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="デバイス更新モジュールの名前の図。" lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>デバイス更新エージェントをビルドして実行する方法

独自のカスタマー デバイス更新エージェントをビルドして変更することもできます。

手順に従って、ソースからデバイス更新エージェントを[ビルド](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)します。

エージェントが正常にビルドされたら、エージェントを[実行](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)します。

次に、エージェントをイメージに組み込むために必要な変更を行います。  ガイダンスについては、デバイス更新エージェントを[変更](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md)する方法をご覧ください。


## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

問題が発生した場合は、IoT Hub 用デバイス更新の[トラブルシューティング ガイド](troubleshoot-device-update.md)をご確認ください。考えられる問題を取り除き、Microsoft に提供する必要がある情報を収集するのに役に立ちます。


## <a name="next-steps"></a>次のステップ

Device Update for IoT Hub の簡単なデモンストレーションとして、次の事前ビルドのイメージとバイナリを使用できます。

- [イメージの更新: Raspberry Pi 3 B+ 参照 Yocto イメージの使用を開始する](device-update-raspberry-pi.md)ことに関する記事。必要に応じて他のアーキテクチャ用の独自のイメージをビルドするために、オープン ソースを通じて拡張できます。

- [Ubuntu (18.04 x64) シミュレーター参照エージェントの使用を開始する](device-update-simulator.md)

- [パッケージ更新: Ubuntu Server 18.04 x64 パッケージ エージェントの使用を開始する](device-update-ubuntu-agent.md)

- [Azure リアルタイム オペレーティング システムでの Device Update for Azure IoT Hub のチュートリアル](device-update-azure-real-time-operating-system.md)

