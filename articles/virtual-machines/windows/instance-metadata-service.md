---
title: Windows 用の Azure Instance Metadata Service
description: Azure Instance Metadata Service の概要と、Windows で現在実行中の仮想マシン インスタンスに関する情報が提供される方法について説明します。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435231"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Azure Instance Metadata Service (IMDS) によって、現在実行中の仮想マシン インスタンスに関する情報が提供されます。 これを使用して、仮想マシンの管理と構成を行うことができます。
この情報には、SKU、ストレージ、ネットワークの構成、今後のメンテナンス イベントなどがあります。 使用できるデータの完全な一覧については、「[メタデータ API](#metadata-apis)」を参照してください。


IMDS は、仮想マシン (VM) のインスタンスと仮想マシン スケール セット インスタンスを実行するために使用できます。 [Azure Resource Manager](/rest/api/resources/) を使用して作成および管理されている VM は、すべての API でサポートされます。 クラシック デプロイ モデルを使用して作成された VM がサポートされるのは、構成証明されたエンドポイントとネットワーク エンドポイントのみです。 構成証明されたエンドポイントでは、制限された範囲のみでサポートされます。

IMDS は、既知のルーティング不可能な IP アドレス (`169.254.169.254`) で使用できる REST エンドポイントです。 VM 内からのみアクセスできます。 VM と IMDS 間の通信がホストから離れることはありません。
IMDS に対してクエリを実行する場合は、HTTP クライアントから VM 内の Web プロキシをバイパスし、`169.254.169.254` を [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) と同様に扱います。

## <a name="security"></a>セキュリティ

IMDS のエンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティング不可能な IP アドレスでアクセスできます。 さらに、`X-Forwarded-For` ヘッダーがあるすべての要求は、サービスによって拒否されます。
また、実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に `Metadata: true` ヘッダーを含める必要があります。

> [!IMPORTANT]
> IMDS は、機密データ用のチャネルではありません。 このエンドポイントは、VM 上のすべてのプロセスに対して開かれています。 このサービスを通じて公開される情報は、VM 内で実行されているすべてのアプリケーションに対して共有される情報と考えてください。

## <a name="usage"></a>使用方法

### <a name="access-azure-instance-metadata-service"></a>Azure Instance Metadata Service にアクセスする

IMDS にアクセスするには、[Azure Resource Manager](/rest/api/resources/) または [Azure ポータル](https://portal.azure.com)から VM を作成し、以下のサンプルを使用します。
その他の例については、「[Azure Instance Metadata Samples (Azure Instance Metadata のサンプル)](https://github.com/microsoft/azureimds)」を参照してください。

インスタンスのすべてのメタデータを取得するサンプル コードを次に示します。 特定のデータ ソースにアクセスするには、「[メタデータ API](#metadata-apis)」セクションを参照してください。 

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy` フラグを使用できるのは、PowerShell 6 以降のみです。 プロキシを設定していない場合は、フラグを省略できます。

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 REST クエリを `ConvertTo-Json` コマンドレットにパイプでつないで整形出力します。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>データ出力

既定では、IMDS によって JSON 形式 (`Content-Type: application/json`) でデータが返されます。 ただし、一部の API では、要求された場合、別の形式でデータを返すことができます。
次の表は、API でサポートされる可能性のあるその他のデータ形式を示しています。

API | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/attested | json | なし
/identity | json | なし
/instance | json | text
/scheduledevents | json | なし

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> `/metadata/instance` のリーフ ノードでは `format=json` は機能しません。 既定の形式は JSON なので、これらのクエリでは `format=text` を明示的に指定する必要があります。

### <a name="version"></a>Version

IMDS はバージョン管理されています。HTTP 要求で API バージョンを指定することは必須です。

サポートされる API のバージョンは次のとおりです。 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> バージョン 2020-10-01 は、まだ利用できないリージョンがある可能性があります。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために以前のバージョンにもアクセスできます。

バージョンを指定しない場合、エラーが表示され、サポートされている最新バージョンの一覧が示されます。

> [!NOTE]
> 応答は JSON 文字列です。 次の例では、バージョンが指定されていない場合のエラーの状態を示しています。 応答は、読みやすくするために整形出力されています。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Response**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>メタデータ API

IMDS には、さまざまなデータ ソースを表す複数の API があります。

API | 説明 | 導入されたバージョン
----|-------------|-----------------------
/attested | 「[構成証明済みデータ](#attested-data)」を参照してください | 2018-10-01
/identity | [アクセス トークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください | 2018-02-01
/instance | 「[インスタンス API](#instance-api)」を参照してください | 2017-04-02
/scheduledevents | [スケジュールされたイベント](scheduled-events.md)に関する記事を参照してください | 2017-08-01

## <a name="instance-api"></a>インスタンス API

インスタンス API は、VM、ネットワーク、ストレージなど、VM インスタンスの重要なメタデータを公開します。 `instance/compute` を使用して、次のカテゴリにアクセスできます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
azEnvironment | VM が実行されている Azure 環境。 | 2018-10-01
customData | この機能は現在無効になっています。 | 2019-02-01
isHostCompatibilityLayerVm | ホストの互換性レイヤーで VM が実行されているかどうかを示します。 | 2020-06-01
licenseType | [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)のライセンスの種類。 これは AHB 対応 VM でのみ利用できることに注意してください。 | 2020-09-01
location | VM が実行されている Azure リージョン。 | 2017-04-02
name | VM の名前。 | 2017-04-02
offer | VM イメージのプラン情報。 これは、Azure イメージ ギャラリーからデプロイされるイメージにのみ存在します。 | 2017-04-02
osProfile.adminUsername | 管理者アカウントの名前を指定します。 | 2020-07-15
osProfile.computerName | コンピュータの名前を指定します。 | 2020-07-15
osProfile.disablePasswordAuthentication | パスワード認証が無効かどうかを指定します。 これは Linux VM でのみ利用できることに注意してください。 | 2020-10-01
osType | Linux または Windows。 | 2017-04-02
placementGroupId | お使いの仮想マシン スケール セットの[配置グループ](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)。 | 2017-08-01
plan | VM が Azure Marketplace イメージである場合、[プラン](/rest/api/compute/virtualmachines/createorupdate#plan)にはその名前、製品、発行元が含まれています。 | 2018-04-02
platformUpdateDomain |  VM が実行されている[更新ドメイン](../manage-availability.md)。 | 2017-04-02
platformFaultDomain | VM が実行されている[障害ドメイン](../manage-availability.md)。 | 2017-04-02
provider | VM のプロバイダー。 | 2018-10-01
publicKeys | VM とパスに割り当てられた[公開キーのコレクション](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)。 | 2018-04-02
publisher | VM イメージの発行元。 | 2017-04-02
resourceGroupName | VM の[リソース グループ](../../azure-resource-manager/management/overview.md)。 | 2017-08-01
resourceId | リソースの[完全修飾](/rest/api/resources/resources/getbyid) ID。 | 2019-03-11
sku | VM イメージの特定の SKU。 | 2017-04-02
securityProfile.secureBootEnabled | VM で UEFI セキュア ブートが有効になっているかどうかを示します。 | 2020-06-01
securityProfile.virtualTpmEnabled | VM で仮想トラステッド プラットフォーム モジュール (TPM) が有効になっているかどうかを示します。 | 2020-06-01
storageProfile | [ストレージ プロファイル](#storage-metadata)を参照してください。 | 2019-06-01
subscriptionId | VM の Azure サブスクリプション。 | 2017-08-01
tags | VM の[タグ](../../azure-resource-manager/management/tag-resources.md)。  | 2017-08-01
tagsList | プログラムによる解析を簡単にするために JSON 配列として書式設定されたタグ。  | 2019-06-04
version | VM イメージのバージョン。 | 2017-04-02
vmId | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2017-04-02
vmScaleSetName | お使いの仮想マシン スケール セットの[仮想マシン スケール セット名](../../virtual-machine-scale-sets/overview.md)。 | 2017-12-01
vmSize | [VM のサイズ](../sizes.md)に関する記事を参照してください。 | 2017-04-02
ゾーン | VM の[可用性ゾーン](../../availability-zones/az-overview.md)。 | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>サンプル 1:Azure で実行されている VM を追跡する

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、IMDS から `vmId` フィールドを使用します。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>サンプル 2:異なるデータ レプリカの配置

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した [HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておく必要がある場合があります。
これらの決定を行うために、インスタンスの[可用性ゾーン](../../availability-zones/az-overview.md)を使用することもできます。
IMDS を介して、このデータに直接クエリを実行できます。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>サンプル 3: サポート ケースの際の VM に関する詳細情報の取得

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 この場合、コンピューティング メタデータを共有するよう顧客に依頼すると役に立つことがあります。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>サンプル 4: VM が実行されている Azure 環境の取得

Azure には、[Azure Government](https://azure.microsoft.com/overview/clouds/government/) などさまざまなソブリン クラウドがあります。 ランタイムの決定を行うために、Azure 環境が必要な場合があります。 次の例では、この動作を実現する方法を示します。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

クラウドと Azure 環境の値を次に示します。

 クラウド   | Azure 環境
---------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>ネットワーク メタデータ 

ネットワーク メタデータは、インスタンス API の一部です。 次のネットワーク カテゴリは、`instance/network` エンドポイントを介して使用できます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
ipv4/privateIpAddress | VM のローカル IPv4 アドレス。 | 2017-04-02
ipv4/publicIpAddress | VM のパブリック IPv4 アドレス。 | 2017-04-02
subnet/address | VM のサブネット アドレス。 | 2017-04-02
subnet/prefix | サブネットのプレフィックス。 例:24 | 2017-04-02
ipv6/ipAddress | VM のローカル IPv6 アドレス。 | 2017-04-02
macAddress | VM の MAC アドレス。 | 2017-04-02

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次のすべての例の応答は、読みやすくするために整えられています。

#### <a name="sample-1-retrieve-network-information"></a>サンプル 1:ネットワーク情報の取得

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Response**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>サンプル 2:パブリック IP アドレスの取得

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>ストレージ メタデータ

ストレージ メタデータは、`instance/compute/storageProfile` エンドポイントの下にあるインスタンス API の一部です。
VM に関連付けられているストレージ ディスクの詳細を提供します。 

VM のストレージ プロファイルは、イメージ参照、オペレーティング システム ディスク、およびデータ ディスクの 3 つのカテゴリに分類されます。

イメージ参照オブジェクトには、オペレーティング システム イメージに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
id      | Resource ID
offer   | プラットフォームまたはイメージのオファー
publisher | イメージ発行者
sku     | イメージ SKU
version | プラットフォームまたはイメージのバージョン

オペレーティング システム ディスク オブジェクトには、VM によって使用される オペレーティング システム ディスクに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
encryptionSettings | ディスクの暗号化の設定
image   | ソース ユーザー イメージの仮想ハード ディスク
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
osType  | ディスクに含まれているオペレーティング システムの種類
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで `writeAccelerator` が有効になっているかどうか

データ ディスク アレイには、VM に接続されているデータ ディスクの一覧が含まれています。 各データ ディスク オブジェクトには、次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
image   | ソース ユーザー イメージの仮想ハード ディスク
lun     | ディスクの論理ユニット番号
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで `writeAccelerator` が有効になっているかどうか

次の例は、VM のストレージ情報に対してクエリを実行する方法を示しています。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**Response**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM のタグ

VM タグは、`instance/compute/tags` エンドポイントの下にあるインスタンス API に含まれています。
論理的に分類するために Azure VM にタグが適用されている場合があります。 VM に割り当てられているタグを取得するには、次の要求を使用します。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` フィールドは、セミコロンで区切られたタグを含む文字列です。 この出力は、タグ自体でセミコロンが使用されている場合に問題になることがあります。 プログラムでタグを抽出するようにパーサーが作成されている場合は、`tagsList` フィールドを使用する必要があります。 `tagsList` フィールドは区切り記号のない JSON 配列であるため、解析が容易になります。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**Response**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>構成証明済みデータ

IMDS は、提供されるデータが Azure から送信されることを保証するために役立ちます。 Microsoft によってこの情報の一部に署名が行われるため、Azure Marketplace のイメージが Azure で実行されているものであることを確認できます。

### <a name="sample-1-get-attested-data"></a>サンプル 1:構成証明済みデータの取得

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

**Request**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた `nonce` 値が返される場合があります。

`Api-version` は必須フィールドです。 サポートされる API バージョンについては、[使用法のセクション](#usage)を参照してください。
`Nonce` は、省略可能な 10 桁の文字列です。 指定されていない場合、IMDS は現在の世界協定時のタイムスタンプを代わりに返します。

**Response**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

signature BLOB は、ドキュメントの [pkcs7](https://aka.ms/pkcs7) で署名されたバージョンです。 特定の VM 固有の詳細とともに署名に使用される証明書が含まれています。 

Azure Resource Manager を使用して作成された VM の場合、これにはドキュメントの作成と有効期限のための `vmId`、`sku`、`nonce`、`subscriptionId`、`timeStamp` とイメージに関するプラン情報が含まれています。 プラン情報は Azure Marketplace のイメージにのみ設定されます。 

クラシック デプロイ モデルを使用して作成された VM の場合、設定されることが保証されるのは `vmId` のみです。 応答から証明書を抽出し、応答が有効で Azure から送られたものであることを確認するために使用できます。

ドキュメントには次のフィールドが含まれています。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
licenseType | [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit)のライセンスの種類。 これは AHB 対応 VM でのみ利用できることに注意してください。 | 2020-09-01
nonce | 必要に応じて、要求で指定できる文字列。 `nonce` が指定されていない場合は、現在の協定世界時のタイムスタンプが使用されます。 | 2018-10-01
plan | [Azure Marketplace イメージ プラン](/rest/api/compute/virtualmachines/createorupdate#plan)。 プラン ID (名前)、製品イメージまたはオファー (製品)、およびパブリッシャー ID (パブリッシャー) が含まれます。 | 2018-10-01
timestamp/createdOn | 署名されたドキュメントが作成されたときの協定世界時のタイムスタンプ。 | 2018-20-01
timestamp/expiresOn | 署名されたドキュメントの有効期限が切れるときの協定世界時のタイムスタンプ。 | 2018-10-01
vmId |  VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2018-10-01
subscriptionId | VM の Azure サブスクリプション。 | 2019-04-30
sku | VM イメージの特定の SKU。 | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>サンプル 2:VM が Azure で実行されていることの検証

Azure Marketplace のベンダーは、自分たちのソフトウェアが Azure でのみ実行されるようにライセンスされていることを確認することを望みます。 だれかが VHD をオンプレミスの環境にコピーする場合、ベンダーはそれを検出できる必要があります。 これらのベンダーは、IMDS を介して、応答が Azure のみからであることを保証する署名付きデータを取得できます。

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

署名が Microsoft Azure のものであることを確認し、証明書チェーンにエラーがないか確認します。

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> IMDS のキャッシュ メカニズムにより、以前にキャッシュされた `nonce` 値が返される場合があります。

最初の要求で `nonce` パラメーターを指定した場合、署名されたドキュメントの `nonce` を比較できます。

> [!NOTE]
> パブリック クラウド用の証明書と各ソブリン クラウド用の証明書は異なります。

クラウド | Certificate
------|------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 証明書では、パブリック クラウドに対して `metadata.azure.com` が完全に一致していない可能性があります。 そのため、証明書の検証では、任意の `.metadata.azure.com` サブドメインからの共通名を許可する必要があります。

検証時にネットワークの制約が原因で中間証明書をダウンロードできない場合は、中間証明書をピン留めできます。 Azure によって証明書がロールオーバーされることに注意してください。これは PKI の標準的な動作です。 ロールオーバーが行われたら、ピン留めされた証明書を更新する必要があります。 中間証明書を更新する変更を計画すると、そのたびに、Azure ブログが更新され、Azure の顧客に通知されます。 

中間証明書は、「[PKI リポジトリ](https://www.microsoft.com/pki/mscorp/cps/default.htm)」で見つけることができます。 中間証明書は、リージョンごとに異なる場合があります。

> [!NOTE]
> Azure China 21Vianet 用の中間証明書は、Baltimore ではなく DigiCert Global Root CA からのものです。
ルート チェーン機関の変更の一環として Azure China 用の中間証明書をピン留めした場合は、中間証明書を更新する必要があります。

## <a name="failover-clustering-in-windows-server"></a>Windows Server でのフェールオーバー クラスタリング

フェールオーバー クラスタリングを使用して IMDS に対してクエリを実行する場合は、ルーティング テーブルにルートを追加する必要がある場合があります。 その方法は次のとおりです。

1. 管理者特権でコマンド プロンプトを開きます。

1. 次のコマンドを実行し、IPv4 ルーティング テーブルのネットワーク宛先 (`0.0.0.0`) のインターフェイスのアドレスをメモします。

```bat
route print
```

> [!NOTE]
> 次の出力例は、フェールオーバー クラスターが有効になっている Windows Server VM からのものです。 わかりやすくするために、出力には IPv4 ルート テーブルだけが含まれています。

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

次のコマンドを実行し、ネットワーク宛先 (`0.0.0.0`) のインターフェイスのアドレスを使用します。この例では `10.0.1.10` です。

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>マネージド ID

システムによって割り当てられたマネージド ID は、VM 上で有効にすることができます。 1 つ以上のユーザー割り当てのマネージド ID を VM に割り当てることもできます。
その後、IMDS からマネージド ID のトークンを要求できます。 これらのトークンは、Azure Key Vault などの他の Azure サービスで認証するために使用します。

この機能を有効にするための詳細な手順については、[アクセストークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください。

## <a name="scheduled-events"></a>スケジュールされたイベント
IMDS を使用すると、スケジュールされたイベントの状態を取得できます。 その後、ユーザーは、これらのイベントに対して実行する一連のアクションを指定できます。 詳細については、「[スケジュールされたイベント](scheduled-events.md)」を参照してください。 

## <a name="regional-availability"></a>リージョン別の提供状況

このサービスは、すべての Azure クラウドで一般提供されています。

## <a name="sample-code-in-different-languages"></a>さまざまな言語のサンプル コード

次の表に、VM 内で各種の言語を使用して IMDS を呼び出す例を示します。

Language      | 例
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>エラーとデバッグ

見つからないデータ要素または無効な形式の要求がある場合は、IMDS によって標準 HTTP エラーが返されます。 次に例を示します。

HTTP 状態コード | 理由
-----------------|-------
200 OK |
400 Bad Request | リーフ ノードのクエリ時に `Metadata: true` ヘッダーがないか、`format=json` パラメーターがありません。
404 見つかりません  | 要求された要素は存在しません。
405 Method Not Allowed | `GET` 要求のみがサポートされています。
410 削除 | しばらくしてから再試行してください (最長 70 秒)。
429 Too Many Requests | 現在 API は、1 秒あたり最大 5 つのクエリをサポートしています。
500 Service Error     | しばらくしてからやり直してください。

### <a name="frequently-asked-questions"></a>よく寄せられる質問

**`400 Bad Request, Required metadata header not specified` エラーが発生します。これはどういう意味でしょうか。**

IMDS では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、IMDS へのアクセスが許可されます。

**使用している VM に関するコンピューティング情報を取得できないのはなぜですか。**

現在、IMDS では、Azure Resource Manager で作成されたインスタンスのみがサポートされます。

**しばらく前に Azure Resource Manager で VM を作成しました。コンピューティング メタデータ情報が表示されないのはなぜですか。**

2016 年 9 月以降に VM を作成した場合は、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 2016 年 9 月より前に VM を作成した場合は、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除して、メタデータを更新してください。

**新しいバージョンに入力されたすべてのデータが表示されないのはなぜですか。**

2016 年 9 月以降に VM を作成した場合は、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 2016 年 9 月より前に VM を作成した場合は、VM インスタンスに対して拡張機能またはデータ ディスクを追加/削除して、メタデータを更新してください。

**エラー `500 Internal Server Error` または `410 Resource Gone` が発生するのはなぜですか。**

要求を再試行してください。 詳細については、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」を参照してください。 問題が解決しない場合は、VM の Azure portal でサポートの問題を作成してください。

**これは仮想マシン スケール セット インスタンスで機能しますか。**

はい。IMDS は仮想マシン スケール セット インスタンスで使用できます。

**仮想マシン スケール セットでタグを更新しましたが、(単一インスタンスの VM とは異なり) インスタンスにタグが表示されません。操作方法に何か間違いがありますか。**

現時点では、仮想マシン スケール セットのタグは、再起動、再イメージ化、またはインスタンスに対するディスクの変更の際に VM に対してのみ表示されます。

**サービスの呼び出しの要求がタイムアウトするのはなぜですか。**

メタデータの呼び出しは、VM のプライマリ ネットワーク カードに割り当てられたプライマリ IP アドレスから行う必要があります。 さらに、ルートを変更した場合、VM のローカル ルーティング テーブルにアドレスが 169.254.169.254/32 のルートが存在する必要があります。
   * <details>
        <summary>ルーティング テーブルの確認</summary>

        1. ローカル ルーティング テーブルをダンプし、IMDS エントリを探します。 次に例を示します。
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. `169.254.169.254` のルートが存在することを確認し、対応するネットワーク インターフェイス (`172.16.69.7` など) をメモします。
        1. インターフェイス構成をダンプし、ルーティング テーブルで参照されているものに対応するインターフェイスを見つけ、MAC (物理) アドレスをメモします。
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. インターフェイスが VM のプライマリ NIC とプライマリ IP に対応していることを確認します。 Azure portal でネットワーク構成を確認するか、Azure CLI を使用して調べることで、プライマリ NIC および IP を見つけることができます。 パブリック IP とプライベート IP (および CLI を使用している場合は MAC アドレス) をメモします。 PowerShell CLI の例を次に示します。
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. 一致しない場合は、プライマリ NIC および IP がターゲットになるようにルーティング テーブルを更新します。
    </details>

## <a name="support"></a>サポート

複数回試行してもメタデータの応答を取得できない場合は、Azure portal でサポートの問題を作成できます。
**[問題の種類]** で、 **[管理]** を選択します。 **[カテゴリ]** で、 **[Instance Metadata Service]** を選択します。

![Instance Metadata Service サポートのスクリーンショット](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>次のステップ

[VM のアクセス トークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[スケジュールされたイベント](scheduled-events.md)
