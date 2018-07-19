---
title: Azure ポータルを使用して、Azure リソースに MSI アクセスを割り当てる方法
description: Azure Portal を使用して、1 つのリソースに MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: cf6df6dd87de56ca3c8180d5377e35d9be9bbd79
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036447"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Azure Portal を使用してリソースに管理対象サービス ID アクセスを割り当てる

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID (MSI) で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この記事では、Azure Portal を使用して、Azure 仮想マシンまたは仮想マシン スケール セットの MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

[Azure VM](qs-configure-portal-windows-vm.md)、[Azure VMSS](qs-configure-portal-windows-vmss.md) などの Azure リソースで MSI を有効にした後、次のようにします。

1. MSI ファイルを構成した Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。

2. アクセス制御を変更する目的のリソースに移動します。 この例では、ストレージ アカウントに Azure 仮想マシンおよび Azure 仮想マシン スケール セット アクセスを許可するため、ストレージ アカウントに移動します。

3. Azure 仮想マシンについては、リソースの **[アクセス制御 (IAM)]** ページを選択し、**[+ 追加]** を選択します。 **[ロール]** を指定し、**[アクセスの割り当て先] を [仮想マシン]** にして、対応する **[サブスクリプション]** とリソースが存在する **[リソース グループ]** を指定します。 検索条件領域に、リソースが表示されるはずです。 リソースを選択し、**[保存]** を選択します。 

   ![アクセス制御 (IAM) のスクリーンショット](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Azure 仮想マシン スケール セットについては、リソースの **[アクセス制御 (IAM)]** ページを選択し、**[+ 追加]** を選択します。 次に、**ロール**、**アクセスの割り当て先**を指定します。 検索条件領域で、仮想マシン スケール セットを検索します。 リソースを選択し、**[保存]** を選択します。
   
   ![アクセス制御 (IAM) のスクリーンショット](../managed-service-identity/media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. **[アクセス制御 (IAM)]** のメイン ページに戻ると、リソースの MSI の新しいエントリが表示されます。

    Azure 仮想マシン:

   ![アクセス制御 (IAM) のスクリーンショット](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Azure 仮想マシン スケール セット:

    ![アクセス制御 (IAM) のスクリーンショット](../managed-service-identity/media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>トラブルシューティング

リソースの MSI が使用可能な ID の一覧に表示されない場合は、MSI が正しく有効になっていることを確認します。 この場合は、Azure 仮想マシンに戻り、次のことを確認します。

- **[構成]** ページを参照し、**MSI の有効**の値が **[はい]** になっていることを確認します。
- **[拡張]** ページを参照して、MSI 拡張機能が正常に展開されていることを確認します (**[拡張]** ページは、Azure 仮想マシン スケール セットでは使用できません)。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](overview.md)に関する記事をご覧ください。
- Azure 仮想マシンで MSI を有効にするには、「[Configure a VM Managed Service Identity (MSI) using the Azure portal (Azure Portal を使用して Azure VM 管理対象サービス ID (MSI) を構成する)](qs-configure-portal-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セットで MSI を有効にするには、「[Configure an Azure Virtual Machine Scale Set Managed Service Identity (MSI) using the Azure portal (Azure Portal を使用して Azure 仮想マシン スケール セット管理対象サービス ID (MSI) を構成する)](qs-configure-portal-windows-vmss.md)」を参照してください


