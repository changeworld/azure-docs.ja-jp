---
title: Azure でのリソース アクセスについて | Microsoft Docs
description: このトピックでは、完全な Azure Portal でのサブスクリプション管理者によるリソース アクセス制御の概念について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.custom: it-pro;
ms.openlocfilehash: 8996406a2e4c907402651d3f8cb8cd19e47e2829
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="understanding-resource-access-in-azure"></a>Azure でのリソース アクセスについて

Azure のアクセス制御では、最初に課金に注目します。 [Azure アカウント センター](https://account.azure.com)からアクセスされる、Azure アカウントの所有者は、アカウント管理者 (AA) です。 サブスクリプションは課金用のコンテナーですが、セキュリティの境界としても機能します。 各サブスクリプションはサービス管理者 (SA) を有し、SA は[Azure Portal](https://portal.azure.com/) を使ってそのサブスクリプションの Azure リソースを追加、削除、変更することができます。 新しいサブスクリプションの既定の SA は AA ですが、AA は Azure アカウント センターで SA を変更できます。

<br><br>![Azure アカウント][1]

サブスクリプションは、ディレクトリとも関連付けられています。 ディレクトリでは、一連のユーザーを定義します。 たとえば、ディレクトリを作成した職場や学校のユーザーや、外部のゲスト ユーザーとして定義できます。 サブスクリプションは、サービス管理者 (SA) または共同管理者 (CA) のいずれかとして割り当てられているディレクトリ ユーザーのサブセットからアクセス可能です。唯一の例外は、従来版との兼ね合いから、Microsoft アカウント (旧 Windows Live ID) はディレクトリに存在しなくても SA または CA として割り当てることができる、という点です。

<br><br>![Azure でのアクセス制御][2]

Microsoft アカウントを使ってサインインした SA は、Azure Portal 内の機能を使って、サブスクリプションが関連付けられているディレクトリを変更できます。 この操作は、サブスクリプションのアクセス制御に対して影響を及ぼします。

<br><br>![簡単なユーザー サインインのフロー][3]

簡単な例では、組織 (Contoso など) による課金とアクセス制御が、同じサブスクリプション セットに対して適用されます。 つまり、ディレクトリは、1 つの Azure アカウントが所有するサブスクリプションに関連付けられます。 Azure Portal に正常にログインすると、2 つのリソース コレクションが表示されます (前の図ではオレンジ色で表示)。

* ユーザー アカウントが存在しているディレクトリ (内部で提供、または外部プリンシパルとして追加)。 ログインに使用するディレクトリがこの計算に関連付けられないこと、したがって、ログインした場所にかかわりなくディレクトリは常に表示されることに注意してください。
* ログインに使用されるディレクトリに関連付けられているサブスクリプションの一部であると同時に、ユーザー (ここでは SA または CA) がアクセスできるリソース。

<br><br>![複数のサブスクリプションおよびディレクトリを持つユーザー][4]

複数のディレクトリにまたがるサブスクリプションを使っているユーザーは、サブスクリプション フィルターを使って、Azure Portal の現在のコンテキストを切り替えることができます。 内部的には、ディレクトリごとに異なるログインという結果になっていますが、この操作は、シングル サインオン (SSO) によってシームレスに実行されます。

サブスクリプションのディレクトリ ビューが 1 つであるため、複数のサブスクリプション間でリソースを移動する、といった操作はさらに難しくなる可能性があります。 リソースの転送を実行するには、まず最初に **[設定]** の [サブスクリプション] ページから **[ディレクトリの編集]** コマンドを使って、同じディレクトリにサブスクリプションを関連付けます。

## <a name="next-steps"></a>次の手順
* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../billing/billing-add-change-azure-subscription-administrator.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)
* Azure AD でロールを割り当てる方法の詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](../active-directory/active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/rbac-and-directory-admin-roles/IC707931.png
[2]: ./media/rbac-and-directory-admin-roles/IC707932.png
[3]: ./media/rbac-and-directory-admin-roles/IC707933.png
[4]: ./media/rbac-and-directory-admin-roles/IC707934.png
