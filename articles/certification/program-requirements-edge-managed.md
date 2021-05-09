---
title: Edge Managed 認定の要件
description: Edge Managed 認定プログラムの要件
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975749"
---
# <a name="azure-certification-edge-managed"></a>Azure 認定 Edge Managed 

このドキュメントでは、Azure Certified Device カタログで表されるデバイス固有の機能について説明します。 機能とは、デバイスを記述できる単一のデバイス属性です。 

## <a name="program-purpose"></a>プログラムの目的

Edge Managed は、ベースラインの Azure Certified Device 認定の増分となる認定です。 Edge Managed では、Azure に接続されるデバイスを対象としたデバイス管理標準に焦点を当て、モジュールのデプロイと管理のための IoT ランタイム互換性を検証します。 (以前は、このプログラムは IoT Edge 認定プログラムとして識別されていました。) 

Edge Managed 認定では、モジュールのデプロイと管理における IoT Edge ランタイムの互換性を検証します。 このプログラムでは、Azure に接続された IoT デバイスの管理の信頼度を上げることができます。

## <a name="requirements"></a>必要条件

Edge Managed 管理の認定を受けるには、[Azure Certified Device ベースライン プログラム](.\program-requirements-azure-certified-device.md)のすべての要件を満たす必要があります。

**DPS: テストの目的は、3 つの構成証明メソッドのいずれかを使用して、デバイスが IoT Hub Device Provisioning Service を実装およびサポートしていることを確認することです。**

| **名前**                | AzureReady.DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **ターゲットの可用性** | Ignite (プレビュー)                                                |
| **適用対象**          | 任意のデバイス                                      |
| **OS**                  | 非依存                                                     |
| **検証タイプ**     | 自動                                                    |
| **検証**          | AIC では、デバイス コード サポート DPS を検証します。 **1.** ユーザーは、構成証明メソッド (x.509、TPM、SAS キー) のいずれかを選択する必要があります。 **2.** 構成証明メソッドに応じて、ユーザーは、**a)** X.509 証明書を AICS マネージド DPS スコープにアップロードする、**b)** SAS キーまたは保証キーをデバイスに実装するなど、対応するアクションを実行する必要があります。 **3.** 次に、[接続] ボタンをクリックして、DPS 経由で AICS マネージド IoT Hub に接続します。                                                    |
| **リソース**           |                                                      |
| **Azure の推奨事項:**     | 該当なし                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge ランタイムが存在する: テストの目的は、IoT Edge ランタイム ($edgehub と $edgeagent) を搭載したデバイスが正常に機能していることを確認することです。**

| **名前**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **ターゲットの可用性** | 現在利用可能                                                          |
| **適用対象**          | IoT Edge デバイス                                                   |
| **OS**                  | [Tier1 と Tier2 OS](../iot-edge/support.md)                                                     |
| **検証タイプ**     | 自動                                                    |
| **検証**          | AIC では、インストールされている IoT Edge RT のデプロイ機能を検証します。 **1.** ユーザーは特定の OS を指定する必要があります (Tier1/2 の一覧にない OS は使用できません)。**2.** AIC では、config.yaml を生成して、正規の [シミュレートされた一時センサー エッジ モジュール](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview)をデプロイします。**3.** AIC では、Docker 互換コンテナー サブシステム (Moby) がデバイスにインストールされていることを検証します。**4.** テスト結果は、シミュレートされた一時センサー エッジ モジュールと Docker 互換コンテナー サブシステムの機能が正常にデプロイされたかどうかに基づいて判定されます。                                                    |
| **リソース**           | **a)** [AICS のブログ](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/)、**b)** [認定手順](./overview.md) (すべての追加リソースを含む)、**c)** [要件](./program-requirements-azure-certified-device.md) |
| **Azure の推奨事項:**     | 該当なし                                                    |

### <a name="capability-template"></a>機能テンプレート:

**IoT Edge 簡単な設定: テストの目的は、IoT Edge デバイスを簡単に設定し、物理デバイスの検証中に、IoT Edge ランタイムがプレインストールされていること IoT Edge デバイスで検証することです。**

| **名前**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **ターゲットの可用性** | 現在利用可能 (COVID-19 が原因で現在保留中)                                            |
| **適用対象**          | IoT Edge デバイス                                                   |
| **OS**                  | [Tier1 と Tier2 OS](../iot-edge/support.md)                                                     |
| **検証タイプ**     | 手動/ラボ検証済み                                                    |
| **検証**          | OEM では、物理デバイスを IoT 管理 (HCL) に発送する必要があります。 HCL では、物理デバイスで手動による検証を実行して、次のことを確認します。**1.** EdgeRT が Moby サブシステムを使用している (許可された再配布バージョン)。 Docker ではない。**2.** エッジをデプロイする機能を検証するための最新のエッジ モジュールを選択します。                                                     |
| **リソース**           | **a)** [AICS のブログ](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/)、**b)** [認定手順](./overview.md)、**c)** [要件](./program-requirements-azure-certified-device.md) |
| **Azure の推奨事項:**     | N/A                                                    |