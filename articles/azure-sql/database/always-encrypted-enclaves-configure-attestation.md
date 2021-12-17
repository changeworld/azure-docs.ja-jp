---
title: Azure Attestation を使用して Always Encrypted の構成証明を構成する
description: Azure SQL Database でセキュリティで保護されたエンクレーブが設定された Always Encrypted に対して Azure Attestation を構成します。
keywords: データの暗号化, sql 暗号化, データベース暗号化, 機密データ, Always Encrypted, セキュリティで保護されたエンクレーブ, SGX, 構成証明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6a27acf96b42a4a963b88e281fd692a91616b7e4
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727203"
---
# <a name="configure-attestation-for-always-encrypted-using-azure-attestation"></a>Azure Attestation を使用して Always Encrypted の構成証明を構成する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Microsoft Azure Attestation](../../attestation/overview.md) は、Intel Software Guard Extensions (Intel SGX) エンクレーブなどの高信頼実行環境 (TEE) を証明するためのソリューションです。 

Azure SQL Database での[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) に対して使用される Intel SGX エンクレーブを証明するために Azure Attestation を使用するには、次のことを行う必要があります。

1. [構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)を作成し、推奨される構成証明ポリシーを使用して構成します。

2. 構成証明の URL を特定し、アプリケーション管理者との間で共有します。

> [!NOTE]
> 構成証明の構成は、構成証明管理者が行います。 「[SGX エンクレーブと構成証明を構成する場合のロールと責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)」を参照してください。

## <a name="create-and-configure-an-attestation-provider"></a>構成証明プロバイダーを作成して構成する

