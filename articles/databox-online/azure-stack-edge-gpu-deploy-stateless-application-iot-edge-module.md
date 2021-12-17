---
title: IoT Edge モジュールを使用して Azure Stack Edge Pro GPU に Kubernetes ステートレス アプリをデプロイする | Microsoft Docs
description: 外部 IP 経由でアクセスされる IoT Edge モジュールを使用して、Azure Stack Edge Pro GPU デバイスに Kubernetes ステートレス アプリケーションをデプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 233afd9b103a250633c5e49776fc0dfda55a0389
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112250"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>IoT Edge モジュールを使用して、Azure Stack Edge Pro GPU デバイスで Kubernetes ステートレス アプリケーションを実行する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、IoT Edge モジュールを使用して、Azure Stack Edge Pro デバイスにステートレス アプリケーションをデプロイする方法について説明します。

ステートレス アプリケーションをデプロイするには、次の手順を実行します。

- IoT Edge モジュールをデプロイする前に、前提条件が満たされていることを確認します。
- IoT Edge モジュールを追加して、お使いの Azure Stack Edge Pro でコンピューティング ネットワークにアクセスします。
- 有効になっているネットワーク インターフェイスにモジュールがアクセスできることを確認します。

この記事では、Web サーバー アプリのモジュールを使用したシナリオについて説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしている必要があります。

- Azure Stack Edge Pro デバイス。 次のことを確認してください。

    - デバイスにコンピューティング ネットワークの設定が構成されている。
    - [チュートリアル:デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事の手順に従って、デバイスがアクティブ化されている。
- **コンピューティングの構成** が完了している。コンピューティングの構成は、お使いのデバイスで、[チュートリアル: Azure Stack Edge Pro デバイスでのコンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関する記事に従って構成します。 お使いのデバイスには、関連付けられた IoT Hub リソース、IoT デバイス、および IoT Edge デバイスが必要です。


## <a name="add-webserver-app-module"></a>Web サーバー アプリ モジュールを追加する

Azure Stack Edge Pro デバイスに Web サーバー アプリ モジュールを追加するには、次の手順を行います。

1. デバイスに関連付けられている IoT Hub リソースで、 **[自動デバイス管理] > [IoT Edge]** に移動します。
1. お使いの Azure Stack Edge Pro デバイスに関連付けられている IoT Edge デバイスを選択してクリックします。 

    ![IoT Edge デバイスの選択](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. **[Set modules]\(モジュールの設定\)** を選びます。 **[デバイスへのモジュールの設定]** で、 **[+ 追加]** 、 **[IoT Edge モジュール]** の順に選択します。

    ![[IoT Edge モジュール] を選択する](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. **[IoT Edge モジュールの追加]** で:

    1. デプロイする Web サーバー アプリ モジュールの **名前** を指定します。
    2. **[モジュールの設定]** タブで、モジュール イメージの **[イメージ URI]** を指定します。 指定した名前とタグに一致するモジュールが取得されます。 このケースでは、`mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` によって、パブリック `mcr.microsoft.com` レジストリから、(1.15.5-alpine としてタグ付けされた) nginx イメージがプルされます。

        ![IoT Edge モジュールの追加](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. **[コンテナーの作成オプション]** タブで、次のサンプル コードを貼り付けます。  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        この構成を使用すると、TCP ポート 8080 で、コンピューティング ネットワーク IP over *http* を使用してモジュールにアクセスできます (既定の Web サーバー ポートは 80)。 **[追加]** を選択します。

        ![[IoT Edge カスタム モジュール] ブレードでポート情報を指定する](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. **[Review + create]\(レビュー + 作成\)** を選択します。 モジュールの詳細を確認し、 **[作成]** を選択します。

## <a name="verify-module-access"></a>モジュール アクセスを確認する

1. モジュールが正常にデプロイされ、実行されていることを確認します。 **[モジュール]** タブで、モジュールの状態が **[実行中]** になっているはずです。  

    ![モジュールの状態が [実行中] であることを確認する](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Web サーバー アプリの外部エンドポイントを取得するには、[Kubernetes ダッシュボードにアクセスします](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)。 
1. ダッシュボードの左側のペインで、**iotedge** 名前空間によってフィルター処理します。 **[検出と負荷分散] > [サービス]** に移動します。 表示されたサービスの一覧で、Web サーバー アプリ モジュールの外部エンドポイントを探します。 

    ![外部エンドポイントで Web サーバー アプリに接続する](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 外部エンドポイントを選択して、新しいブラウザー ウィンドウを開きます。

    Web サーバー アプリが実行中であることがわかります。

    ![指定したポート経由でモジュールに接続されていることを確認する](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>次のステップ

- IoT Edge モジュールを使用してステートフル アプリケーションを公開する方法について学習してください<!--insert link-->.
