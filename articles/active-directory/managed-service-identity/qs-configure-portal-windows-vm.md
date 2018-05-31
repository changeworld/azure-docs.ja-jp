---
title: Azure ポータルを使用して Azure VM で MSI を構成する方法
description: Azure ポータルを使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930342"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Azure ポータルを使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal を使用して、システムが Azure VM に割り当てる ID を有効および無効にする方法について説明します。 ユーザーが Azure portal を使用して Azure VM の ID を割り当てたり削除したりすることは、現在はサポートされていません。

> [!NOTE]
> 現時点で、ユーザー割り当ての ID の操作は、Azure portal ではサポートされていません。 アップデートは後ほどご確認ください。 

## <a name="prerequisites"></a>前提条件

- マネージド サービス ID の基本についてご不明な点がある場合は、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Azure VM の作成中のマネージド サービス ID

現在、Azure portal からの VM の作成では、マネージド サービス ID の操作はサポートされていません。 代わりに、最初に VM を作成するには、VM 作成に関する次のいずれかのクイックスタートの記事を参照してください。

- [Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Azure Portal で Linux 仮想マシンを作成する](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

続いて、次のセクションに進み、VM でマネージド サービス ID を有効にする方法の詳細を参照します。

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>既存の Azure VM でマネージド サービス ID を有効にする

もともとシステム割り当て ID がプロビジョニングされていなかった VM において、システム割り当て ID を有効にするには:

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 対象の仮想マシンに移動し、[構成] ページを選択します。

3. [マネージド サービス ID] で [はい] を選択して VM におけるシステム割り当て ID を有効にし、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

    > [!NOTE]
    > Azure portal による VM へのユーザー割り当て ID の追加は、現時点ではサポートされていません。

   ![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Azure VM からマネージド サービス ID を削除する

仮想マシンでシステム割り当ての ID が不要になった場合は、次のようにします。

1. VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、「仮想マシンの共同作業者」など、VM 上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 対象の仮想マシンに移動し、[構成] ページを選択します。

3. [マネージド サービス ID] で [いいえ] を選択して VM におけるシステム割り当て ID を無効にし、[保存] をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

    > [!NOTE]
    > Azure portal による VM へのユーザー割り当て ID の追加は、現時点ではサポートされていません。

   ![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>関連コンテンツ

- マネージド サービス ID の概要については、[概要](overview.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure ポータルを使用して、Azure VM の MSI に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。

