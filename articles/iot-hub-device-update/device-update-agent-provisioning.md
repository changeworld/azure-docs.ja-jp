---
title: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のエージェントのプロビジョニング
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ecfbd8ac385a3449ce2934ec93b10c255d621fe1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259077"
---
# <a name="device-update-agent-provisioning"></a>デバイス更新エージェントのプロビジョニング

デバイス更新モジュール エージェントは、他のシステム プロセス、および同じ論理デバイスの一部として IoT Hub に接続されている [IoT Edge モジュール](../iot-edge/iot-edge-modules.md)と平行して実行できます。 このセクションでは、デバイス更新エージェントをモジュール ID としてプロビジョニングする方法について説明します。 


## <a name="module-identity-vs-device-identity"></a>モジュール ID とデバイス ID

IoT Hub では、デバイス ID ごとに最大 50 のモジュール ID を作成できます。 モジュールの ID ごとに、モジュール ツインは暗黙的に生成されます。 デバイス側では、IoT Hub デバイス SDK を使用して、それぞれが IoT Hub への独立した接続を確立するモジュールを作成できます。 モジュール ID とモジュール ツインは、デバイス ID とデバイス ツインと同様の機能を、より細かい粒度で提供します。 [IoT Hub でのモジュール ID の詳細を参照してください](../iot-hub/iot-hub-devguide-module-twins.md)

デバイス レベルのエージェントから、デバイスのモジュール ID としてのエージェントの追加に移行する場合は、デバイス ツインを介して通信していた古いエージェントを削除します。 デバイス更新エージェントをモジュール ID としてプロビジョニングすると、デバイスとデバイス更新サービス間のすべての通信がモジュール ツインを介して行われます。そのため、[グループ](device-update-groups.md)を作成するときに、デバイスのモジュール ツインにタグ付けすることを忘れないでください。すべての[通信](device-update-plug-and-play.md)は、モジュール ツインを介して行われる必要があります。

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

