---
title: "Azure での Windows VM の可用性管理 | Microsoft Docs"
description: "複数の仮想マシンを使って Azure の Windows アプリケーションの高い可用性を確保する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b170d942cd1c165db4cb5a563a2d149b13483d6b
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Azure での Windows 仮想マシンの可用性の管理 

複数の仮想マシンを設定および管理して、Azure の Windows アプリケーションの高い可用性を確保する方法について説明します。 [Linux 仮想マシンの可用性を管理する](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

クラシック デプロイメント モデルを使用しているときに、可用性セットを作成および使用する方法については、 [可用性セットの構成方法](classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページをご覧ください。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>次のステップ
仮想マシンの負荷分散の詳細については、 [仮想マシンの負荷分散](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

