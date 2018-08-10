---
title: Azure リソース向けの Privileged Identity Management - サブスクリプションの管理を有効にする | Microsoft Docs
description: グローバル管理者がテナント内のサブスクリプションを管理できる方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d7a9c0090d3bfaaf1161b6255c4c0b659c77d692
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620282"
---
# <a name="enable-subscription-management-in-your-tenant"></a>テナントのサブスクリプション管理を有効にする

ディレクトリのグローバル管理者は、既定のアクセスではテナント内の一部サブスクリプション リソースを利用できないことがあります。 この記事では､テナントのすべてのサブスクリプションにアクセスできるようにする手順の概略を説明します｡ また､アクセス権を受け取った後で引き続き組織が求めるセキュリティ制御に遵守するための推奨方法も紹介します｡

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>自分のディレクトリ内のサブスクリプションの管理を有効にできるのは誰か

サブスクリプション管理を有効にする場合､グローバル管理者ロールに割り当てられたユーザーは､必ず下記手順に従う必要があります｡ 自分自身に対してサブスクリプションの管理を有効にした後、リソースへのアクセスを必要とする他のグローバル管理者を追加できます。 グローバル管理者ロールのすべてのメンバーに対してアクセスを有効にするディレクトリ設定はありません。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

グローバル管理者ロールの有資格メンバーまたはアクティブ メンバーであるアカウントを使用して Azure Portal にサインインします｡ アカウントが有資格のグローバル管理者の場合は、次の手順に進む前にロールをアクティブ化しておく必要があります。

## <a name="access-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センターにアクセスする

Azure Portal にグローバル管理者としてサインインすると、Azure サブスクリプションへのアクセス権を提供する設定を編集することができます。 Azure Active Directory (Azure AD) 管理センターに移動して､**Properties** を選択します｡

![Azure AD 管理センターのスクリーンショット - プロパティを強調表示した状態](media/azure-pim-resource-rbac/aad_properties.png)

プロパティの一覧の **Global admin can manage Azure subscriptions** で **Yes** を選択します｡

![[プロパティ] ページのスクリーンショット - Yes に設定した状態](media/azure-pim-resource-rbac/aad_properties_save.png)

これで､テナント内のすべてのサブスクリプション リソースに対するユーザー アクセス管理者ロールに自動的にアカウントが追加されます。

## <a name="browse-to-azure-ad-pim"></a>Azure AD PIM に移動する

 ここから､Azure AD Privileged Identity Management (PIM) に移動します｡ **Manage** で **Azure resources** を選択します｡

![PIM のスクリーンショット - Azure リソースを強調表示した状態](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>リソースの管理と探索を行う

組織が既に Azure AD PIM を使用して Azure AD で管理者を保護している場合は、ブレードが読み込まれたときにサブスクリプションの一覧が表示されます。

![PIM のスクリーンショット - サブスクリプション一覧をブレード表示した状態](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> リソースが表示されない場合は、以下を確認してください。
>- グローバル管理者ロールの有効期限が切れていないこと 
>- 組織が Azure サブスクリプションを持っていること

![PIM のスクリーンショット - リソース一覧が空の状態](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>割り当てを構成する

一覧からサブスクリプションを選択し、自分自身 (または自分がメンバーであるグループ) をリソースの有資格所有者として割り当てます。 
[ロールの割り当ての詳細](pim-resource-roles-assign-roles.md)

次の手順に進む前に、各リソースでこのプロセスを繰り返します。

## <a name="clean-up-standing-access"></a>永続的なアクセスをクリーンアップする

組織内の重要なサブスクリプションに対する有資格割り当てを受けると、ディレクトリのプロパティに含まれるオプションを無効にすることで、永続的なアクセスをクリーンアップできます。

![[プロパティ] ページのスクリーンショット - No に設定した状態](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>次の手順

[リソースの探索](pim-resource-roles-discover-resources.md)

[ロール設定の構成](pim-resource-roles-configure-role-settings.md)








