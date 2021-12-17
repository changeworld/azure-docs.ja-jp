---
title: マネージド データ ディスクを使用して Service Fabric ノード タイプをデプロイする
description: 接続されたマネージド データ ディスクを使用して Service Fabric ノード タイプを作成してデプロイする方法について説明します
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 17ec57ca04defa61080ebb5a67f879c737ea16fa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460395"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>マネージド データ ディスクを使用して Azure Service Fabric クラスター ノード タイプをデプロイする (プレビュー)

>[!NOTE]
> マネージド データ ディスクのサポートは、現在はまだプレビュー段階です。運用環境のワークロードには使用しないでください。


Service Fabric ノード タイプでは既定として、データ ストレージの基になる仮想マシン スケール セット内の各 VM 上の一時ディスクが使用されます。 ただし、一時ディスクは永続的ではなく、一時ディスクのサイズは特定の VM SKU に拘束されるため、一部のシナリオでは制限が厳しすぎる可能性があります。 Azure Managed Disksでは、VM SKU とは別に、あるノード タイプに使用されるサイズとパフォーマンスをお客様が指定できる永続データ ディスクを利用できます。 次のドキュメントでは、Service Fabric のネイティブ サポートを使用して、既定のデータ パスとして Azure Managed Disks を構成して使用する手順を示します。 Service Fabric によって、ノード タイプの作成時に Azure Managed Disks が自動的に構成され、VM または仮想マシン スケール セットが再イメージ化される状況が処理されます。

* マネージド データ ディスクに必要な最小ディスク サイズは 50 GB です。
* 複数のマネージド データ ディスクが接続されているシナリオでは、お客様が独自にデータ ディスクを管理する必要があります。

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Service Fabric でマネージド データ ディスクを使用するための仮想マシン スケール セットの構成
あるノード タイプで Azure Managed Disks を使用するには、基になる仮想マシン スケール セット リソースを次の方法で構成します。

* 仮想マシン スケール セットのテンプレートのデータ ディスクのセクションにマネージド ディスクを追加します。 
* Service Fabric 拡張機能を次の設定で更新します。 
    * Windows の場合 - **useManagedDataDisk: true** および **dataPath: 'K:\\\\SvcFab'** 。  ドライブ文字 "K" は 1 つの表記で、仮想マシン スケール セット SKU に存在するすべてのドライブ文字よりもアルファベット順で大きい任意のドライブ文字を指定できます。
    * Linux の場合 - **useManagedDataDisk:true** および **dataPath: '\mnt\sfdataroot'** 。

>[!NOTE]
> Linux Service Fabric クラスター用のマネージド データ ディスクのサポートは、現在使用できません。

Service Fabric 拡張機能の Azure Resource Manager テンプレート
```json
{
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": false,
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
                            "dataPath": "K:\\\\SvcFab",
                            "useManagedDataDisk": true,
                            "durabilityLevel": "Bronze",
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            },
                            "systemLogUploadSettings": {
                                "Enabled": true
                            },
                        },
                        "typeHandlerVersion": "1.1"
                    }
                },
            ]
        },
        "storageProfile": 
        {
            "datadisks": [
                {
                    "lun": "1",
                    "createOption": "empty",
                    "diskSizeGB": "100",
                    "managedDisk": { "storageAccountType": "Standard_LRS" }
                }
            ]
        }
    }
}
```

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Service Fabric ノード タイプのためのマネージド データ ディスクの使用に移行する
* すべての移行シナリオで、上で指定したマネージド データ ディスクを使用する新しいノード タイプを追加する必要があります。
* 新しいノード タイプが追加された後、ワークロードを新しいノード タイプに移行します。
* リソースのデプロイが完了したら、元のクラスターから削除したいノード タイプのノードを無効にしていくことができます。

## <a name="next-steps"></a>次の手順 
* [Service Fabric の概要](service-fabric-reliable-services-introduction.md)
* [ノード タイプと仮想マシン スケール セット](service-fabric-cluster-nodetypes.md)
* [Service Fabric の容量計画](service-fabric-best-practices-capacity-scaling.md)
