---
title: Azure Portal を使用して Azure 仮想マシン スケール セットの MSI を構成する
description: Azure Portal を使用して、Azure VMSS で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Azure Portal を使用して、Azure 仮想マシン スケール セットの管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure Portal を使用して Azure 仮想マシン スケール セットの MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットの作成中に MSI を有効にする

この記事の執筆時点で、Azure Portal で仮想マシン スケール セットの作成中に MSI を有効にすることはサポートされていません。 代わりに、次の Azure 仮想マシン スケール セットの作成に関するクイック スタートの記事を参照して、最初に Azure 仮想マシン スケール セットを作成してください。

- [Azure Portal での仮想マシン スケール セットの作成](../../virtual-machine-scale-sets/quick-create-portal.md)  

続いて、次のセクションに進み、仮想マシン スケール セットで MSI を有効にする方法の詳細を参照します。

## <a name="enable-msi-on-an-existing-azure-vmms"></a>既存の Azure VMMS で MSI を有効にする

最初にプロビジョンされた仮想マシン スケール セット (MSI なし) が存在する場合は、次のようにします。

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。

2. 目的の仮想マシン スケール セットに移動します。

3. **[構成]** ページをクリックし、[管理対象サービス ID] で **[はい]** を選択して仮想マシン スケール セットで MSI を有効にし、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットから MSI を削除する

MSI が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. 仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。 また、お使いのアカウントが、仮想マシン スケール セット上の書き込みアクセス許可が提供されるロールに属していることを確認します。

2. 目的の仮想マシン スケール セットに移動します。

3. **[構成]** ページをクリックし、**[管理対象サービス ID]** で **[いいえ]** を選択して仮想マシン スケール セットから MSI を削除し、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>次の手順

- MSI の概要については、[管理対象サービス ID の概要](overview.md)に関する記事をご覧ください。
- Azure Portal を使用して、Azure 仮想マシン スケール セットの MSI に[別の Azure リソースへのアクセス](howto-assign-access-portal.md)を許可します。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
