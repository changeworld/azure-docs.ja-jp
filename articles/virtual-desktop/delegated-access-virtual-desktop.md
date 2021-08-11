---
title: Azure Virtual Desktop における委任されたアクセス - Azure
description: Azure Virtual Desktop のデプロイで管理機能を委任する方法 (例を含む)。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 2367a2840e64956d3dfca41ce8ee5e31c9f354c7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751993"
---
# <a name="delegated-access-in-azure-virtual-desktop"></a>Azure Virtual Desktop における委任されたアクセス

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)を参照してください。

Azure Virtual Desktop には委任されたアクセス モデルが用意され、これにより特定のユーザーにロールを割り当てることで、それらのユーザーが持つことのできるアクセス数を定義できます。 ロールの割り当てには、セキュリティ プリンシパル、ロールの定義、スコープの 3 つのコンポーネントがあります。 Azure Virtual Desktop の委任されたアクセス モデルは、Azure RBAC モデルに基づいています。 特定のロールの割り当てとそのコンポーネントの詳細については、「[Azure ロールベースのアクセス制御の概要](../role-based-access-control/built-in-roles.md)」を参照してください。

Azure Virtual Desktop の委任されたアクセスでは、ロール割り当ての要素ごとに次の値がサポートされています。

* セキュリティ プリンシパル
    * ユーザー
    * ユーザー グループ
    * サービス プリンシパル
* ロール定義
    * 組み込みのロール
    * カスタム ロール
* Scope
    * ホスト プール
    * アプリ グループ
    * Workspaces

## <a name="powershell-cmdlets-for-role-assignments"></a>ロール割り当てを目的とした PowerShell のコマンドレット

開始する前に、[PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って、Azure Virtual Desktop PowerShell モジュールを設定してください (まだ設定していない場合)。

Azure Virtual Desktop では、アプリ グループをユーザーまたはユーザー グループに公開するときに、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。 デスクトップ仮想化ユーザー ロールがユーザーまたはユーザー グループに割り当てられます。そのスコープはアプリ グループになります。 このロールは、アプリ グループに対する特別なデータ アクセスをユーザーに付与します。

次のコマンドレットを実行して、Azure Active Directory ユーザーをアプリ グループに追加します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、Azure Active Directory ユーザー グループをアプリ グループに追加します。

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>次のステップ

各ロールで使用できる PowerShell コマンドレットのより詳細な一覧については、「[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)」を参照してください。

Azure RBAC でサポートされているロールの完全な一覧については、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

Azure Virtual Desktop 環境を設定する方法のガイドについては、「[Azure Virtual Desktop 環境](environment-setup.md)」を参照してください。
