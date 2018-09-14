---
title: IoT によるカスタムのシミュレートされたデバイスの展開 - Azure | Microsoft Docs
description: このハウツー ガイドでは、カスタムのシミュレートされたデバイスをデバイス シミュレーション ソリューション アクセラレータに展開する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346789"
---
# <a name="deploy-a-new-simulated-device"></a>新しいシミュレートされたデバイスをデプロイする

リモート監視およびデバイス シミュレーション ソリューション アクセラレータは、いずれも独自のシミュレートされたデバイスを定義できます。 この記事では、カスタマイズされた chiller デバイスの種類と新しい lightbulb デバイスの種類をデバイス シミュレーション ソリューション アクセラレータに展開する方法について説明します。

この記事の手順では、「[Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md)」(新しいシミュレートされたデバイスの作成とテスト) ハウツー ガイドを完了し、カスタマイズされた chiller デバイスの種類と新しい lightbulb デバイスの種類を定義するファイルがあることを前提としています。

このハウツー ガイドの手順では、以下の方法を説明します。

1. SSH を使用して、デバイス シミュレーション ソリューション アクセラレータをホストしている仮想マシンのファイル システムにアクセスします。

1. Docker コンテナーの外部からデバイス モデルを読み込むように Docker を構成します。

1. カスタム デバイス モデル ファイルを使用してシミュレーションを実行します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドに従うには、次のものが必要です。

- [デバイス シミュレーション ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS)のデプロイ済みインスタンス。
- `ssh` および `scp` コマンドを実行するローカルの **bash** シェル。 Windows の場合、[git](https://git-scm.com/download/win) をインストールするには、**bash** をインストールする方法が簡単です。
- カスタム デバイス モデル ファイル (「[Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md)」(新しいシミュレーションされたデバイスの作成とテスト) で説明されているファイルなど)。

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker を構成する

このセクションでは、Docker コンテナー内からではなく、仮想マシンの **/tmp/devicemodels** フォルダーからデバイス モデル ファイルを読み込むように Docker を構成します。 このセクションのコマンドは、ローカル マシンの **bash** シェルで実行してください。

1. SSH を使用して、ローカル マシンから Azure の仮想マシンに接続します。 次のコマンドでは、仮想マシン **vm-vikxv** のパブリック IP アドレスが **104.41.128.108** であることを前提としています。この値を前のセクションの仮想マシンのパブリック IP アドレスに置き換えます。

   ```sh
    ssh azureuser@104.41.128.108
    ```

    プロンプトに従い、前のセクションで設定したパスワードを使用して仮想マシンにサインインします。

1. コンテナーの外部からデバイス モデルを読み込むようにデバイス シミュレーション サービスを構成します。 まず Docker 構成ファイルを開きます。

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    **devicesimulation** コンテナーの設定を探し、次のスニペットのように **volumes** の設定を編集します。

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    変更を保存します。

1. JSON およびスクリプト ファイルを保存するフォルダーを作成します。

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    **[bash]** ウィンドウで SSH セッションを開いたままにしておきます。

1. カスタム デバイス モデル ファイルを仮想マシンにコピーします。 このコマンドは、カスタム デバイス モデルを作成したマシン上の別の **bash** シェルで実行します。 まず、デバイス モデルの JSON ファイルが保存されているローカル フォルダーに移動します。 次のコマンドでは、仮想マシンのパブリック IP アドレスが **104.41.128.108** であることを前提としています。この値を実際の仮想マシンのパブリック IP アドレスに置き換えます。 プロンプトが表示されたら、仮想マシンのパスワードを入力します。

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. 新しいデバイス モデルを使用するためにデバイス シミュレーション Docker コンテナーを再起動します。 仮想マシンへの SSH セッションを開いた状態で、**bash** シェルで次のコマンドを実行します。

    ```sh
    sudo /app/start.sh
    ```

    実行中の Docker コンテナーの状態とそのコンテナー ID を確認するには、次のコマンドを使用します。

    ```sh
    docker ps
    ```

    デバイス シミュレーション コンテナーからログを表示するには、次のコマンドを実行します。 コンテナー ID を実際のデバイス シミュレーション コンテナーの ID に置き換えます。

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>シミュレーションを実行する

カスタム デバイス モデルを使用してシミュレーションを実行できるようになりました。

1. [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard) からデバイス シミュレーション Web UI を起動します。

1. Web UI で、カスタム デバイス モデルのいずれかを使用してシミュレーションを構成および実行します。

1. シミュレーションを実行するときは、**Azure portal で [View IoT Hub metrics]\(IoT Hub メトリックの表示\)** をクリックしてシミュレーションを監視します。 または、次の Azure CLI コマンドを使用して、デバイスからクラウドへのトラフィックに監視することもできます。 IoT ハブの名前を実際のハブ名に置き換えます。

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

さらに調べる場合は、デバイス シミュレーション ソリューション アクセラレータをデプロイしたままにします。

ソリューション アクセラレータが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページでそれを選択し、**[ソリューションの削除]** をクリックして削除してください。

## <a name="next-steps"></a>次の手順

このガイドでは、カスタム デバイス モデルをデバイス シミュレーション ソリューション アクセラレータに展開する方法について説明しました。 推奨される次の手順は、[デバイス モデル スキーマ](iot-accelerators-device-simulation-device-schema.md) について学習することです。
