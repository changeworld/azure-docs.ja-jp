---
title: Azure Key Vault の HSM保護キーを生成し、転送する方法 - Azure Key Vault | Microsoft Docs
description: この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461745"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>HSM で保護されたキーを Key Vault にインポートする (プレビュー)

> [!NOTE]
> この機能はプレビュー段階であり、**米国東部 2 EUAP** リージョンと **米国中部 EUAP** リージョンでのみ利用できます。 

Azure Key Vault を使用するときの確実性をさらに高めるには、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。 Azure Key Vault では、HSM の nCipher nShield ファミリ (FIPS 140-2 レベル 2 で検証済み) を使用してキーが保護されます。

このトピックの情報は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。

> [!NOTE]
> この機能は Azure China 21Vianet では使用できません。 
> 
> このインポート方法は、[サポートされている HSM](#supported-hsms) に対してのみ使用できます。 

Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](key-vault-overview.md)  HSM で保護されたキーの Key Vault 作成を含む入門チュートリアルについては、「[Azure Key Vault とは](key-vault-overview.md)」を参照してください。

## <a name="overview"></a>概要

* キー コンテナーでキー (キー交換キーまたは KEK と呼ばれる) を生成します。 これは、キー操作が "インポート" だけの RSA-HSM キーである必要があります。 RSA-HSM キーは、Key Vault Premium SKU でのみサポートされます。
* KEK の公開キーを .pem ファイルとしてダウンロードします
* オンプレミスの HSM に接続されているオフライン ワークステーションに、KEK 公開キーを転送します。
* オフライン ワークステーションで、HSM ベンダーから提供されている BYOK ツールを使用して BYOK ファイルを作成します。 
* ターゲット キーは KEK で暗号化され、Azure Key Vault HSM に転送されるまで暗号化が維持されます。 暗号化されたキーだけが、オンプレミスの HSM から外に出て行きます。
* Azure Key Vault HSM 内で生成された KEK は、エクスポートできません。 HSM により、Key Vault HSM の外部では平文の KEK が存在できないよう強制されます。
* KEK は、ターゲット キーがインポートされるのと同じキー コンテナー内に存在する必要があります。
* BYOK ファイルが Key Vault にアップロードされると、Key Vault HSM により、KEK 秘密キーを使用してターゲット キー マテリアルが解読され、HSM キーとしてインポートされます。 この操作は完全に Key Vault HSM 内で行われ、ターゲット キーは常に HSM の保護境界内に留まっています。

## <a name="prerequisites"></a>前提条件

Azure Key Vault の Bring Your Own Key (BYOK) の前提条件の一覧については、次の表を参照してください。

| 要件 | 詳細情報 |
| --- | --- |
| Azure のサブスクリプション |Azure Key Vault を作成するには、Azure サブスクリプションが必要です: [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/) |
| HSM で保護されたキーをインポートするためのキー コンテナー (Premium SKU) |Azure Key Vault のサービス層と機能に関する詳細については、 [Azure Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/) Web サイトを参照してください。 |
| サポートされている HSM の一覧に含まれる HSM と、HSM ベンダーから提供されている BYOK ツールおよび説明 | ハードウェア セキュリティ モジュールにアクセスでき、HSM の基本操作に関する知識が必要です。 「[サポートされている HSM](#supported-hsms)」を参照してください。 |
| Azure CLI バージョン 2.1.0 以降 | 詳しくは、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」をご覧ください。|

## <a name="supported-hsms"></a>サポートされている HSM

|HSM ベンダーの名前|サポートされている HSM モデル|追加情報|
|---|---|---|
|Thales|SafeNet Luna HSM 7 ファミリ (ファームウェア バージョン 7.3 以降)| [SafeNet Luna の BYOK ツールとドキュメント](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> HSM で保護されたキーを HSM の nCipher nShield ファミリからインポートするには、[従来の BYOK の手順を使用します](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>キーを生成し、Azure Key Vault HSM に転送する

次の手順でキーを生成し、Azure Key Vault HSM に転送します。

* [ステップ 1:KEK を生成する](#step-1-generate-a-kek)
* [手順 2:KEK 公開キーをダウンロードする](#step-2-download-kek-public-key)
* [ステップ 3:キーを生成して転送用に準備をする](#step-3-generate-and-prepare-your-key-for-transfer)
* [手順 4:キーを Azure Key Vault に転送する](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>手順 1:KEK を生成する

KEK (キー交換キー) は、Key Vault の HSM で生成される RSA キーです。 このキーは、インポートするキー (ターゲット キー) を暗号化するために使用されます。

KEK には次の条件があります。
1. **RSA HSM** キーであること (2048 ビット、3072 ビット、4096 ビット)
2. ターゲット キーをインポートするのと同じキー コンテナーで生成されていること
3. **インポート**のための許可されたキー操作セットで作成されていること

インポートのためのキー操作セットで KEK を作成するには、[az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) コマンドを使用します。 次のコマンドから返されるキー識別子 "kid" を記録しておきます。 [手順 3](#step-3-generate-and-prepare-your-key-for-transfer) で必要になります。


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>手順 2:KEK 公開キーをダウンロードする

KEK 公開キーを .pem ファイルにダウンロードするには、[az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) を使用します。 インポートするターゲット キーは、KEK 公開キーを使用して暗号化されています。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK.publickey.pem ファイルをオフライン ワークステーションに転送します。 このファイルは、次の手順の間に必要です。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>手順 3:キーを生成して転送用に準備をする

BYOK ツールをダウンロードしてインストールする方法については、HSM ベンダーのドキュメントを参照してください。 HSM ベンダーの指示に従って、ターゲット キーを生成した後、キー転送パッケージ (BYOK ファイル) を作成します。 BYOK ツールでは、[手順 1](#step-1-generate-a-kek) のキー識別子と、[手順 2](#step-2-download-kek-public-key) でダウンロードした と KEKforBYOK.publickey.pem ファイルを使用して、暗号化されたターゲット キーを BYOK ファイルに生成します。

BYOK ファイルを接続されたワークステーションに転送します。

> [!NOTE] 
> ターゲット キーは、サイズが 2048 ビット、3072 ビット、または 4096 ビットの RSA キーである必要があります。 現時点では、楕円曲線キーのインポートはサポートされていません。
> <br/><strong>既知の問題:</strong>SafeNet Luna HSM から RSA 4K ターゲット キーをインポートすると失敗します。 問題が解決されたら、このドキュメントを更新します。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>手順 4:キーを Azure Key Vault に転送する

この最後の手順では、キー転送パッケージ (BYOK ファイル) を切断されたワークステーションからインターネットに接続されたワークステーションに転送した後、[az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) コマンドを使用して BYOK ファイルを Azure Key Vault HSM にアップロードし、キーのインポートを完了します。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

アップロードが成功すると、インポートしたキーのプロパティが表示されます。

## <a name="next-steps"></a>次のステップ

これでこの HSM 保護キーを Key Vault で使用できます。 詳しくは、この価格と機能の[比較](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。
