---
title: Kubernetes に IoT Edge をインストールする方法 | Microsoft Docs
description: ローカル開発クラスター環境を利用し、Kubernetes に IoT Edge をインストールする方法について説明します
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510211"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes に IoT Edge をインストールする方法 (プレビュー)

IoT Edge を Kubernetes と統合し、それを回復性があり、可用性が高いインフラストラクチャ層として利用できます。 これは IoT Edge *Custom Resource Definition* (CRD) を Kubernetes API Server に登録します。 また、クラウドにより管理される目的の状態とローカル クラスターの状態を照合する *Operator* (IoT Edge エージェント) を提供します。

モジュールの有効期間は Kubernetes スケジューラーによって管理されます。このスケジューラーはモジュールの可用性を管理し、その配置を選択します。 IoT Edge は一番上で実行されるエッジ アプリケーション プラットフォームを管理し、IoT Hub に指定されている目的の状態とエッジ クラスターでの状態と継続的に照合します。 エッジ アプリケーション モデルはそれでも、IoT Edge のモジュールとルートを基礎とするおなじみのモデルです。 IoT Edge エージェントの演算子は、ポッド、デプロイ、サービスなど、Kubernetes ネイティブのコンストラクトへの*自動*変換を実行します。

アーキテクチャの概要図は次のようになります。

![Kubernetes アーキテクチャ](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

エッジ デプロイの各コンポーネントの範囲は、デバイスに固有の Kubernetes 名前空間に設定され、複数のエッジ デバイスとそのデプロイ間で同じクラスター リソースを共有することを可能にします。

>[!NOTE]
>Kubernetes の IoT Edge は[一般プレビューの段階](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

## <a name="install-locally-for-a-quick-test-environment"></a>テスト環境を簡単に用意する目的でローカルにインストールする

### <a name="prerequisites"></a>前提条件

* Kubernetes 1.10 以降。 既存のクラスター セットアップがない場合、[Minikube](https://kubernetes.io/docs/setup/minikube/) をローカル クラスター環境として利用できます。

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide)。これは Kubernetes パッケージ マネージャーです。

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。クラスターを表示し、操作するために使用します。

### <a name="setup-steps"></a>セットアップの手順

1. **Minikube** を起動します。

    ``` shell
    minikube start
    ```

1. クラスターで **Helm** サーバー コンポーネント (*tiller*) を初期化します。

    ``` shell
    helm init
    ```

1. IoT Edge リポジトリを追加し、helm インストールを更新します。

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [IoT Hub を作成し](../iot-hub/iot-hub-create-through-portal.md)、[IoT Edge デバイスを登録し](how-to-register-device.md)、その接続文字列をメモします。

1. iotedged と IoT Edge エージェントをクラスターにインストールします。

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. ブラウザーで Kubernetes ダッシュボードを開きます。

    ```shell
    minikube dashboard
    ```

    クラスターの名前空間に IoT Edge デバイスが *msiot-\<iothub-name>-\<edgedevice-name>* の形式で表示されます。 IoT Edge エージェントと iotedged ポッドがこの名前空間で稼働しているはずです。

1. クイックスタートの「[モジュールを展開する](quickstart-linux.md#deploy-a-module)」セクションの手順で、シミュレートされた温度センサー モジュールを追加します。 IoT Edge モジュールは、他の IoT Edge デバイスと同様に、IoT Hub ポータルから管理されます。 Kubernetes ツールでモジュールの構成をローカル変更することは推奨されません。上書きされる可能性があります。

1. 数秒後、ダッシュボードのエッジ デバイス名前空間の下で **[ポッド]** ページが更新され、IoT Edge ハブとシミュレートされたセンサー ポッドが実行中として一覧表示されます。IoT Edge ハブ ポッドは IoT Hub にデータを取り込んでいます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

エッジ デプロイにより作成されたリソースをすべて削除するには、次のコマンドを使用します。その際、前のセクションの手順 5 で使用した名前を指定します。

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>次のステップ

### <a name="deploy-as-a-highly-available-edge-gateway"></a>高可用エッジ ゲートウェイとしてデプロイする

Kubernetes クラスターのエッジ デバイスは、ダウンストリーム デバイスの IoT ゲートウェイとして利用できます。 ノードの故障に対して回復性が与えられ、エッジ デプロイの可用性が上がるように構成できます。 このシナリオで IoT Edge を使用する方法については、こちらの[詳しいチュートリアル](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes)をご覧ください。
