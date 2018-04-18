---
title: Azure リソース向けの Privileged Identity Management - サブスクリプションの管理を有効にする | Microsoft Docs
description: グローバル管理者がテナント内のサブスクリプションを管理できる方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>サブスクリプションの管理を有効化する

ディレクトリのグローバル管理者は、既定のアクセスではテナント内のすべてのサブスクリプションのリソースにアクセスできないことがあります。 この記事では、テナント内のすべてのサブスクリプションにアクセスできるようにするための手順と、アクセス権の取得後に組織で要求されているセキュリティ コントロールへの準拠を維持するために推奨されるアプローチについて説明します。

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>自分のディレクトリ内のサブスクリプションの管理を有効にできるのは誰か

グローバル管理者ロールに割り当てられた各ユーザーは、次の手順に従って、サブスクリプションの管理を有効にする必要があります。 自分自身に対してサブスクリプションの管理を有効にした後、リソースへのアクセスが必要な場合がある他のグローバル管理者を追加できます。 グローバル管理者ロールのすべてのメンバーに対してアクセスを有効にするディレクトリ設定はありません。

## <a name="log-on-to-the-azure-portal"></a>Azure Portal にログオンする

グローバル管理者ロールの有資格メンバーまたはアクティブ メンバーであるアカウントで Azure Portal にログインすることから始めます。 アカウントが有資格のグローバル管理者の場合は、次の手順に進む前にロールをアクティブ化しておく必要があります。

## <a name="access-the-azure-ad-admin-center"></a>Azure AD 管理センターにアクセスする

Azure Portal にグローバル管理者としてにログオンしているため、Azure サブスクリプションへのアクセスを提供する設定を編集できます。 Azure AD 管理センターに移動し、左側のナビゲーションで [プロパティ] タブを見つけて選択します。

![](media/azure-pim-resource-rbac/aad_properties.png)

プロパティの一覧で、[Global admin can manage Azure subscriptions]\(グローバル管理者は Azure サブスクリプションを管理できる\) オプションを [はい] に切り替えます。

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Azure AD PIM に移動する

このオプションを有効にすると、テナント内のすべてのサブスクリプション リソースに対する [ユーザー アクセス管理者] ロールに自分のアカウントが自動的に追加されます。 ここから、Azure AD PIM に移動し、左側のナビゲーションの [管理] セクションで [Azure リソース] を選択します。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>リソースの管理と探索を行う

組織が既に Azure AD PIM を使用して Azure Active directory で管理者を保護している場合は、ブレードが読み込まれたときにサブスクリプションの一覧が表示されます。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> リソースが表示されない場合は、以下を確認してください。
>- グローバル管理者ロールの有効期限が切れていないこと 
>- 組織が Azure サブスクリプションを持っていること

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>割り当てを構成する

一覧からサブスクリプションを選択し、自分自身 (または自分がメンバーであるグループ) をリソースの有資格所有者として割り当てます。 
[ロールの割り当ての詳細](pim-resource-roles-assign-roles.md)

次の手順に進む前に、各リソースでこのプロセスを繰り返します。

## <a name="clean-up-standing-access"></a>永続的なアクセスをクリーンアップする

組織内の重要なサブスクリプションに対する有資格割り当てを行ったため、ディレクトリのプロパティに含まれるオプションを無効にすることで、永続的なアクセスをクリーンアップできます。

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>次の手順

[リソースの探索](pim-resource-roles-discover-resources.md)

[ロール設定の構成](pim-resource-roles-configure-role-settings.md)








