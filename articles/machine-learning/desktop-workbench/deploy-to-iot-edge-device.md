---
title: Azure Machine Learning モデルの Azure IoT Edge デバイスへの展開 | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning モデルを Azure IoT Edge デバイスに展開する方法について説明します。
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 24d3cf0c4b1a1283e7a6a7f61f0bb23dae7143d5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43113528"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Azure Machine Learning モデルの Azure IoT Edge デバイスへの展開

Azure Machine Learning モデルは、Docker ベースの Web サービスとしてコンテナー化することができます。 Azure IoT Edge を使用すると、コンテナーをリモートでデバイスにデプロイできます。 これらのサービスを一緒に使用すると、エッジでモデルを実行して応答時間を短縮し、データ転送を減らすことができます。 

追加のスクリプトと手順については、「[AI Toolkit for Azure IoT Edge (Azure IoT Edge の AI Toolkit)](http://aka.ms/AI-toolkit)」をご覧ください。

## <a name="operationalize-the-model"></a>モデルの運用化

Azure IoT Edge モジュールはコンテナー イメージに基づいています。 機械学習モデルを IoT Edge デバイスにデプロイするには、Docker イメージを作成する必要があります。

[Azure Machine Learning モデル管理の Web サービス展開](model-management-service-deploy.md)に関する手順に従ってモデルを操作可能にし、ご使用のモデルで Docker イメージを作成します。

## <a name="deploy-to-azure-iot-edge"></a>Azure IoT Edge への展開

モデルのイメージを取得したら、それを任意の Azure IoT Edge デバイスにデプロイできます。 Machine Learning モデルはすべて、IoT Edge デバイスで実行できます。 

### <a name="set-up-an-iot-edge-device"></a>IoT Edge デバイスを設定する

Azure IoT Edge のドキュメントを使用してデバイスを準備します。 

1. [Azure IoT Hub にデバイスを登録します](../../iot-edge/how-to-register-device-portal.md)。 このプロセスの出力は、物理デバイスの構成に使用できる接続文字列です。 
2. 物理デバイスに IoT Edge ランタイムをインストールし、接続文字列を使用して構成します。 このランタイムは [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) または [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) デバイスにインストールできます。  


### <a name="find-the-machine-learning-container-image-location"></a>Machine Learning のコンテナー イメージの場所の検索
お使いの Machine Learning コンテナー イメージの場所が必要になります。 コンテナー イメージの場所を検索するには、以下のようにします。

1. [Azure Portal](http://portal.azure.com/) にログインします。
2. **[Azure Container Registry]** で、調査対象のレジストリを選択します。
3. レジストリで、**[リポジトリ]** をクリックして、すべてのリポジトリの一覧とそのイメージを表示します。

Azure portal でコンテナー レジストリを表示しているときに、コンテナー レジストリの資格情報を取得します。 プライベート レジストリからイメージをプルできるように、これらの資格情報を IoT Edge デバイスに渡す必要があります。 

1. コンテナー レジストリで、**[アクセス キー]** をクリックします。 
2. 管理者ユーザーがまだ有効でない場合は**有効**にします。 
3. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値を保存します。 

### <a name="deploy-the-container-image-to-your-device"></a>デバイスにコンテナー イメージをデプロイする

コンテナー イメージとコンテナー レジストリの資格情報を使用して、機械学習モデルを IoT Edge デバイスにデプロイする準備ができました。 

[Azure portal から IoT Edge モジュールをデプロイする](../../iot-edge/how-to-deploy-modules-portal.md)手順に従って、IoT Edge デバイスでモデルを起動します。 











