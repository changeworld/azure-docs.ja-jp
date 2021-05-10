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
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587257"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Kubernetes に IoT Edge をインストールする方法 (プレビュー)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge を Kubernetes と統合し、それを回復性があり、可用性が高いインフラストラクチャ層として利用できます。 高レベルの IoT Edge ソリューション内でこのサポートが適合する場所を示します。

![k8s 概要](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>この統合の優れたメンタル モデルは、Linux や Windows に加えて IoT Edge アプリケーションを実行できる別の運用環境として Kubernetes を考えることです。

## <a name="architecture"></a>アーキテクチャ 
Kubernetes では、IoT Edge によって、エッジ ワークロードのデプロイに *カスタム リソース定義* (CRD) が提供されます。 IoT Edge Agent では、クラウドにより管理される目的の状態とローカル クラスターの状態を調整する *CRD コントローラー* のロールが想定されます。

モジュールの有効期間は Kubernetes スケジューラーによって管理されます。このスケジューラーはモジュールの可用性を管理し、その配置を選択します。 IoT Edge は一番上で実行されるエッジ アプリケーション プラットフォームを管理し、IoT Hub に指定されている目的の状態とエッジ クラスターでの状態と継続的に照合します。 アプリケーション モデルはそれでも、IoT Edge のモジュールとルートを基礎とするおなじみのモデルです。 IoT Edge エージェント コントローラーでは、ポッド、デプロイ、サービスなど、Kubernetes ネイティブのコンストラクトへの "*自動*" 変換 IoT Edge のアプリケーション モデルが実行されます。

アーキテクチャの概要図は次のようになります。

![Kubernetes アーキテクチャ](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

エッジ デプロイの各コンポーネントの範囲は、デバイスに固有の Kubernetes 名前空間に設定され、複数のエッジ デバイスとそのデプロイ間で同じクラスター リソースを共有することを可能にします。

>[!NOTE]
>Kubernetes の IoT Edge は[一般プレビューの段階](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)にあります。

## <a name="tutorials-and-references"></a>チュートリアルとリファレンス 

詳細なチュートリアルやリファレンスなどの詳細については、[Kubernetes 上の IoT Edge のプレビュー ドキュメント ミニサイト](https://aka.ms/edgek8sdoc)を参照してください。
