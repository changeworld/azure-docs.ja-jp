---
title: "Azure Machine Learning モデルの Azure IoT Edge デバイスへの展開 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning モデルを Azure IoT Edge デバイスに展開する方法について説明します。"
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 60db74535edd09841a1b660c2bf832af65fbfcad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Azure Machine Learning モデルの Azure IoT Edge デバイスへの展開

Docker ベースの Web サービスとしてのコンテナー化された Azure Machine Learning モデルはすべて、Azure IoT Edge デバイスでも実行できます。 追加のスクリプトと手順については、「[AI Toolkit for Azure IoT Edge (Azure IoT Edge の AI Toolkit)](http://aka.ms/AI-toolkit)」をご覧ください。

## <a name="operationalize-the-model"></a>モデルの運用化
[Azure Machine Learning モデル管理の Web サービス展開](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)に関する手順に従ってモデルを操作可能にし、ご使用のモデルで Docker イメージを作成します。

## <a name="deploy-to-azure-iot-edge"></a>Azure IoT Edge への展開
Azure IoT Edge によって、クラウド分析やカスタム ビジネス ロジックがデバイスに移動します。 Machine Learning モデルはすべて、IoT Edge デバイスで実行できます。 IoT Edge デバイスの設定と展開の作成に関するドキュメントは、[aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc) にあります。

その他の注意事項を次に示します。

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>お使いの Edge デバイスの Edge ランタイムにレジストリの資格情報を追加する
IoT Edge を実行しているコンピューターでは、レジストリの資格情報を追加して、ランタイムがコンテナーにアクセスしてプルできるようにします。

Windows の場合は、次のコマンドを実行します。
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Linux の場合は、次のコマンドを実行します。
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Machine Learning のコンテナー イメージの場所の検索
お使いの Machine Learning コンテナー イメージの場所が必要になります。 コンテナー イメージの場所を検索するには、以下のようにします。

1. [Azure Portal](http://portal.azure.com/) にログインします。
2. **[Azure Container Registry]** で、調査対象のレジストリを選択します。
3. レジストリで、**[リポジトリ]** をクリックして、すべてのリポジトリの一覧とそのイメージを表示します。