[構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)は、[構成証明ポリシー](../../attestation/basic-concepts.md#attestation-request)に対して[構成証明要求](../../attestation/basic-concepts.md#attestation-request)を評価し、[構成証明トークン](../../attestation/basic-concepts.md#attestation-token)を発行する Azure Attestation のリソースです。 

構成証明ポリシーは、[要求規則の文法](../../attestation/claim-rule-grammar.md)を使用して指定します。

> [!IMPORTANT]
> 構成証明プロバイダーは、エンクレーブ内で実行されているコードを検証しない、Intel SGX エンクレーブの既定のポリシーを使用して作成されます。 Microsoft では、セキュリティで保護されたエンクレーブが設定された Always Encrypted に既定のポリシーを使用するのではなく、後述の推奨ポリシーを設定することを強くお勧めします。

Azure SQL Database の Always Encrypted に使用される Intel SGX エンクレーブを証明するには、次のポリシーをお勧めします。

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

上記のポリシーでは、次のことを確認します。

- Azure SQL Database 内のエンクレーブはデバッグに対応していません。 
  > エンクレーブは、デバッグが無効でも有効でも読み込むことができます。 デバッグ サポートは、開発者がエンクレーブで実行されているコードの問題を解決できるように設計されています。 実稼働システムでは、エンクレーブが提供する保護レベルを下げるようなコンテンツがエンクレーブに含まれていれば、デバッグによって管理者はそれを調べることができます。 悪意のある管理者がエンクレーブ マシンを乗っ取り、デバッグ サポートをオンにしようとした場合、構成証明に失敗するよう、方針としてはデバッグを無効にすることが推奨されます。 
- エンクレーブの製品 ID は、セキュリティで保護されたエンクレーブが設定された Always Encrypted に割り当てられた製品 ID に一致します。
  > エンクレーブにはそれぞれ、他のエンクレーブと区別するための一意の製品 ID が与えられます。 Always Encrypted エンクレーブに割り当てられた製品 ID は 4639 です。 
- ライブラリのセキュリティ バージョン番号 (SVN) は 0 より大きくなります。
  > この SVN によって、Microsoft はエンクレーブ コードで特定された潜在的なセキュリティ バグに対応できます。 セキュリティの問題が見つかり、修正された場合、Microsoft は、新しい SVN が付けられた (1 つ上の番号が付けられた) 新しいバージョンのエンクレーブをデプロイします。 上で推奨されているポリシーは、新しい SVN を反映する目的で更新されます。 推奨されるポリシーに合わせて自分のポリシーを更新することで、悪意のある管理者が古くて安全ではないエンクレーブを読み込もうとしたとき、構成証明を失敗させることができます。
- エンクレーブ内のライブラリが Microsoft 署名キーを使用して署名されている (x-ms-sgx-mrsigner 要求の値が署名キーのハッシュである)。
  > 構成証明の主な目標の 1 つは、エンクレーブ内で実行されるバイナリが、実行されるべきバイナリであることをクライアントに認識させることにあります。 構成証明ポリシーからは、この目的のために 2 つのメカニズムが提供されます。 1 つは **mrenclave** 要求です。これは、エンクレーブで実行されることになっているバイナリのハッシュです。 **mrenclave** の問題は、コードをほんの少し変えただけでもバイナリ ハッシュが変わることにあります。エンクレーブ内で実行されているコードの改訂が難しくなります。 そのため、**mrsigner** の使用を推奨しています。これは、エンクレーブ バイナリの署名に使用されるキーのハッシュです。 Microsoft がエンクレーブを改訂するとき、署名キーが変わらない限り、**mrsigner** も同じままです。 この方法で、顧客のアプリケーションを壊すことなく、更新後のバイナリをデプロイすることが可能になります。 

> [!IMPORTANT]
> Always Encrypted エンクレーブ バイナリへの署名に使用するキーのローテーションが必要になる場合がありますが、これはまれな事例であると想定されています。 新しいキーで署名されたエンクレーブ バイナリの新しいバージョンが Azure SQL Database に展開される前に、この記事は更新され、推奨される新しい構成証明ポリシーが提供されます。また、アプリケーションが中断なく機能し続けるようにするために、構成証明プロバイダーでポリシーを更新する方法も示されます。

構成証明プロバイダーを作成して構成証明ポリシーで構成する方法については、以下を参照してください。

- [クイック スタート: Azure portal を使用して Azure Attestation を設定する](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Azure portal で構成証明ポリシーを構成する場合は、[構成証明の種類] を [`SGX-IntelSDK`] に設定します。
- [クイック スタート:Azure PowerShell を使用して Azure Attestation を設定する](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Azure PowerShell で構成証明ポリシーを構成する場合は、`Tee` パラメーターを `SgxEnclave` に設定します。
- [クイックスタート: Azure CLI を使用して Azure Attestation を設定する](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Azure CLI で構成証明ポリシーを構成する場合は、`attestation-type` パラメーターを `SGX-IntelSDK` に設定します。


## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>構成証明ポリシーの構成証明 URL を確認する

構成証明ポリシーを構成したら、構成証明 URL を、Azure SQL Database のセキュア エンクレーブが設定された Always Encrypted を使用するアプリケーションの管理者と共有する必要があります。 構成証明 URL は、構成証明ポリシーを含む構成証明プロバイダーの `Attest URI` です。これは、`https://MyAttestationProvider.wus.attest.azure.net` のようになります。

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Azure portal を使用して構成証明 URL を確認する

構成証明プロバイダーの [概要] ペインで、`Attest URI` プロパティの値をクリップボードにコピーします。 

### <a name="use-powershell-to-determine-the-attestation-url"></a>PowerShell を使用して構成証明の URL を確認する

`Get-AzAttestation` コマンドレットを使用して、AttestURI を含む、構成証明プロバイダーのプロパティを取得します。

```powershell
Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName
```

詳細については、「[構成証明プロバイダーを作成して管理する](../../attestation/quickstart-powershell.md#create-and-manage-an-attestation-provider)」を参照してください。

## <a name="next-steps"></a>次の手順

- [セキュリティで保護されたエンクレーブが設定された Always Encrypted のキーを管理する](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)