[パッケージ ベースの更新](./understand-device-update.md#support-for-a-wide-range-of-update-artifacts)用に IoT デバイスまたは IoT Edge デバイスを設定する場合は、次の手順に従って packages.microsoft.com をコンピューターのリポジトリに追加します。

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

このセクションでは、次のデバイスで、デバイス更新エージェントをモジュール ID としてプロビジョニングする方法について説明します。 
* IoT Edge 対応デバイス、または 
* Edge IoT 以外のデバイス、または
* その他の IoT デバイス 
 
管理している IoT デバイスの種類に基づいてデバイス更新エージェントを追加するには、以下のセクションのすべてまたはいずれかに従います。 

### <a name="on-iot-edge-enabled-devices"></a>IoT Edge 対応デバイスの場合

[IoT Edge 対応デバイス](../iot-edge/index.yml)でデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1. 指示に従って、[1 つの Linux IoT Edge デバイスを手動でプロビジョニング](../iot-edge/how-to-provision-single-device-linux-symmetric.md?preserve-view=true&view=iotedge-2020-11)します。

1. デバイス更新のイメージの更新エージェントをインストールします。

    [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) リポジトリにサンプル イメージを用意しています。 swUpdate ファイルは、Raspberry Pi B3+ ボードにフラッシュできる基本イメージです。 .gz ファイルは、Device Update for IoT Hub を使用してインポートする更新プログラムです。 例については、[イメージを IoT Hub デバイスにフラッシュする方法](./device-update-raspberry-pi.md#flash-sd-card-with-image)に関するページを参照してください。  

1. デバイス更新のパッケージの更新エージェントをインストールします。

    - packages.miscrosoft.com の最新のエージェントのバージョンの場合: 次のコマンドを使用して、デバイス上のパッケージ リストを更新し、デバイス更新エージェント パッケージとその依存関係をインストールします。   

        ```shell
        sudo apt-get update
        ```
    
        ```shell
        sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
        ```
    
    - [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) の "RC" (リリース候補) のエージェントのバージョンの場合: デバイス更新エージェントをインストールするマシンに .dep ファイルをダウンロードし、次のコマンドを実行します。
   
        ```shell
        sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
        ```
    
1. これで、IoT Edge デバイスでデバイス更新エージェントを開始する準備ができました。 

### <a name="on-non-edge-iot-linux-devices"></a>Edge IoT 以外の Linux デバイスの場合

IoT Linux デバイスでデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1. IoT ID サービスをインストールし、最新バージョンを IoT デバイスに追加します。 
    1. コンピューターまたは IoT デバイスにログオンします。
    1. ターミナル ウィンドウを開きます。
    1.  次のコマンドを使用して、最新の [IoT ID サービス](https://github.com/Azure/iot-identity-service/blob/main/docs-dev/packaging.md#installing-and-configuring-the-package) を IoT デバイスにインストールします。
    > [!Note]
    > 現在 IoT ID サービスでは、対称キーを使用してモジュール ID が IoT Hub に登録されます。

    ```shell
    sudo apt-get install aziot-identity-service
    ```
        
1. IoT ID サービスをプロビジョニングして IoT デバイス情報を取得します。

    構成テンプレートのカスタム コピーを作成して、プロビジョニング情報を追加できるようにします。 ターミナルで、次のコマンドを入力します。
      
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
    
1. この時点で、次のコマンドを使用して IoT ID サービスを適用し、再起動します。 これで、"Done!" と出力されます。これは、IoT ID サービスが正しく構成されたことを示しています。

    > [!Note]
    > 現在 IoT ID サービスでは、対称キーを使用してモジュール ID が IoT Hub に登録されます。
    
    ```shell
    sudo aziotctl config apply
    ```
    
1. 最後に、デバイス更新エージェントをインストールします。 [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) にサンプル画像を提供しており、swUpdate ファイルは、Raspberry Pi B3+ ボードにフラッシュできる基本イメージであり、.gz ファイルは、Device Update for IoT Hub を使用してインポートする更新プログラムです。 [IoT Hub デバイスにイメージをフラッシュする方法](./device-update-raspberry-pi.md#flash-sd-card-with-image)の例を参照してください。

1. これで、IoT デバイスでデバイス更新エージェントを開始する準備ができました。 

### <a name="other-iot-devices"></a>その他の IoT デバイス

テスト用または制約のあるデバイスの場合、デバイス更新エージェントを IoT ID サービスを使用せずに構成することもできます。 (モジュールまたはデバイスから) 接続文字列を使用してデバイス更新エージェントをプロビジョニングするには、次の手順に従います。

1. [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) リポジトリにサンプル イメージを用意しています。 swUpdate ファイルは、Raspberry Pi B3+ ボードにフラッシュできる基本イメージです。 .gz ファイルは、Device Update for IoT Hub を使用してインポートする更新プログラムです。 例については、[イメージを IoT Hub デバイスにフラッシュする方法](./device-update-raspberry-pi.md#flash-sd-card-with-image)に関するページを参照してください。

1. コンピューターまたは IoT Edge デバイス/IoT デバイスにログオンします。
    
1. ターミナル ウィンドウを開きます。

1. [デバイス更新構成ファイル](device-update-configuration-file.md)に接続文字列を追加します。

    1. ターミナル ウィンドウに以下を入力します。
   
        - [パッケージの更新](device-update-ubuntu-agent.md)では、sudo nano/etc/adu/adu-conf.txt を使用
        - [イメージの更新](device-update-raspberry-pi.md)では、sudo nano /adu/adu-conf.txt を使用
       
    1. テキストを含むウィンドウが開きます。 IoT デバイスでデバイス更新エージェントを初めてプロビジョニングするときに、'connection_String=' の後の文字列全体を削除します。 これは単なるプレース ホルダーのテキストです。
    
    1. ターミナルで、\<your-connection-string\> を、ご自身のデバイス更新エージェント インスタンスのデバイスの接続文字列に置き換えます。 Enter キーを押し、 **[保存]** を選択します。 こちらの例のようになります。
    
        ```text
        connection_string=<ADD CONNECTION STRING HERE>
        ```   
        
    > [!Important]
    > 接続文字列の前後に引用符を追加しないでください。
    
1. これで、IoT デバイスでデバイス更新エージェントを開始する準備ができました。 


## <a name="how-to-start-the-device-update-agent"></a>デバイス更新エージェントを開始する方法

このセクションでは、IoT デバイスで正常に実行されているモジュール ID としてデバイス更新エージェントを開始および検証する方法について説明します。

1. デバイス更新エージェントがインストールされているコンピューターまたはデバイスにログインします。

1. ターミナル ウィンドウを開き、次のコマンドを入力します。

    ```shell
    sudo systemctl restart adu-agent
    ```
    
1. 次のコマンドを使用して、エージェントの状態を確認できます。 問題が発生した場合は、こちらの[トラブルシューティング ガイド](troubleshoot-device-update.md)を参照してください。
    
    ```shell
    sudo systemctl status adu-agent
    ```
    
    状態 OK が表示されます。

1. IoT Hub ポータルで、IoT デバイスまたは IoT Edge デバイスに移動し、デバイス更新エージェントを使用して構成したデバイスを見つけます。 そこで、デバイス更新エージェントがモジュールとして実行されていることが確認されます。 次に例を示します。

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
