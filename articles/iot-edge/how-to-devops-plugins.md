---
title: Jenkins プラグインを使用して CI/CD を有効にする - Azure IoT Edge | Microsoft Docs
description: Jenkins 用 Azure IoT Edge 拡張機能を使用すると、IoT Edge の開発とデプロイのタスクを既存の DevOps ソリューションに統合することができます。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510245"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Azure IoT Edge と Jenkins パイプラインの統合

Azure IoT Edge には、Azure DevOps と Azure DevOps Projects 向けの組み込みのサポートがあるだけでなく、DevOps の機能を Jenkins に拡張するプラグインも用意されています。 [Jenkins](https://jenkins.io/) は、プラグインを使用してさまざまな種類のデプロイと IoT Edge などのデプロイ プロジェクトをサポートするオープン ソースのオートメーション サーバーです。

Jenkins 用の Azure IoT Edge プラグインでは、継続的インテグレーションと継続的なデプロイに重点を置いてます。 モジュールをビルドし、そのコンテナー イメージをコンテナー レジストリにプッシュするビルドとプッシュのパイプラインを作成できます。 次に、モジュールをご使用の IoT Edge デバイスにデプロイするリリース パイプラインを作成します。

Jenkins 用の Azure IoT Edge プラグインの使用を開始する前に、Azure 内に IoT ハブとコンテナー イメージを保持するコンテナー レジストリが必要です。 Azure サービス プリンシパルを使用して、プラグインがご使用の IoT Edge デバイスにデプロイを作成できるように、Jenkins の共同作業作成者アクセス許可を IoT ハブに付与します。

開始する準備ができたら、インストールを探して、[Jenkins 用 Azure IoT Edge プラグイン](https://plugins.jenkins.io/azure-iot-edge)の詳細を使用します。
