---
title: "Linux VM の可用性管理 | Microsoft Docs"
description: "複数の仮想マシンを使って Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c18781ba99276e0aceee0d1d6f4fdf80568fca60
ms.openlocfilehash: 3c305ed5b1ba35088983050f4a5c0ac56ab3888b


---

# <a name="manage-the-availability-of-virtual-machines"></a>仮想マシンの可用性管理
複数の仮想マシンを設定および管理して、Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。 [Windows 仮想マシンの可用性を管理する](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)こともできます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Resource Manager デプロイメント モデルで CLI を使用して可用性セットを作成する方法の詳細については、「[Azure 可用性セット: 可用性セットを管理するコマンド](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)」を参照してください。

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>次のステップ
仮想マシンの負荷分散の詳細については、 [仮想マシンの負荷分散](virtual-machines-linux-load-balance.md)に関するページをご覧ください。




<!--HONumber=Nov16_HO3-->


