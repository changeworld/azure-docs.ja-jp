---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24c2bfa4aae94642d3ed66f2cfa6e31ba1e6b19a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457419"
---
エフェメラル OS ディスクは、ローカルの仮想マシン (VM) ストレージで作成され、リモートの Azure Storage に保持されません。 エフェメラル OS ディスクは、ステートレス ワークロードで適切に動作します。この場合、アプリケーションでは個々の VM 障害が許容されますが、大規模なデプロイにかかる時間、または個々の VM インスタンスの再イメージ化にかかる時間が、より重要となります。 また、クラシック デプロイ モデルを使用してデプロイされたアプリケーションを、Resource Manager デプロイ モデルに移動することも適切です。 エフェメラル OS ディスクでは、OS ディスクへの読み取り/書き込み待機時間が短縮され、VM の再イメージ化が高速化されます。 さらに、エフェメラル OS ディスクは無料で、OS ディスクのストレージ コストはかかりません。 
 
エフェメラル ディスクの主な機能は次のとおりです。 
- これらは、Marketplace イメージとカスタム イメージの両方で使用できます。
- VM イメージを最大で VM キャッシュのサイズまでデプロイできます。
- VM を元のブート状態に速やかにリセットまたは再イメージ化することができます。  
- 一時ディスクと同様に、実行時の待機時間が短縮されます。 
- OS ディスクのコストはかかりません。 
 
 
永続 OS ディスクとエフェメラル OS ディスクの主な違いは、次のとおりです。

|                             | 永続 OS ディスク                          | エフェメラル OS ディスク                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS ディスクのサイズ制限      | 2 TiB                                                                                        | VM サイズのキャッシュ サイズまたは 2TiB のうち、小さい方 - [DS](../articles/virtual-machines/linux/sizes-general.md)、[ES](../articles/virtual-machines/linux/sizes-memory.md)、[M](../articles/virtual-machines/linux/sizes-memory.md)、[FS](../articles/virtual-machines/linux/sizes-compute.md)、および [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| サポート対象の VM サイズ          | All                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| サポート対象のディスクの種類           | マネージド OS ディスクとアンマネージド OS ディスク                                                                | マネージド OS ディスクのみ                                                               |
| リージョンのサポート              | すべてのリージョン                                                                                  | すべてのリージョン                              |
| データの永続化            | 書き込まれた OS ディスク データは Azure Storage に格納                                  | OS ディスクに書き込まれたデータは、ローカル VM ストレージに格納され、Azure Storage に保持されない |
| 停止と割り当て解除      | VM とスケール セット インスタンスの停止と割り当て解除、またその状態からの再起動が可能 | VM とスケール セット インスタンスの停止と割り当て解除は不可                                  |
| 特殊化された OS ディスクのサポート | はい                                                                                          | いいえ                                                                                 |
| OS ディスクのサイズ変更              | VM 作成中、VM の停止と割り当て解除後に変更可能                                | VM 作成時のみ変更可能                                                  |
| VM サイズ変更時の動作   | OS ディスク データを維持                                                                    | OS ディスクのデータの削除後に OS を再プロビジョニング                                      |

## <a name="scale-set-deployment"></a>スケール セットのデプロイ  
エフェメラル OS ディスクを使用するスケール セットを作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>VM のデプロイ 
テンプレートを使用して、エフェメラル OS ディスクで VM をデプロイできます。 エフェメラル OS ディスクを使用する VM を作成するプロセスでは、テンプレートで `diffDiskSettings` プロパティをリソースの種類 Microsoft.Compute/virtualMachines に追加します。 また、エフェメラル OS ディスクに対してキャッシュ ポリシーを `ReadOnly` に設定する必要があります。 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>REST を使用して VM を再イメージ化する
現時点では、エフェメラル OS ディスクで仮想マシン インスタンスを再イメージ化するには、唯一の方法として REST API を使用します。 スケール セットの場合、再イメージ化は Powershell、CLI、およびポータルを使用して既に利用できます。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:ローカル OS ディスクのサイズは?**

A:プレビューでは、プラットフォームやイメージを最大で VM キャッシュのサイズまでサポートする予定です。この場合、OS ディスクへのすべての読み取り/書き込みは、仮想マシンと同じノードでローカルに行われます。 

**Q:エフェメラル OS ディスクはサイズ変更できますか?**

A:いいえ。エフェメラル OS ディスクをプロビジョニングした後、OS ディスクをサイズ変更することはできません。 

**Q:エフェメラル VM にマネージド ディスクを接続できますか?**

A:はい。エフェメラル OS ディスクを使用する VM にマネージド データ ディスクをアタッチできます。 

**Q:エフェメラル OS ディスクではすべての VM サイズがサポートされますか?**

A:いいえ。B シリーズ、N シリーズ、H シリーズのサイズを除くすべての Premium Storage VM のサイズ (DS、ES、FS、GS、および M) がサポートされます。  
 
**Q:エフェメラル OS ディスクを既存の VM およびスケール セットに適用できますか?**

A:いいえ。エフェメラル OS ディスクは、VM およびスケール セットの作成時にのみ使用できます。 

**Q:スケール セット内でエフェメラル OS ディスクと通常の OS ディスクを混在させることはできますか?**

A:いいえ。同じスケール セット内でエフェメラル OS ディスクと永続 OS ディスクのインスタンスを混在させることはできません。 

**Q:Powershell または CLI を使用してエフェメラル OS ディスクを作成できますか?**

A:はい。REST、テンプレート、PowerShell、CLI を使用して、エフェメラル OS ディスクで VM を作成できます。

**Q:エフェメラル OS ディスクでサポートされていない機能は何ですか?**

A:エフェメラル ディスクでサポートされていない機能は次のとおりです。
- VM イメージのキャプチャ
- ディスクのスナップショット 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OS ディスクのスワップ 
