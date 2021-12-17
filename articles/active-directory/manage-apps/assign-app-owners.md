---
title: エンタープライズ アプリケーション所有者の割り当て
titleSuffix: Azure AD
description: Azure Active Directory のアプリケーションに所有者を割り当てる
services: active-directory
documentationcenter: ''
author: saipradeepb23
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: saibandaru
ms.openlocfilehash: 2eb7d911dae8e0ebf61a2d50ae692b0af2dcf1ca
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555453"
---
# <a name="assign-enterprise-application-owners"></a>エンタープライズ アプリケーション所有者の割り当て

所有者を割り当てることは、特定のアプリケーション登録またはエンタープライズ アプリケーションについての Azure AD 構成のすべての側面を管理する能力を付与するための簡単な方法です。 ユーザーは所有者として、シングル サインオン構成、プロビジョニング、ユーザーの割り当てなど、エンタープライズ アプリケーションの組織固有の構成を管理できます。 所有者は、他の所有者を追加または削除することもできます。 グローバル管理者とは異なり、所有者は、自分が所有するエンタープライズ アプリケーションのみを管理できます。

エンタープライズ アプリケーションの所有者になることができるのはユーザーのみです。 グループは所有者として割り当てることはできません。 所有者は、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションは、所有者よりも多くのアクセス許可を持つことができるため、所有者がユーザーまたはサービス プリンシパルとして持つアクセス許可を上回る特権の昇格が可能です。 

アプリケーション所有者は、アプリケーションのアクセス許可に応じて、アプリケーションの偽装中にユーザーや他のオブジェクトを作成または更新する可能性があります。 アプリケーションの所有者は、個々のアプリケーションを対象にしたアプリケーション管理者と同じアクセス許可を持っています。 詳細については、[Azure AD の組み込みロール](../roles/permissions-reference.md#application-administrator)に関するページを参照してください。 

## <a name="assign-an-owner"></a>所有者の割り当て

エンタープライズ アプリケーションに所有者を割り当てるには

1. 組織の **アプリケーション管理者** ロールまたは **クラウド アプリケーション管理者** ロールに適合するアカウントを使用して、[Azure AD 組織](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
2. **[エンタープライズ アプリケーション]** を選択し、所有者を追加したいアプリケーションを選択します。
3. **[所有者]** を選択し、 **[追加]** を選択して、所有者を選択できるユーザー アカウントの一覧を取得します。
4. アプリケーションの所有者にしたいユーザー アカウントを検索して選択します。
5. **[選択]** をクリックして、アプリケーションの所有者として選択したユーザー アカウントを追加します。

> [!NOTE]
> ユーザー設定の **[Azure AD 管理ポータルへのアクセスを制限する]** が `Yes` に設定されている場合、管理者以外のユーザーは自分が所有するアプリケーションを Azure portal を使用して管理できません。 所有するエンタープライズ アプリケーションで実行できるアクションの詳細については、「[所有するエンタープライズ アプリケーション](../fundamentals/users-default-permissions.md#owned-enterprise-applications)」を参照してください。 

## <a name="next-steps"></a>次の手順

- [Azure Active Directory でアプリ登録のアクセス許可を委任する](../roles/delegate-app-roles.md)
