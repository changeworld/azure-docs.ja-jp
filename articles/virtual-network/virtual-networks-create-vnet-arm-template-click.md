---
title: ARM テンプレートを使用した仮想ネットワークの作成 | Microsoft Docs
description: ARM テンプレートを使用した仮想ネットワークの作成方法の説明 | リソース マネージャー
services: virtual-network
documentationcenter: ''
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# ARM テンプレートを使用した仮想ネットワークの作成
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

このドキュメントでは、リソース マネージャーのデプロイ モデルを使用して VNet を作成する方法を説明します。[クラシック デプロイメント モデルで仮想ネットワークを作成](virtual-networks-create-vnet-classic-pportal.md)することもできます。

GitHub から既存の ARM テンプレートをダウンロードして変更し、そのテンプレートを GitHub、PowerShell、および Azure CLI からデプロイする方法を説明します。

GitHub から直接 ARM テンプレートをデプロイするだけで、変更を加えない場合は、[Github からのテンプレートのデプロイ](#deploy-the-arm-template-by-using-click-to-deploy)に進んでください。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=AcomDC_0810_2016-->