---
title: マネージド ID
description: Media Services を Azure マネージド ID と共に使用することができます。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 02f832c7fc25003950d6a112d951c07d1332e08a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454370"
---
# <a name="managed-identities"></a>マネージド ID

異なるサービス間でやり取りされる通信のセキュリティを確保するシークレットと資格情報の管理は、開発者に共通の課題です。 Azure のマネージド ID を使用すれば、開発者が資格情報を管理する必要がなくなります。Azure リソースの ID は Azure AD から提供され、その ID を使用して Azure Active Directory (Azure AD) トークンが取得されます。

## <a name="media-services-managed-identity-scenarios"></a>Media Services のマネージド ID のシナリオ

Media Services でマネージド ID を使用できるシナリオは 3 つあります。

- [Key Vault に Media Services アカウントのアクセス許可を付与し、カスタマー マネージド キーを有効にする](security-encrypt-data-managed-identity-cli-tutorial.md)
- [Media Services アカウントにストレージ アカウントへのアクセス許可を付与して、Media Services が Azure Storage Network ACL をバイパスできるようにする](security-access-storage-managed-identity-cli-tutorial.md)
- 他のサービス (VM や [Azure Functions](security-function-app-managed-identity-cli-tutorial.md) など) による Media Services へのアクセスを許可する

最初の 2 つのシナリオでは、マネージド ID を使用して、他のサービスへの "*Media Services アカウント*" のアクセス許可を付与します。  3 番目のシナリオでは、Media Services へのアクセスに使用されるマネージド ID が "*サービス*" に含まれています。

:::image type="content" source="media/diagrams/managed-identities-scenario-comparison.svg" alt-text="マネージド ID シナリオの比較":::

> [!NOTE]
> これらのシナリオは組み合わせることができます。 Media Services アカウント (たとえば、カスタマー マネージド キーにアクセスするためなど) と、Azure Functions リソースの両方に対してマネージド ID を作成して、Media Services アカウントにアクセスできます。

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法

これらのチュートリアルを試すと、Media Services でマネージド ID を使用する方法を実際に体験することができます。

- [CLI: Key Vault のキーを使用して Media Services アカウントにデータを暗号化する](security-encrypt-data-managed-identity-cli-tutorial.md)
- [CLI: 不明な IP アドレスからの要求をブロックするように構成されているストレージ アカウントへの Media Services のアクセスを許可する](security-access-storage-managed-identity-cli-tutorial.md)
- [CLI: 関数アプリに Media Services アカウントへのアクセス権を付与する](security-function-app-managed-identity-cli-tutorial.md)
- [PORTAL: Azure portal を使用して Media Services でカスタマー マネージド キーまたは BYOK を使用する](security-customer-managed-keys-portal-tutorial.md)
- [POSTMAN/REST: Media Services REST API でカスタマー マネージド キーまたは BYOK を使用する](security-customer-managed-keys-rest-postman-tutorial.md)

## <a name="further-reading"></a>関連項目

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

Azure Functions の詳細については、[Azure Functions](../../azure-functions/functions-overview.md) に関するページを参照してください。
