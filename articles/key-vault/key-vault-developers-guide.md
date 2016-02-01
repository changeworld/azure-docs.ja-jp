<properties
   pageTitle="Azure Key Vault 開発者ガイド | Microsoft Azure"
   description="開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/19/2016"
   ms.author="bruceper" />

# Azure Key Vault 開発者ガイド

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。Key Vault は、複数の種類のキーとアルゴリズムをサポートし、重要なキーに対してハードウェア セキュリティ モジュール (HSM) と連携させて使用できます。さらに、Key Vault を使用して、サイズが制限されたオクテット オブジェクトであるシークレットを、特定のセマンティクスを使用せずに安全に保存できます。オブジェクトに対するアクセス制御は、種類別に管理されます。

承認された場合、以下を実行できます。

- 暗号化キーを、[作成](https://msdn.microsoft.com/library/azure/dn903634.aspx)、[インポート](https://msdn.microsoft.com/library/azure/dn903626.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn903616.aspx)、[削除](https://msdn.microsoft.com/library/azure/dn903611.aspx)などの操作を使用して管理する

- シークレットを、[取得](https://msdn.microsoft.com/library/azure/dn903633.aspx)、[更新](https://msdn.microsoft.com/library/azure/dn986818.aspx)、[削除](https://msdn.microsoft.com/library/azure/dn903613.aspx)などの操作を使用して管理する

- 暗号化キーを、[署名](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[確認](https://msdn.microsoft.com/library/azure/dn878082.aspx)、[WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx)、[暗号化](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[暗号化解除](https://msdn.microsoft.com/library/azure/dn878097.aspx)操作で使用する

Key Vault に対する操作は、Azure Active Directory によって認証され、許可されます。

## Key Vault のプログラミング

プログラマー向けの Key Vault 管理システムは、REST を基盤とするさまざまなインターフェイスで構成されます。[Key Vault REST API リファレンス](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[.NET SDK ドキュメント](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Node.js SDK ドキュメント](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[.NET SDK パッケージ](https://azure.microsoft.com/ja-JP/documentation/api/)|[Node.js SDK パッケージ](https://www.npmjs.com/package/azure-keyvault)|

## Key Vault の管理

Azure Key Vault コンテナー (資格情報コンテナー) は、次の記事の説明に従って、REST、PowerShell または CLI を使用して管理できます。

- [REST を使用した Key Vault の作成と管理](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [PowerShell を使用した Key Vault の作成と管理](key-vault-get-started.md)
- [CLI を使用した Key Vault の管理](key-vault-manage-with-cli.md)


## 方法

次の記事では、タスクの具体的なガイダンスを提供します。

- [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)

## 例

- 次のダウンロードには、サンプル アプリケーション (HelloKeyVault) と、Azure Web サービスの例の両方が含まれています。[Azure Key Vault のコード サンプル](http://www.microsoft.com/download/details.aspx?id=45343)
- このチュートリアルを使用すると、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。[Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md)

## 対応ライブラリ

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0): 識別子からキーを検索し、キーを使用して操作を実行する、IKey および IKeyResolver インターフェイスを提供します。

- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0): Azure Key Vault の拡張機能を提供します。

<!----HONumber=AcomDC_0121_2016-->