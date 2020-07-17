---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372024"
---
### <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Cognitive Services リソースとキー コンテナーは、同じリージョンの同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)」をご覧ください。

新しい Cognitive Services リソースは、常に Microsoft のマネージド キーを使用して暗号化されます。 リソースを作成するときに、カスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは Azure Key Vault に格納され、キー コンテナーは、Cognitive Services リソースに関連付けられているマネージド ID に対してキーのアクセス許可を付与するアクセス ポリシーを使用して、プロビジョニングする必要があります。 マネージド ID は、CMK の価格レベルを使用してリソースを作成した後でのみ使用できます。

Cognitive Services の暗号化のために Azure Key Vault でカスタマー マネージド キーを使用する方法については、以下を参照してください。

- [Azure portal から Cognitive Services の暗号化用に Key Vault でカスタマー マネージド キーを構成する](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

カスタマー マネージド キーを有効にすると、システムによって割り当てられるマネージド ID (Azure AD の機能) も有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory に登録されます。 登録された後、マネージド ID には、カスタマー マネージド キーのセットアップ時に選択されたキー コンテナーへのアクセス権が付与されます。 詳しくは、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関する記事をご覧ください。

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、キー コンテナーへのアクセスは削除され、カスタマー キーで暗号化されたデータにはアクセスできなくなります。 このデータに依存する機能はすべて動作しなくなります。

> [!IMPORTANT]
> マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID が自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはリソースを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのリソースに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

カスタマー マネージド キーを有効にするには、Azure キー コンテナーを使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。

Cognitive Services の暗号化では、サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

### <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーをローテーションするときは、新しいキー URI を使用するように Cognitive Services リソースを更新する必要があります。 Azure portal で新しいバージョンのキーを使用するようにリソースを更新する方法については、[Azure portal を使用した Cognitive Services 用のカスタマー マネージド キーの構成](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)に関する記事の「**キーのバージョンを更新する**」セクションをご覧ください。

キーをローテーションしても、リソースのデータの再暗号化はトリガーされません。 ユーザーがこれ以上操作を行う必要はありません。

### <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) に関する記事、または [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事を参照してください。 アクセス権を取り消すと、Cognitive Services で暗号化キーにアクセスできなくなるため、Cognitive Services リソース内のすべてのデータへのアクセスが事実上ブロックされます。


