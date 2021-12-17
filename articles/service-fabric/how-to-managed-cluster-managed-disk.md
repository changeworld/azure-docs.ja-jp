---
title: Service Fabric マネージド クラスター ノードでのマネージド ディスクの種類を選択する
description: Service Fabric マネージド クラスター ノードのマネージド ディスク タイプを選択し、ARM テンプレートで構成する方法を学習します。
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: d0113ff161c000480f0e2f233f9acf6851f9d222
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130068857"
---
# <a name="select-managed-disk-types-for-service-fabric-managed-cluster-nodes"></a>Service Fabric マネージド クラスター ノードでのマネージド ディスクの種類を選択する

Azure Service Fabric のマネージド クラスターでは、リライアブル コレクションやアクターなどのシナリオで、アプリケーション データを含むすべてのストレージ ニーズにマネージド ディスクが使用されます。 Azure マネージド ディスクは、Azure によって管理されて Azure Virtual Machines で使用されるブロックレベルの記憶域ボリュームです。 マネージド ディスクは、オンプレミス サーバーの物理ディスクと似ていますが、仮想化されています。 マネージド ディスクを使用した場合、ユーザーがすべきことは、サイズと種類を指定してディスクをプロビジョニングするだけです。 ディスクのプロビジョニング後、残りの作業は Azure によって行われます。 マネージド ディスクの詳細については、「[Azure マネージド ディスクの概要](../virtual-machines/managed-disks-overview.md)」をご覧ください。

## <a name="managed-disk-types"></a>マネージド ディスクの種類

Azure Service Fabric マネージド クラスターでは、次のマネージド ディスクの種類がサポートされます。
* Standard HDD
    * Standard HDD のローカル冗長ストレージ。 バックアップ用、重要でない、頻度の低いアクセスに最適です。 
* Standard SSD の *既定値*
    * Standard SSD のローカル冗長ストレージ。 Web サーバー、使用頻度の低いエンタープライズ アプリケーション、Dev/Test に最適です。
* Premium SSD *特定の VM サイズと互換性があります* 詳細については、[[Premium SSD]](../virtual-machines/disks-types.md#premium-ssds) を参照してください
    * Premium SSD ローカル冗長ストレージ。 実稼働およびパフォーマンスが要求されるワークロードに最適です。

>[!NOTE]
> VM サイズに関連付けられている一時ディスクは、Service Fabric またはアプリケーション関連のデータの格納には使用 *されません*

## <a name="specifying-a-service-fabric-managed-cluster-disk-type"></a>Service Fabric マネージド クラスター ディスクの種類の指定

Service Fabric マネージド クラスター ディスクの種類を指定するには、マネージド クラスター リソース定義に次の値を含める必要があります。

* 値 **dataDiskType** プロパティ。ノードに使用するマネージド ディスクの種類を指定します。

次のいずれかの値になります。
* "Standard_LRS"
* "StandardSSD_LRS"
* "Premium_LRS"
>[!NOTE]
> すべてのマネージド ディスクの種類がすべての VM サイズで使用できるわけではありません。詳細については、「[Azure で利用できるディスクの種類](../virtual-machines/disks-types.md)」を参照してください

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "dataDiskType": "StandardSSD_LRS"
}
```

次の仕様を含むサンプル テンプレートが利用可能です: [Service Fabric マネージド クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)
