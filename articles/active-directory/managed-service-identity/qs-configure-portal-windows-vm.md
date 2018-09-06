---
title: Azure portal を使用して Azure VM でマネージド サービス ID を構成する方法
description: Azure portal を使用して、Azure VM でマネージド サービス ID を構成する手順を説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888558"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Azure portal を使用して VM マネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal を使用して、システムとユーザーによって Azure 仮想マシン (VM) に割り当てられた ID を有効および無効にする方法について説明します。 

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するには、アカウントに次のロールが割り当てられている必要があります。
    - Azure VM のシステム割り当て ID を有効化および削除するための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。

## <a name="system-assigned-identity"></a>システム割り当て ID

このセクションでは、Azure portal を使用して VM のシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>VM の作成中にシステム割り当て ID を有効にする

現在 Azure portal では、VM 作成中のシステム割り当て ID の有効化はサポートされていません。 代わりに、VM の作成に関する次のいずれかのクイック スタート記事を参照して、まず VM を作成してください。そして、その VM でシステム割り当て ID を有効にする方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>既存の VM でシステム割り当て ID を有効にする

もともとシステム割り当て ID がプロビジョニングされていなかった VM において、システム割り当て ID を有効にするには:

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシンに移動して **[ID]** を選択します。

3. **[システム割り当て済み]** にある **[状態]** で **[オン]** を選択して、**[保存]** をクリックします。

   ![[構成] ページのスクリーンショット](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>VM からシステム割り当て ID を削除する

仮想マシンでシステム割り当ての ID が不要になった場合は、次のようにします。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 

2. 目的の仮想マシンに移動して **[ID]** を選択します。

3. **[システム割り当て済み]** にある **[状態]** で **[オフ]** を選択して、**[保存]** をクリックします。

   ![[構成] ページのスクリーンショット](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

 このセクションでは、Azure portal を使用して、VM との間でユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>VM の作成中にユーザー割り当て ID を割り当てる

現在 Azure portal では、VM 作成中のユーザー割り当て ID の割り当てはサポートされていません。 代わりに、VM の作成に関する次のいずれかのクイック スタート記事を参照して、まず VM を作成してください。そして、その VM にユーザー割り当て ID を割り当てる方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>ユーザー割り当て ID を既存の VM に割り当てる

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の VM に移動して、**[ID]**、**[ユーザー割り当て済み]**、**\+[追加]** の順にクリックします。

   ![VM へのユーザー割り当て ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. VM に追加したいユーザー割り当て ID をクリックして、**[追加]** をクリックします。

    ![VM へのユーザー割り当て ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>VM からユーザー割り当て ID を削除する

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の VM に移動します。次に、**[ID]**、**[ユーザー割り当て済み]**、削除したいユーザー割り当て済み ID の名前を順にクリックしてから、**[削除]** をクリックします (確認ウィンドウで **[はい]** をクリックします)。

   ![VM からのユーザー割り当て ID の削除](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>関連コンテンツ

- マネージド サービス ID の概要については、[概要](overview.md)をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure portal を使用して、Azure VM のマネージド サービス ID に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。

