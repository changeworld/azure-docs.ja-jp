---
title: "Azure Resource Manager の仮想マシンに Key Vault を設定する | Microsoft Docs"
description: "Azure Resource Manager の仮想マシンと共に使用するために Key Vault を設定する方法"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: bc60d5659c80ad1245d7a34b4cd81c0ae73a5ba9


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager の仮想マシンの Key Vault を設定する

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager スタックでは、Key Vault のリソース プロバイダーにより提供されるリソースとしてシークレット/証明書がモデル化されます。 Azure Key Vault の詳細については、「 [Azure Key Vault とは](../key-vault/key-vault-whatis.md)

Key Vault を Azure Resource Manager 仮想マシンと共に使用するには、Key Vault の *EnabledForDeployment* プロパティを True に設定する必要があります。 この設定は、さまざまなクライアントで実行できます。

## <a name="use-cli-to-set-up-key-vault"></a>CLI を使用して Key Vault を設定する
コマンド ライン インターフェイス (CLI) を使用して Key Vault を作成する方法については、「 [CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)」を参照してください。

CLI の場合、デプロイ ポリシーを割り当てる前に、Key Vault を作成する必要があります。 この処理には、次のコマンドを使用できます。

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>テンプレートを使用して Key Vault を設定する
テンプレートを使用する場合、Key Vault リソースの `enabledForDeployment` プロパティを `true` に設定する必要があります。

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

テンプレートを使用して、Key Vault の作成時に構成できるその他のオプションについては、「 [Create a Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)」を参照してください。




<!--HONumber=Nov16_HO3-->


