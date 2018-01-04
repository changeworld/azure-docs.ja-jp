---
title: "Azure ポータルを使用して、Azure リソースに MSI アクセスを割り当てる方法"
description: "Azure Portal を使用して、1 つのリソースに MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7d0a50db28ba3d9926f7a83fe224b7a0dbe6ed20
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Azure Portal を使用してリソースに管理対象サービス ID アクセスを割り当てる

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID (MSI) で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この記事では、Azure Portal を使用して、Azure 仮想マシンの MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

[Azure VM などの](msi-qs-configure-portal-windows-vm.md) Azure リソースで MSI を有効にした後、次のようにします。

1. MSI ファイルを構成した Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。

2. アクセス制御を変更する目的のリソースに移動します。 この例では、ストレージ アカウントに Azure VM アクセスを許可しているため、ストレージ アカウントに移動します。

3. リソースの **[アクセス制御 (IAM)]** ページを選択し、**[+ 追加]** を選択します。 **[ロール]** を指定し、**[アクセスの割り当て先] を [仮想マシン]** にして、対応する **[サブスクリプション]** とリソースが存在する **[リソース グループ]** を指定します。 検索条件領域に、リソースが表示されるはずです。 リソースを選択し、**[保存]** を選択します。 

   ![アクセス制御 (IAM) のスクリーンショット](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. **[アクセス制御 (IAM)]** のメイン ページに戻ると、リソースの MSI の新しいエントリが表示されます。 この例では、デモ リソース グループの "SimpleWinVM" VM に、ストレージ アカウントへの**共同作成者**アクセスが許可されています。

   ![アクセス制御 (IAM) のスクリーンショット](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>トラブルシューティング

リソースの MSI が使用可能な ID の一覧に表示されない場合は、MSI が正しく有効になっていることを確認します。 この場合は、Azure VM に戻り、次のことを確認します。

- **[構成]** ページを参照し、**MSI の有効**の値が **[はい]** になっていることを確認します。
- **[拡張]** ページを参照して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- Azure VM で MSI を有効にするには、「[Azure ポータルを使用して Azure VM 管理対象サービスID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」を参照してください。


