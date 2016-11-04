---
title: Linux VM の可用性管理 | Microsoft Docs
description: 複数の仮想マシンを使って Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: cynthn

---
# 仮想マシンの可用性管理
複数の仮想マシンを設定および管理して、Azure の Linux アプリケーションの高い可用性を確保する方法について説明します。[Windows 仮想マシンの可用性を管理する](virtual-machines-windows-manage-availability.md)こともできます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Resource Manager デプロイメント モデルで CLI を使用して可用性セットを作成する方法の詳細については、「[Azure 可用性セット: 可用性セットを管理するコマンド](azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)」を参照してください。

[!INCLUDE [virtual-machines-common-manage-availability](../../includes/virtual-machines-common-manage-availability.md)]

## 次のステップ
仮想マシンの負荷分散の詳細については、[仮想マシンの負荷分散](virtual-machines-linux-load-balance.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0601_2016-->