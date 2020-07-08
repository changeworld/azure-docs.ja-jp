---
title: サブネットを Azure NetApp Files に委任する | Microsoft Docs
description: サブネットを Azure NetApp Files に委任する方法を説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/04/2020
ms.author: b-juche
ms.openlocfilehash: 713a72b0a406d2038d56dc6fcc41e169d02c54eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483620"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>サブネットを Azure NetApp Files に委任する 

サブネットを Azure NetApp Files に委任する必要があります。   ボリュームを作成する際は、委任されたサブネットを指定する必要があります。

## <a name="considerations"></a>考慮事項
* 新しいサブネットを作成するウィザードでは、既定で /24 ネットワーク マスクになっており、251 個の使用可能な IP アドレスが提供されます。 /28 ネットワーク マスクを使用すると、16 個の使用可能な IP アドレスが提供され、このサービスにはそれで十分です。
* 各 Azure Virtual Network (VNet) で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
   Azure では、VNet 内に複数の委任されたサブネットを作成できます。  ただし、委任されたサブネットを複数使用している場合、新しいボリュームを作成しようとすると失敗します。  
   1 つの VNet には、委任されたサブネットを 1 つだけ含めることができます。 NetApp アカウントでは、複数の Vnet にボリュームを配置でき、それぞれに独自の委任されたサブネットがあります。  
* 委任されたサブネット内のネットワーク セキュリティ グループまたはサービス エンドポイントを指定することはできません。 そうした場合、サブネットの委任が失敗します。
* グローバルにピアリングされた仮想ネットワークからボリュームへのアクセスは、現在はサポートされていません。
* Azure NetApp Files に委任されたサブネットのアドレス プレフィックス (宛先) を使用して、VM サブネットに[ユーザー定義のカスタム ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)を作成することはサポートされていません。 これは VM の接続に影響を及ぼします。

## <a name="steps"></a>手順 
1.  Azure portal で **[仮想ネットワーク]** ブレードに移動し、Azure NetApp Files のために使用する仮想ネットワークを選択します。    

1. 仮想ネットワーク ブレードで **[サブネット]** を選択し、 **[+ サブネット]** ボタンをクリックします。 

1. [サブネットの追加] ページで以下の必須フィールドを指定して、Azure NetApp Files のために使用する新しいサブネットを作成します。
    * **Name**:サブネット名を指定します。
    * **アドレス範囲**:IP アドレス範囲を指定します。
    * **サブネットの委任**: **[Microsoft.NetApp/volumes]** を選択します。 

      ![サブネットの委任](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
[Azure NetApp Files のためのボリュームを作成する](azure-netapp-files-create-volumes.md)ときに、サブネットを作成して委任することもできます。 

## <a name="next-steps"></a>次のステップ  
* [Azure NetApp Files のボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Azure サービスの仮想ネットワーク統合について理解する](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


