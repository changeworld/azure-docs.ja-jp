---
title: Media Services でのマネージド ID と信頼されたストレージ
description: Media Services をマネージド ID と共に使用して、信頼されたストレージを有効にすることができます。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: a0ded2a1ad5cd590ab5715edebde2ab19e399e8d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686468"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Media Services でのマネージド ID と信頼されたストレージ

Media Services を[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) と共に使用して、信頼されたストレージを有効にすることができます。 Media Services アカウントを作成するときは、それをストレージ アカウントに関連付ける必要があります。 Media Services は、システム認証を使用してそのストレージ アカウントにアクセスできます。 Media Services により、Media Services アカウントとストレージ アカウントが同じサブスクリプションに含まれていることが検証され、また、関連付けを追加するユーザーが Azure Resource Manager RBAC を使用してストレージ アカウントにアクセスしていることが検証されます。

## <a name="trusted-storage"></a>信頼されたストレージ

ただし、ストレージ アカウントをセキュリティで保護するためにファイアウォールを使用する場合は、マネージド ID 認証を使用する必要があります。 これにより、Media Services は、信頼されたストレージ アクセスによって、ファイアウォールまたは VNet 制限で構成されたストレージ アカウントにアクセスできるようになります。  信頼された Microsoft サービスの詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)」を参照してください。

## <a name="media-services-managed-identity-scenarios"></a>Media Services のマネージド ID のシナリオ

現在、Media Services でマネージド ID を使用できるシナリオは 2 つあります。

- Media Services アカウントのマネージド ID を使用してストレージ アカウントにアクセスする。

- Media Services アカウントのマネージド ID を使用して Key Vault にアクセスし、顧客キーにアクセスする。

次の 2 つのセクションでは、2 つのシナリオの違いについて説明します。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Media Services アカウントのマネージド ID を使用してストレージ アカウントにアクセスする

1. マネージド ID が割り当てられた Media Services アカウントを作成します。
1. 自分が所有しているストレージ アカウントに、マネージド ID プリンシパルのアクセス権を付与します。
1. Media Services は、マネージド ID を使用して、ユーザーに代わってストレージ アカウントにアクセスできます。

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Media Services アカウントのマネージド ID を使用して Key Vault にアクセスし、顧客キーにアクセスする

1. マネージド ID が割り当てられた Media Services アカウントを作成します。
1. 自分が所有している Key Vault に、マネージド ID プリンシパルのアクセス権を付与します。
1. 顧客キー ベースのアカウントの暗号化を使用するように Media Services アカウントを構成します。
1. Media Services は、マネージド ID を使用して、ユーザーに代わって Key Vault にアクセスします。

カスタマー マネージド キーと Key Vault の詳細については、「[Media Services での Bring Your Own Key (カスタマー マネージド キー)](concept-use-customer-managed-keys-byok.md)」を参照してください。

## <a name="tutorials"></a>チュートリアル

これらのチュートリアルには、前述の両方のシナリオが含まれます。

- [Azure portal を使用して Media Services でカスタマー マネージド キーまたは BYOK を使用する](tutorial-byok-portal.md)
- [Media Services REST API でカスタマー マネージド キーまたは BYOK を使用する](tutorial-byok-postman.md)。

## <a name="next-steps"></a>次のステップ

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関するページを参照してください。
