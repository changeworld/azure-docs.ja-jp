---
title: Azure Stack でユーザーごとにリソースへのアクセス許可を管理する | Microsoft Azure
description: サービス管理者またはテナントとして、RBAC アクセス許可を管理する方法を説明します。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: patricka
ms.reviewer: ''
ms.openlocfilehash: 2e36f52568d349aeecd47f90bf9431f096cc4fdc
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41954625"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Azure Stack のロールベースのアクセス制御でリソースへのアクセスを管理する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack はロールベースのアクセス制御 (RBAC) をサポートしますが、これは[アクセス管理のためのセキュリティ モデル](https://docs.microsoft.com/azure/role-based-access-control/overview)であり、Microsoft Azure が使用するものと同じです。 RBAC を使用して、ユーザー、グループ、またはアプリケーションによるサブスクリプション、リソース、およびサービスへのアクセスを管理することができます。

## <a name="basics-of-access-management"></a>アクセス管理の基礎

ロールベースのアクセス制御は、環境をセキュリティで保護するために使用できるきめ細やかなアクセス制御を提供します。 特定のスコープで RBAC のロールを割り当てることによって、ユーザーに必要な正しいアクセス許可をユーザーに付与します。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。 アクセス管理の詳細については、[Azure Portal でのロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview)の記事を参照してください。

### <a name="built-in-roles"></a>組み込みのロール

Azure Stack には、すべてのリソースの種類に適用できる 3 つの基本的なロールがあります:

* **所有者**は、リソースへのアクセスを含め、すべてを管理できます。
* **共同作業者**は、リソースへのアクセス以外のすべてを管理できます。
* **閲覧者**は、すべてを閲覧できますが、変更を加えることはできません。

### <a name="resource-hierarchy-and-inheritance"></a>リソース階層と継承

Azure Stack には、次のリソース階層があります:

* 各サブスクリプションは、1 つのディレクトリに属しています。
* 各リソース グループは、1 つのサブスクリプションに属しています。
* 各リソースは、1 つのリソース グループに属しています。

親スコープで付与されたアクセス権は、子スコープに継承されます。 例: 

* 閲覧者ロールをサブスクリプション スコープで Azure AD グループに割り当てると、 そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示できるようになります。
* 共同作成者ロールをリソース グループ スコープでアプリケーションに割り当てると、 アプリケーションでは、そのリソース グループ内のすべてのタイプのリソースを管理できるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

### <a name="assigning-roles"></a>ロールの割り当て

ユーザーに複数のロールを割り当てることができ、各ロールを異なるスコープに関連付けることができます。 例: 

* Subscription-1 の閲覧者ロールを TestUser-A に割り当てます。
* TestVM-1 の所有者ロールを TestUser-A に割り当てます。

Azure の[ロールの割り当て](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)の記事では、ロールの表示、割り当て、および削除について詳細が説明されています。

### <a name="resource-hierarchy-and-inheritance"></a>リソース階層と継承

Azure Stack には、次のリソース階層があります:

* 各サブスクリプションは、1 つのディレクトリに属しています。
* 各リソース グループは、1 つのサブスクリプションに属しています。
* 各リソースは、1 つのリソース グループに属しています。

親スコープで付与されたアクセス権は、子スコープに継承されます。 例: 

* **閲覧者**ロールをサブスクリプション スコープで Azure AD グループに割り当てます。 そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示できるようになります。
* **共同作成者**ロールをリソース グループ スコープでアプリケーションに割り当てます。 アプリケーションでは、そのリソース グループ内のすべてのタイプのリソースを管理できるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

### <a name="assigning-roles"></a>ロールの割り当て

ユーザーに複数のロールを割り当てることができ、各ロールを異なるスコープに関連付けることができます。 例: 

* Subscription-1 の閲覧者ロールを TestUser-A に割り当てます。
* TestVM-1 の所有者ロールを TestUser-A に割り当てます。

Azure の[ロールの割り当て](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)の記事では、ロールの表示、割り当て、および削除について詳細が説明されています。

## <a name="set-access-permissions-for-a-user"></a>ユーザーのアクセス権限の設定

次の手順では、ユーザーのアクセス許可の構成方法について説明します。

1. 管理するリソースへの所有者アクセス許可があるアカウントでサインインします。
2. 左側のナビゲーション ペインで、**[リソース グループ]** を選択します。
3. アクセス許可を設定するリソース グループの名前を選択します。
4. リソース グループのナビゲーション ペインで、**[アクセス制御 (IAM)]** を選択します。 **[Access control]**(アクセス制御) ビューに、リソース グループへのアクセス権を持つ項目が一覧表示されます。 これらの結果をフィルター処理したり、メニュー バーを使用してアクセス許可を追加または削除したりできます。
5. **[アクセス制御]** メニュー バーで、**[+ Add]**(+ 追加) を選択します。
6. **[アクセス許可の追加]** で:

   * 割り当てるロールを **[ロール]** ドロップダウン リストから選択します。
   * 割り当てるリソースを **[アクセスの割り当て先]** ドロップダウン リストから選択します。
   * ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。 ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。

7. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

[サービス プリンシパルの作成](azure-stack-create-service-principals.md)