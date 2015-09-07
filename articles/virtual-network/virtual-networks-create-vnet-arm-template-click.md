<properties
   pageTitle="ARM テンプレートを使用した仮想ネットワークの作成 | Microsoft Azure"
	description="ARM テンプレートを使用した仮想ネットワークの作成方法の説明 | リソース マネージャー"
	services="virtual-network"
	documentationCenter=""
	authors="telmosampaio"
	manager="carolz"
	editor=""
	tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/21/2015"
	ms.author="telmos"/>

# ARM テンプレートを使用した仮想ネットワークの作成

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

このドキュメントでは、リソース マネージャーのデプロイ モデルを使用して VNet を作成する方法を説明します。[Create a virtual network in the classic deployment model (従来のデプロイ モデルに基づいた仮想ネットワークを作成) ](virtual-networks-create-vnet-classic-pportal.md)することもできます。

GitHub から既存の ARM テンプレートをダウンロードして変更し、そのテンプレートを GitHub、PowerShell、および Azure CLI からデプロイする方法を説明します。

GitHub から直接 ARM テンプレートをデプロイして変更を加えない場合は、[deploy a template from github (Github からテンプレートをデプロイ)](#deploy-the-arm-template-by-using-click-to-deploy) にスキップしてください。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!---HONumber=August15_HO9-->