---
title: Azure Active Directory Domain Services の削除 | Microsoft Docs
description: Azure portal を使用して Azure Active Directory Domain Services マネージド ドメインを無効化または削除する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fa0cc8c0ca548dbf5021d7531198802729afaf2f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427819"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure portal を使用して Azure Active Directory Domain Services マネージド ドメインを無効化または削除する

Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインが不要になった場合は、削除できます。 Azure AD DS マネージド ドメインをオフにしたり、一時的に無効にしたりすることはできません。 マネージド ドメインを削除しても、Azure AD テナントが削除されたり、その他の悪影響が出たりすることはありません。

この記事では、Azure portal を使用してマネージド ドメインを削除する方法について説明します。

> [!WARNING]
> **削除は永続的であり、元に戻すことはできません。**
> 
> マネージド ドメインを削除すると、次の処理が行われます。
>   * マネージド ドメインのドメイン コント ローラーはプロビジョニング解除され、仮想ネットワークから削除されます。
>   * マネージド ドメイン上のデータは完全に削除されます。 このデータには、作成したカスタム OU、GPO、カスタムの DNS レコード、サービス プリンシパル、GMSA などが含まれます。
>   * マネージド ドメインに参加しているコンピューターでは、ドメインとの信頼関係が失われ、ドメインへの参加を解消する必要があります。
>       * 企業の AD 資格情報を使用してこれらのコンピューターにサインインすることはできません。 代わりに、コンピューターのローカル管理者の資格情報を使用する必要があります。

## <a name="delete-the-managed-domain"></a>マネージド ドメインを削除する

マネージド ドメインを削除するには、次の手順を実行します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. マネージド ドメインの名前 (*aaddscontoso.com* など) を選択します。
1. **[概要]** ページで **[削除]** を選択します。 削除を確定するには、マネージド ドメインのドメイン名を再度入力し、 **[削除]** を選択します。

マネージド ドメインを削除するには、15 から 20 分以上かかることがあります。

## <a name="next-steps"></a>次のステップ

Azure AD DS に希望する機能があれば、[フィードバックの投稿][feedback]をご検討ください。

Azure AD DS を再開する場合は、[Azure Active Directory Domain Services マネージド ドメインの作成と構成][create-instance]に関する記事をご覧ください。

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789?c=5d63b5b7-ae25-ec11-b6e6-000d3a4f0789
[create-instance]: tutorial-create-instance.md
