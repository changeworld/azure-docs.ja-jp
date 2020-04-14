---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297894"
---
Azure Data Explorer は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、データの暗号化に使用する目的で、カスタマー マネージド キーを提供できます。 

顧客が管理するキーは [Azure Key Vault](/azure/key-vault/key-vault-overview) に格納する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Data Explorer クラスターとキー コンテナーは同じリージョンに存在している必要があります。ただし、サブスクリプションは異なっていてもかまいません。 カスタマー マネージド キーの詳細については、[カスタマー マネージド キーと Azure Key Vault](/azure/storage/common/storage-service-encryption) に関する記事を参照してください。 

この記事では、カスタマー マネージド キーを構成する方法について説明します。

## <a name="configure-azure-key-vault"></a>Azure Key Vault を構成する

Azure Data Explorer でカスタマー マネージド キーを構成するには、[キー コンテナーの 2 つのプロパティを設定](/azure/key-vault/key-vault-ovw-soft-delete)する必要があります。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 これらのプロパティは、既定では有効になっていません。 これらのプロパティを有効にするには、新規または既存のキー コンテナーに対して、**論理的な削除の有効化**および**消去保護の有効化**を [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) または [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) で実行します。 サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Key Vault のキー](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)」を参照してください。

> [!NOTE]
> カスタマー マネージド キーを使用したデータ暗号化は、[リーダー クラスターとフォロワー クラスター](/azure/data-explorer/follower)ではサポートされていません。 

## <a name="assign-an-identity-to-the-cluster"></a>クラスターに ID を割り当てる

お使いのクラスターでカスタマー マネージド キーを有効にするには、まず、そのクラスターにシステム割り当てマネージド ID を割り当てます。 このマネージド ID を使って、キー コンテナーへのアクセス許可をクラスターに付与します。 システム割り当てマネージド ID の構成については、[マネージド ID](/azure/data-explorer/managed-identities) に関するページを参照してください。