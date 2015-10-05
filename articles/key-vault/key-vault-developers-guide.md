<properties
   pageTitle="Azure Key Vault 開発者ガイド | Microsoft Azure"
   description="開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Azure Key Vault 開発者ガイド

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。Key Vault は、複数の種類のキーとアルゴリズムをサポートし、重要な顧客キーに対してハードウェア セキュリティ モジュール (HSM) と連携させて使用できます。さらに、Key Vault を使用して、サイズが制限されたオクテット オブジェクトであるシークレットを、特定のセマンティクスを使用せずに安全に保存できます。キーとシークレットを混在させて Key Vault に保存できます。オブジェクトに対するアクセス制御は、種類別に管理されます。

承認されたユーザーは、以下を実行できます。

- 暗号化キーを、[作成](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[インポート](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[削除](https://msdn.microsoft.com/library/azure/dn903611.aspx)などの操作を使用して管理する

- シークレットを、[取得](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn986818.aspx)  [削除] などの操作を使用して管理する

- 暗号化キーを、[署名](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[確認](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx)、[暗号化](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[暗号化解除](https://msdn.microsoft.com/library/azure/dn878097.aspx)操作で使用する

Key Vault に対する操作は、Azure Active Directory によって認証され、許可されます。

## Key Vault のプログラミング

プログラマー向けの Key Vault 管理システムは、REST を基盤とするさまざまなインターフェイスで構成されます。

### REST ()

REST API は、Key Vault とのすべてのプログラムによる対話のベースです。

Key Vault には、「[Key Vault REST API リファレンス](https://msdn.microsoft.com/library/azure/dn903609.aspx)」で説明する独自の REST エンドポイントがあります。

### .NET

.NET API は、C# プログラミング モデルによる実装を、REST エンドポイントとの直接的な対話なしで実現できるラッパー セットです。「[Azure Key Vault .NET クライアント API リファレンス](https://msdn.microsoft.com/library/azure/dn903301.aspx)」を参照してください。

### Node.js

Node.js API は、JavaScript プログラミング モデルによる実装を、REST エンドポイントとの直接的な対話なしで実現できるラッパー セットです。「[Microsoft Azure SDK for Node.js - Key Vault Management](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)」を参照してください。

## PowerShell と CLI による Key Vault の管理

Azure Key Vault のキーとシークレットは、次の記事の説明に従って、PowerShell と CLI を使用して管理することもできます。

- [PowerShell を使用した Key Vault の作成と管理](key-vault-get-started.md)
- [CLI を使用した Key Vault の管理](key-vault-manage-with-cli.md)
- [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)
- [キーとシークレットについて](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## 関連項目

- [Azure Key Vault のコード サンプル](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->