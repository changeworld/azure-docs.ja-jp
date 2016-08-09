<properties
	pageTitle="Azure Resource Manager の仮想マシンに Key Vault を設定する | Microsoft Azure"
	description="Azure Resource Manager の仮想マシンと共に使用するために Key Vault を設定する方法"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rothja"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Azure Resource Manager の仮想マシンの Key Vault を設定する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル

Azure Resource Manager スタックでは、Key Vault のリソース プロバイダーにより提供されるリソースとしてシークレット/証明書がモデル化されます。Azure Key Vault の詳細については、「[Azure Key Vault とは](../key-vault/key-vault-whatis.md)」をご覧ください。

Key Vault を Azure Resource Manager 仮想マシンと共に使用するには、Key Vault の *EnabledForDeployment* プロパティを True に設定する必要があります。この設定は、さまざまなクライアントで実行できます。

## CLI を使用して Key Vault を設定する
コマンド ライン インターフェイス (CLI) を使用して Key Vault を作成する方法については、「[CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)」を参照してください。

CLI の場合、デプロイ ポリシーを割り当てる前に、Key Vault を作成する必要があります。この処理には、次のコマンドを使用できます。

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## テンプレートを使用して Key Vault を設定する
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

テンプレートを使用して、Key Vault の作成時に構成できるその他のオプションについては、「[Create a key vault (Key Vault の作成)](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)」を参照してください。

<!---HONumber=AcomDC_0622_2016-->
