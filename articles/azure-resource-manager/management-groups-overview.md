---
title: "Azure 管理グループでリソースを整理する | Microsoft Docs"
description: "管理グループとその使用方法について説明します。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2018
ms.author: rithorn
ms.openlocfilehash: d1974ff9716d6b84a5f4b4f1795a84483d277a05
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 管理グループのリソースを整理する 

サブスクリプションが複数ある場合は、"管理グループ" と呼ばれるコンテナーにサブスクリプションをまとめて、サブスクリプション間のアクセス、ポリシー、コンプライアンスを管理しやすくすることができます。 これらのコンテナーを使うと、サブスクリプションの種類に関係なく、大きな規模でエンタープライズ レベルの管理を行うことができます。  

管理グループ機能は、パブリック プレビューで使用できます。 管理グループを使い始めるには、[Azure Portal](https://portal.azure.com) にログインし、**[すべてのサービス]** セクションで **[管理グループ]** を探します。 

管理グループに対する Azure Policy のサポートは、パブリック プレビューではまだ利用できず、数週間以内に提供される予定です。  

たとえば、仮想マシン (VM) の作成に使えるリージョンを制限するポリシーを管理グループに適用できます。 そのリージョンでの VM の作成を許可するだけで、その管理グループの下にあるすべての管理グループ、サブスクリプション、リソースに、このポリシーが適用されます。

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>管理グループとサブスクリプションの階層 

管理グループとサブスクリプションの柔軟な構造を作成し、リソースを階層に整理して、統一されたポリシーとアクセス管理を適用できます。 次の図では、部門別に整理された管理グループとサブスクリプションで構成される階層の例を示します。    

![階層](media/management-groups/MG_overview.png)

部門別にグループ化された階層を作成することで、その管理グループに属する部門に "*継承*" される [Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) のロールを割り当てることができます。 管理グループを使うと、ロールの割り当てが 1 回しか必要ないので、ワークロードが減り、エラーのリスクが軽減されます。 

### <a name="important-facts-about-management-groups"></a>管理グループに関する重要な事実
- 1 つのディレクトリでは、10,000 個の管理グループをサポートできます。 
- 管理グループのツリーは、最大 6 レベルの深さをサポートできます。
    - この制限には、ルート レベルまたはサブスクリプション レベルは含まれません。
- 各管理グループは、1 つの親のみをサポートできます。
- 各管理グループは、複数の子を持つことができます。 

## <a name="root-management-group-for-each-directory"></a>各ディレクトリのルート管理グループ

各ディレクトリには、"ルート" 管理グループと呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 ルート管理グループにより、グローバル ポリシーと RBAC の割り当てをディレクトリ レベルで適用できます。 [ディレクトリ管理者は自分自身を昇格させて](../active-directory/role-based-access-control-tenant-admin-access.md)、最初にこのルート グループの所有者にする必要があります。 グループの所有者になった管理者は、任意の RBAC ロールを他のディレクトリ ユーザーまたはグループに割り当てて、階層を管理することができます。  

### <a name="important-facts-about-the-root-management-group"></a>ルート管理グループに関する重要な事実
- ルート管理グループの名前と ID は、既定では Azure Active Directory ID に設定されます。 表示名はいつでも更新でき、Azure Portal 内での表示を変えることができます。 
- すべてのサブスクリプションと管理グループは、ディレクトリ内の 1 つのルート管理グループにまとめられます。  
    - グローバル管理用のルート管理グループにまとめられるディレクトリに、すべての項目を置くことをお勧めします。  
    - パブリック プレビューの期間中、ディレクトリ内のすべてのサブスクリプションは、自動的にはルートの子になりません。   
    - パブリック プレビューの期間中、新しいサブスクリプションは自動的には既定でルート管理グループに設定されません。 
- 他の管理グループと異なり、ルート管理グループを移動または削除することはできません。 
  
## <a name="management-group-access"></a>管理グループ アクセス

Azure 管理グループは、すべてのリソース アクセスとロール定義について、[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) をサポートします。 これらのアクセス許可は、階層内に存在する子リソースに継承されます。   

任意の[組み込み RBAC ロール](../active-directory/role-based-access-control-what-is.md#built-in-roles)を管理グループに割り当てることができますが、よく使われるロールが 4 つあります。 
- **所有者** は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。 
- **共同作成者**は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。
- **リソース ポリシーの共同作成者**は、リソースのディレクトリ内のポリシーを作成および管理できます。     
- **閲覧者** は、既存の Azure リソースを表示できます。 


## <a name="next-steps"></a>次の手順 
管理グループについて詳しくは、以下をご覧ください。 
- [管理グループを作成して Azure リソースを整理する](management-groups-create.md)
- [管理グループを変更、削除、または管理する方法](management-groups-manage.md)
- [Azure PowerShell モジュールのインストール](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 仕様の確認](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Azure CLI 拡張機能のインストール](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

