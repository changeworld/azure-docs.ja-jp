---
title: "Azure ポータルを使用して Azure VM で MSI を構成する方法"
description: "Azure ポータルを使用して、Azure VM で管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9406ba2bbbea41f4677cd0d5aaddf16b0f4f26c8
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Azure ポータルを使用して、VM 管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure ポータルを使用して Azure Windows VM の MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Azure VM の作成中に MSI を有効にする

この記事の執筆時点で、Azure ポータルで VM の作成中に MSI を有効にすることはサポートされていません。 代わりに、 VM を作成する方法の詳細については、クイック スタート「[Azure ポータルで Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)」を参照してください。 続いて、次のセクションに進み、MSI を有効にする方法の詳細を参照します。

## <a name="enable-msi-on-an-existing-azure-vm"></a>既存の Azure VM で MSI を有効にする

最初にプロビジョンされた VM (MSI なし) が存在する場合は、次のようにします。

1. VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。

2. 目的の仮想マシンに移動します。

2. [構成] ページをクリックして、[管理対象サービス ID] で [はい] を選択して VM で MSI を有効にし、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM から MSI を削除する

MSI が不要になった仮想マシンがある場合は、次のようにします。

1. VM をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。

2. 目的の仮想マシンに移動します。

3. [構成] ページをクリックして、[管理対象サービス ID] で [いいえ] を選択して VM から MSI を削除し、**[保存]** をクリックします。 この操作が完了するまでに 60 秒以上かかる場合があります。

   ![[構成] ページのスクリーンショット](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- この記事の出典元は、MSI 固有の手順を含めるように変更されたクイック スタート「[Azure ポータルを使用して Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)」です。 

## <a name="next-steps"></a>次のステップ

- Azure ポータルを使用して、Azure VM の MSI に[別の Azure リソースへのアクセス](msi-howto-assign-access-portal.md)を許可します。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

