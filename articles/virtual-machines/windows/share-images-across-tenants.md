---
title: Azure のテナント間でギャラリー イメージを共有する |Microsoft Docs
description: 共有イメージ ギャラリーを使用して、Azure テナント間で VM イメージを共有する方法について説明します。
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 56c0e906203a010a1356beae4b6e5d66e34cfc2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157917"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure テナント間でギャラリー VM イメージを共有する

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-powershell"></a>PowerShell を使用した VM の作成


アプリケーション ID、シークレット、およびテナント ID を使用して両方のテナントにログインします。 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

アプリの登録のアクセス許可があるリソース グループに VM を作成します。 この例の情報を自身のものに置き換えます。

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>次の手順

[Azure Portal](shared-images-portal.md) を使用して、共有イメージ ギャラリー リソースも作成できます。