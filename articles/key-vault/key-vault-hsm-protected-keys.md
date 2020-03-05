---
title: Azure Key Vault の HSM保護キーを生成し、転送する方法 - Azure Key Vault | Microsoft Docs
description: この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: d7f9527aa5aa3353dc9087f4bcc5f3a5fb241637
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184555"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM で保護されたキーを Key Vault にインポートする

Azure Key Vault の使用時にさらに安心感を高める場合、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。 Azure Key Vault では、HSM の nCipher nShield ファミリ (FIPS 140-2 レベル 2 で検証済み) を使用してキーが保護されます。

この機能は Azure China 21Vianet では使用できません。

> [!NOTE]
> Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](key-vault-overview.md)  
> HSM で保護されたキーの Key Vault 作成を含む入門チュートリアルについては、「[Azure Key Vault とは](key-vault-overview.md)」を参照してください。

## <a name="supported-hsms"></a>サポートされている HSM

HSM で保護されたキーの Key Vault への転送は、使用する HSM に応じて 2 つの異なる方法でサポートされます。 以下の表を使用して、HSM で、独自の HSM で保護されたキーを生成してから、Azure Key Vault で使用するためにそれらを転送する場合に使用する必要がある方法を判別します。 

|HSM のベンダー名|サポートされている HSM モデル|サポートされている HSM キーの転送方法|
|---|---|---|
|Thales|<ul><li>ファームウェアのバージョンが 7.3 以降の SafeNet Luna HSM 7 ファミリ</li></ul>| [新しい BYOK の方法を使用する (プレビュー)](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>HSM の nShield ファミリ</li></ul>|[従来の BYOK の方法を使用する](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>次のステップ

[Key Vault のベスト プラクティス](key-vault-best-practices.md)に従って、キーのセキュリティ、持続性および監視を確実にします。
