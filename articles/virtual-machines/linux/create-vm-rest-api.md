---
title: Azure REST API を使用して Linux 仮想マシンを作成する | Microsoft Docs
description: マネージド ディスクと SSH 認証を使用する Linux 仮想マシンを Azure REST API を使用して Azure 内に作成する方法を説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928184"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>SSH 認証を使用する Linux 仮想マシンを REST API で作成する

Azure の仮想マシン (VM) は、場所、ハードウェアのサイズ、オペレーティング システム イメージ、ログオン資格情報などのさまざまなパラメーターによって定義されます。 この記事では、SSH 認証を使用する Linux 仮想マシンを REST API を使用して作成する方法について説明します。

仮想マシンを作成または更新するには、次の *PUT* 操作を使用します。

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>要求を作成する

*PUT* 要求を作成するには、`{subscription-id}` パラメーターが必要です。 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)を参照してください｡ リソースの `{resourceGroupName}` と `{vmName}` を `api-version` パラメーターと共に定義します。 この記事では、`api-version=2017-12-01` を使用します。

次のヘッダーは必須です｡

| 要求ヘッダー   | 説明 |
|------------------|-----------------|
| *Content-Type:*  | 必須。 `application/json` を設定します。 |
| *Authorization:* | 必須。 有効な `Bearer` [ アクセス トークン](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します｡ |

要求の作成方法の詳細については、「[Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse)」(REST API 要求/応答のコンポーネント) を参照してください。

## <a name="create-the-request-body"></a>要求本文を作成する

要求本文を作成するには、以下の一般的な定義が使用されます。

| Name                       | 必須 | type                                                                                | 説明  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | 文字列                                                                              | リソースの場所。 |
| name                       |          | 文字列                                                                              | 仮想マシンの名前。 |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 仮想マシンのハードウェア設定を指定します。 |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 仮想マシンのストレージ設定を指定します。 |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 仮想マシンのオペレーティング システム設定を指定します。 |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 仮想マシンのネットワーク インターフェイスを指定します。 |

要求本文で使用可能な定義の完全な一覧については、[仮想マシンの作成または更新要求の本文の定義](/rest/api/compute/virtualmachines/createorupdate#definitions)に関するセクションを参照してください。

### <a name="example-request-body"></a>要求本文の例

次の要求本文の例では、Premium マネージド ディスクを使用する Ubuntu 18.04-LTS イメージを定義します。 SSH 公開キー認証が使用され、[前に作成した](../../virtual-network/virtual-network-network-interface.md)既存の仮想ネットワーク インターフェイス カード (NIC) が VM で使用されます。 *osProfile.linuxConfiguration.ssh.publicKeys.keyData* フィールドに SSH 公開キーを指定します。 必要であれば、[SSH キー ペアを生成](mac-create-ssh-keys.md)できます。

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>応答

バーチャル マシンの作成または更新操作には、2 種類の成功応答があります。

| Name        | type                                                                              | 説明 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 作成日時     |

REST API の応答の詳細については、「[Process the response message](/rest/api/azure/#process-the-response-message)」(応答メッセージを処理する) を参照してください。

### <a name="example-response"></a>応答の例

MV を作成する要求本文の例の圧縮された *201 Created* 応答は、*vmId* が割り当てられ、*provisioningState* が *Creating* であることを示します。

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>次の手順

Azure REST API や他の管理ツール (Azure CLI 2.0 や Azure PowerShell など) の詳細については、以下を参照してください。

- [Azure Compute プロバイダー REST API](/rest/api/compute/)
- [Azure Rest API の開始](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Azure PowerShell モジュール](/powershell/azure/overview)
