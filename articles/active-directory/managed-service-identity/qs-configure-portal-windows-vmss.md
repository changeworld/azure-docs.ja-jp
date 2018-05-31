---
title: Azure Portal を使用して Azure 仮想マシン スケール セットの MSI を構成する
description: Azure Portal を使用して、Azure VMSS で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: c915c692a12781538e10d367d40e3efe473a6853
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929045"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Azure portal を使用して VMSS マネージド サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure portal を使用して、システムが VMSS に割り当てる ID を有効および無効にする方法について説明します。 ユーザーが Azure portal を使用して Azure VMSS の ID を割り当てたり削除したりすることは、現在はサポートされていません。

> [!NOTE]
> 現時点で、ユーザー割り当ての ID の操作は、Azure portal ではサポートされていません。 アップデートは後ほどご確認ください。

## <a name="prerequisites"></a>前提条件


- マネージド サービス ID の基本についてご不明な点がある場合は、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中のマネージド サービス ID

現在、Azure portal からの VM の作成では、マネージド サービス ID の操作はサポートされていません。 代わりに、次の Azure 仮想マシン スケール セットの作成に関するクイック スタートの記事を参照して、最初に Azure 仮想マシン スケール セットを作成してください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)  

続いて、次のセクションに進み、仮想マシン スケール セットで MSI を有効にする方法の詳細を参照します。

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>既存の Azure VMMS でマネージド サービス ID を有効にする

もともとシステム割り当て ID がプロビジョニングされていなかった VM において、システム割り当て ID を有効にするには:

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシン スケール セットに移動します。

3. [マネージド サービス ID] で [はい] を選択して VM におけるシステム割り当て ID を有効にし、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   [![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットからマネージド サービス ID を削除する

MSI が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシン スケール セットに移動します。

3. [マネージド サービス ID] で [いいえ] を選択して VM におけるシステム割り当て ID を無効にし、[保存] をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>関連コンテンツ

- マネージド サービス ID の概要については、[概要](overview.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Portal を使用して、Azure 仮想マシン スケール セットの MSI に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
