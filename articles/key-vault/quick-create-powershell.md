---
title: Azure クイック スタート - PowerShell を使用して Key Vault との間でシークレットの設定と取得を行う | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 381cda9072e1433048611628c692fa72ede3dceb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022841"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>クイック スタート: PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う

Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](key-vault-overview.md)に関するページを参照してください。 このクイック スタートでは、PowerShell を使用してキー コンテナーを作成します。 次に、新しく作成したキー コンテナーにシークレットを格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.1.1 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```azurepowershell
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Key Vault の作成

次に、Key Vault を作成します。 この手順を実行するときは、いくつかの情報が必要になります。

このクイック スタート全体で Key Vault の名前として "Contoso KeyVault2" を使用していますが、一意の名前を使用する必要があります。

- **コンテナー名**: Contoso-Vault2。
- **リソース グループ名**: ContosoResourceGroup。
- **場所**: 米国東部。

```azurepowershell
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

* **コンテナーの名前**: この例では、これは **Contoso-Vault2** です。 この名前を他の Key Vault コマンドレットに使用できます。
* **Vault URI (コンテナー URI)**: この例では、これは https://contosokeyvault.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

資格情報コンテナーを作成した後は、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

![キー コンテナー作成コマンドの実行後の出力](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Key Vault へのシークレットの追加

シークレットをコンテナーに追加するには、いくつかの手順を実行する必要があります。 このケースでは、アプリケーションによって使用可能なパスワードを追加します。 パスワードは **ExamplePassword** と呼ばれ、値 '''**Pa$$w0rd**''' がその中に格納されます。

最初に、次のように入力して、Pa$$w0rd の値をセキュリティで保護された文字列に変換します。

```azurepowershell
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

その後、次の PowerShell コマンドを入力して、値 **Pa$$w0rd** を含む **ExamplePassword** という Key Vault にシークレットを作成します。

```azurepowershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurepowershell
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

これで、キー コンテナーを作成し、シークレットを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

 このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 他のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、Key Vault、およびすべての関連リソースを削除できます。

```azurepowershell
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Key Vault を作成してソフトウェア キーを格納しました。 Key Vault の詳細とアプリケーションでの使用方法については、Key Vault と連携する Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> マネージド サービス ID を使用する Web アプリケーションから、Key Vault からのシークレットを読み取る方法を学習するには、[キー コンテナーからシークレットを読み取るように Azure Web アプリを構成する](quick-create-net.md)チュートリアルに進んでください。
