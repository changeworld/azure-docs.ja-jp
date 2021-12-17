---
title: Azure Key Vault のファイアウォールと仮想ネットワークを構成する - Azure Key Vault
description: キー コンテナーのネットワーク設定について学習する
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3e9332706c3bcc9d4f4cabd20d10fa099271d69
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285327"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault のファイアウォールと仮想ネットワークを構成する

このドキュメントでは、Key Vault のファイアウォールのさまざまな構成について詳しく説明します。 これらの設定を構成する詳細な手順に従うには、[こちら](how-to-azure-key-vault-network-security.md)のガイドに従ってください。 

詳細については、「[Azure Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)」を参照してください。

## <a name="firewall-settings"></a>ファイアウォール設定

このセクションでは、Azure Key Vault ファイアウォールを構成するためのさまざまな方法について説明します。

### <a name="key-vault-firewall-disabled-default"></a>Key Vault ファイアウォールを無効にする (既定値)

既定では、新しいキー コンテナーを作成するとき、Azure Key Vault ファイアウォールは無効になります。 すべてのアプリケーションおよび Azure サービスから、キー コンテナーにアクセスし、キー コンテナーに要求を送信することができます。 なお、この構成は、お客様のキー コンテナー上で任意のユーザーが操作を行えるということを意味するものではありません。 キー コンテナーは、Azure Active Directory 認証およびアクセス ポリシーのアクセス許可を必要とすることで、キー コンテナーに格納されているシークレット、キー、および証明書に引き続き制限されます キー コンテナー認証の詳細については、[こちら](./authentication.md)のキー コンテナー認証の基礎に関するドキュメントを参照してください。 詳細については、「[ファイアウォールの向こう側にある Azure Key Vault へのアクセス](./access-behind-firewall.md)」を参照してください。

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault ファイアウォールを有効にする (信頼されたサービスのみ)

Key Vault ファイアウォールを有効にすると、[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\) に対するオプションが表示されます。 信頼されたサービスのリストには、単一の Azure サービスがすべてが含まれるわけではありません。 たとえば、Azure DevOps は信頼されたサービスのリストに掲載されていません。 **これは、信頼されたサービスのリストに表示されないサービスが信頼されていない、またはセキュリティで保護されていないということを意味するものではありません。** 信頼されたサービスのリストには、サービス上で実行されるすべてのコードを Microsoft が制御するサービスが含まれます。 Azure DevOps などの Azure サービス内ではユーザーがカスタム コードを記述できるため、Microsoft はサービスの包括的承認を作成するオプションを提供していません。 さらに、サービスが、信頼されたサービスのリストに掲載されていたからといって、それがすべてのシナリオに対して許可されるわけではありません。 

使おうとしているサービスが信頼されたサービスのリストにあるかどうかを判断するには、[こちら](./overview-vnet-service-endpoints.md#trusted-services)のドキュメントを参照してください。
攻略ガイドについては、こちらの[ポータル、Azure CLI、PowerShell](how-to-azure-key-vault-network-security.md) 向けの手順に従ってください。

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault ファイアウォールを有効にする (IPv4 アドレスおよび範囲 - 静的 IP)

Key Vault ファイアウォールを経由してキー コンテナーにアクセスする特定のサービスを承認したい場合は、その IP アドレスをキー コンテナー ファイアウォール許可リストに追加します。 この構成は、静的 IP アドレスまたは既知の範囲を使用するサービスに最適です。 この場合、CIDR 範囲については、1000 までという制限があります。

Web アプリやロジック アプリなどの Azure リソースの IP アドレスまたは範囲を許可するには、次の手順を行います。

1. Azure Portal にログインする
1. リソース (サービスの特定のインスタンス) を選択する
1. [設定] の下にある [プロパティ] ブレードをクリックする
1. [IP アドレス] フィールドを探す。
1. この値または範囲をコピーし、キー コンテナー ファイアウォール許可リストに入力する。

Key Vault ファイアウォールを介して Azure サービス全体を許可するには、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)の Azure 用の公式に文書化されたデータセンター IP アドレスのリストを使用してください。 目的のリージョンで必要なサービスに関連付けられている IP アドレスを見つけ、それらの IP アドレスを上記の手順を使用してキー コンテナー ファイアウォールに追加します。

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault ファイアウォールを有効にする (仮想ネットワーク - 動的 IP)

キー コンテナーを介して仮想マシンなどの Azure リソースを許可しようとしている場合は、静的 IP アドレスを使用できない可能性があり、さらに Azure Virtual Machines の一部の IP アドレスにキー コンテナーへのアクセスを許可したくないこともあります。

この場合は、仮想ネットワーク内にリソースを作成し、特定の仮想ネットワークおよびサブネットからのトラフィックがご利用のキー コンテナーにアクセスするのを許可する必要があります。 このためには、次の手順を実行します。

1. Azure Portal にログインする
1. 構成するキー コンテナーを選択する
1. [ネットワーク] タブを選択する
1. [+ 既存の仮想ネットワークを追加] を選択する
1. キー コンテナー ファイアウォールを介して許可する仮想ネットワークおよびサブネットを選択する。

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault ファイアウォールを有効にする (プライベート リンク)

ご利用のキー コンテナー上でプライベート リンク接続を構成する方法については、[こちら](./private-link-service.md)のドキュメントを参照してください。

> [!IMPORTANT]
> ファイアウォール ルールを有効にした後は、要求が許可された仮想ネットワークまたは IPv4 アドレス範囲から送信された場合にのみ、ユーザーは Key Vault [データ プレーン](security-features.md#privileged-access)の操作を実行できます。 これは、Azure portal から Key Vault にアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる Key Vault 選択機能にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。

> [!NOTE]
> 構成に関する次の制限事項に注意してください。
> * 最大で 200 個の仮想ネットワーク規則と 1000 個の IPv4 ルールを指定できます。 
> * IP ネットワーク ルールは、パブリック IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、**10.** 、**172.16-31**、および **192.168.** で始まるアドレスが含まれます。 
> * 現時点でサポートされているのは、IPv4 アドレスのみです。

## <a name="references"></a>References
* ARM テンプレート リファレンス: [Azure Key Vault ARM テンプレート リファレンス](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI コマンド: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell コマンドレット:[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次のステップ

* [Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)
* [Azure Key Vault のセキュリティの概要](security-features.md)