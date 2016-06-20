<properties
	pageTitle="Azure Resource Manager の Virtual Machines の Key Vault を設定する | Microsoft Azure"
	description="Azure Resource Manager Virtual Machines と共に使用するために Key Vault を設定する方法"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="singhkay"/>

# Azure Resource Manager の Virtual Machines の Key Vault を設定する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル

Azure Resource Manager スタックでは、Key Vault リソース プロバイダーにより提供されるリソースとしてシークレット/証明書がモデル化されます。Key Vaults の詳細については、「[Azure Key Vault とは](../key-vault/key-vault-whatis.md)」をご覧ください。

## セットアップ
Key Vault を Azure Resource Manager Virtual Machines と共に使用するには、Key Vault の *EnabledForDeployment* プロパティを True に設定する必要があります。この設定は、下の画像のように、さまざまなクライアントで実行できます。

## PowerShell
PowerShell を使用して Key Vault を作成する方法については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md#vault)」をご覧ください。

新しい Key Vault の場合は、次の PowerShell コマンドレットを使用することができます。

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

既存の Key Vault の場合は、次の PowerShell コマンドレットを使用することができます。

	Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## CLI
CLI を利用して Key Vault を作成する方法については、「[CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)」をご覧ください。

CLI の場合、最初に Key Vault を作成し、それからデプロイ ポリシーを有効にする必要があります。この操作は次のコマンドで実行できます。

	azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## テンプレート
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

テンプレートで Key Vault を作成するときに構成できるその他のオプションについては、[ここ](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)で参照してください。

<!---HONumber=AcomDC_0608_2016-->