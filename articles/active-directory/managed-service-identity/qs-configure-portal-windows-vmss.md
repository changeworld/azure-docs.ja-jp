---
title: Azure Portal を使用して、Azure 仮想マシン スケール セットのマネージド サービス ID を構成する
description: Azure Portal を使用して、Azure VMSS でマネージド サービス ID を構成する方法をステップ バイ ステップで説明します。
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887991"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Azure Portal を使用して、仮想マシン スケール セットのマネージド サービス ID を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal を使用して、システムとユーザーによって仮想マシン スケール セットに割り当てられた ID を有効および無効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

- MSI の基本的な事柄については、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するには、アカウントに次のロールが割り当てられている必要があります。
    - 仮想マシン スケール セットからシステム割り当てマネージド ID を有効化および削除するための[仮想マシン共同作成者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)。

## <a name="system-assigned-identity"></a>システム割り当て ID 

このセクションでは、Azure portal を使用して、仮想マシン スケール セットでシステム割り当て ID を有効および無効にする方法について説明します。

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にシステム割り当て ID を有効にする

現在 Azure portal では、仮想マシン スケール セット作成中のシステム割り当て ID の有効化はサポートされていません。 代わりに、仮想マシン スケール セットの作成に関する次のクイック スタート記事を参照して、まず仮想マシン スケール セットを作成してください。そして、仮想マシン スケール セットでシステム割り当て ID を有効にする方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>既存の仮想マシン スケール セットでシステム割り当て ID を有効にする

もともとシステム割り当て ID がプロビジョニングされていなかった仮想マシン スケール セットで、システム割り当て ID を有効にするには:

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシン スケール セットに移動します。

3. **[システム割り当て済み]** にある **[状態]** で **[オン]** を選択して、**[保存]** をクリックします。

   [![[構成] ページのスクリーンショット](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>仮想マシン スケール セットからシステム割り当て ID を削除する

システム割り当て ID が不要になった仮想マシン スケール セットがある場合:

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシン スケール セットに移動します。

3. **[システム割り当て済み]** にある **[状態]** で **[オフ]** を選択して、**[保存]** をクリックします。

   ![[構成] ページのスクリーンショット](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

このセクションでは、Azure portal を使用して、仮想マシン スケール セットとの間でユーザー割り当て ID を追加および削除する方法について説明します。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>仮想マシン スケール セットの作成中にユーザー割り当て ID を割り当てる

現在 Azure portal では、仮想マシン スケール セット作成中のユーザー割り当て ID の割り当てはサポートされていません。 代わりに、仮想マシン スケール セットの作成に関する次のクイック スタート記事を参照して、まず仮想マシン スケール セットを作成してください。そして、その仮想マシン スケール セットにユーザー割り当て ID を割り当てる方法について詳しく説明している、次のセクションに進んでください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>既存の仮想マシン スケール セットにユーザー割り当て ID を割り当てる

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の仮想マシン スケール セットに移動して、**[ID]**、**[ユーザー割り当て済み]**、**\+[追加]** の順にクリックします。

   ![VMSS へのユーザー割り当て ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. 仮想マシン スケール セットに追加したいユーザー割り当て ID をクリックして、**[追加]** をクリックします。
   
   ![VMSS へのユーザー割り当て ID の追加](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>仮想マシン スケール セットからユーザー割り当て ID を削除する

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. 目的の仮想マシン スケール セットに移動します。次に、**[ID]**、**[ユーザー割り当て済み]**、削除したいユーザー割り当て済み ID の名前を順にクリックしてから、**[削除]** をクリックします (確認ウィンドウで **[はい]** をクリックします)。

   ![VMSS からのユーザー割り当て ID の削除](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>関連コンテンツ

- マネージド サービス ID の概要については、[概要](overview.md)をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Portal を使用して、Azure 仮想マシン スケール セットのマネージド サービス ID に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
