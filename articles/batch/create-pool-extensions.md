---
title: Batch プールで拡張機能を使用する
description: 拡張機能は、プロビジョニング後の構成とバッチ計算ノードでのセットアップを容易にする小規模なアプリケーションです。
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: bac3c6c53552ac99aef6210cbe184da754580ca0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508686"
---
# <a name="use-extensions-with-batch-pools"></a>Batch プールで拡張機能を使用する

拡張機能は、プロビジョニング後の構成とバッチ計算ノードでのセットアップを容易にする小規模なアプリケーションです。 Azure Batch で許可されている任意の拡張機能を選択し、プロビジョニング時に計算ノードにインストールできます。 その後、その拡張機能で目的の操作を実行できます。

検出、修正、または診断の機能を追跡するために、使用する拡張機能のライブ状態を確認し、それによって返された情報を取得できます。

## <a name="prerequisites"></a>前提条件

- 拡張機能が使用されているプールでは[仮想マシン構成](nodes-and-pools.md#virtual-machine-configuration)を使用する必要があります。
- CustomScript 拡張機能の種類は Azure Batch サービス用に予約されているため、オーバーライドできません。
- 一部の拡張機能では、適切に機能するために、コンピューティング ノードのコンテキストでアクセス可能なプールレベルのマネージド ID が必要な場合があります。 拡張機能に該当する場合は、「[Batch プールでマネージド ID を構成する](managed-identity-pools.md)」を参照してください。

## <a name="supported-extensions"></a>サポートされる拡張機能

次の拡張機能は、現在 Batch プールを作成するときにインストールできます。

- [Linux 用の Azure Key Vault 拡張機能](../virtual-machines/extensions/key-vault-linux.md)
- [Windows 用の Azure Key Vault 拡張機能](../virtual-machines/extensions/key-vault-windows.md)
- [Linux 用の Azure Monitor のログ分析と監視拡張機能](../virtual-machines/extensions/oms-linux.md)
- [Windows 用の Azure Monitor のログ分析と監視拡張機能](../virtual-machines/extensions/oms-windows.md)
- [Azure Desired State Configuration (DSC) 拡張機能](../virtual-machines/extensions/dsc-overview.md)
- [Windows VM 用の Azure Diagnostics 拡張機能](../virtual-machines/windows/extensions-diagnostics.md)
- [AMD 上の Windows 用の HPC GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)
- [NVIDIA 上の Windows 用の HPC GPU ドライバー拡張機能](../virtual-machines/extensions/hpccompute-gpu-windows.md)
- [Windows 用の Microsoft Antimalware 拡張機能](../virtual-machines/extensions/iaas-antimalware-windows.md)

サポート リクエストを開いて、追加のパブリッシャーや拡張機能の種類に対するサポートを要請できます。

## <a name="create-a-pool-with-extensions"></a>拡張機能を使用してプールを作成する

下の例では、Azure Key Vault 拡張機能が使用される Linux ノードの Batch プールを作成します。

REST API URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

要求本文

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>プールから拡張機能データを取得する

次の例では、Azure Key Vault 拡張機能からデータを取得します。

REST API URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

応答本文

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>次のステップ

- [プール ノードにアプリケーションとデータをコピーする](batch-applications-to-pool-nodes.md)さまざまな方法について説明します。
- [ノードとプール](nodes-and-pools.md)の操作の詳細について説明します。
