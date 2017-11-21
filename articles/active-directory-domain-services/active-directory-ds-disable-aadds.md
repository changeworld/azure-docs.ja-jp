---
title: "Azure Active Directory Domain Services の無効化 | Microsoft Docs"
description: "Azure Portal を使用して Azure Active Directory Domain Services を無効にする"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: 32db916b319c531816a935fcfe3bc4fe82997fdd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を無効にする
この記事では、Azure ポータルを使用して、Azure AD ディレクトリの Azure Active Directory Domain Services を無効にする方法を示します。

> [!WARNING]
> **削除は永続的であり、元に戻すことはできません。**
> 慎重に進めてください。 管理対象ドメインを削除する場合、次の点に注意してください。
  * 管理対象ドメインのドメイン コント ローラーはプロビジョニング解除され、仮想ネットワークから削除されます。
  * 管理対象ドメイン上のデータは完全に削除されます。 これには、管理対象ドメインで作成したカスタム OU、GPO、カスタムの DNS レコード、サービス プリンシパル、GMSA などが含まれます。
  * 管理対象ドメインに参加しているコンピューターでは、ドメインとの信頼関係が失われ、ドメインへの参加を解消する必要があります。
  * 企業の AD 資格情報を使用してこれらのコンピューターにサインインすることはできません。 代わりに、該当のコンピューターのローカル管理者の資格情報を使用します。
管理対象ドメインを削除しても、Azure AD ディレクトリが削除されたり、ディレクトリに悪影響を与えたりすることはありません。
>

Azure AD Domain Services の管理対象ドメインを削除するには、次の手順を実行します。
1. Azure Portal で、[Azure AD Domain Services 拡張機能](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. 管理対象ドメインの名前をクリックします。

    ![削除するドメインの選択](./media/getting-started/domain-services-delete-select-domain.png)

3. **[概要]** ページで、**[削除]** ボタンをクリックします。

    ![ドメインの削除](./media/getting-started/domain-services-delete-domain.png)

4. 削除を確認するには、管理対象ドメインの DNS ドメイン名を入力します。 終了したら、**[削除]** ボタンをクリックします。

    ![ドメインの削除の確認](./media/getting-started/domain-services-delete-domain-confirm.png)

管理対象ドメインは、約 15 ~ 20 分で削除されます。

どの機能によってユーザーが将来 Azure AD Domain Services を選択するかを理解するために、 [フィードバックの共有](active-directory-ds-contact-us.md) を検討してください。 このフィードバックは、デプロイのニーズとユースケースに合わせたサービスの拡張に活用させていただきます。
