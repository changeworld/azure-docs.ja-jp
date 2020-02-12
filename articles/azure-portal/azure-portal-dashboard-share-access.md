---
title: ロールベースのアクセス制御を使用した Azure portal ダッシュボードの共有
description: この記事では、Azure ポータルでロールベースのアクセス制御を使用して、ダッシュボードを共有する方法について説明します。
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900815"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>ロールベースのアクセス制御を使用して Azure ダッシュボードを共有する

構成したダッシュボードは、発行して組織内の他のユーザーと共有することができます。 ダッシュボードを第三者が表示できるようにするには、Azure の[ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)(RBAC)を使用します。 ロールにユーザーまたはユーザーのグループを割り当てます。 このロールは、ユーザーが公開されたダッシュボードを表示または変更できるかどうかを定義します。

公開されたすべてのダッシュボードは Azure リソースとして実装されます。 これらはサブスクリプション内に管理可能な項目として存在し、リソース グループに含まれています。 アクセス制御の見地からは、ダッシュボードはその他のリソース (仮想マシン、ストレージ アカウントなど) と変わりありません。

> [!TIP]
> ダッシュボードの個々のタイルには、表示されているリソースに基づいて、独自のアクセス制御の要件が適用されます。 個々のタイルのデータを保護しながら、ダッシュボードを広く共有することができます。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>ダッシュボードのアクセス制御について

ロールベースのアクセス制御 (RBAC) では、以下に示した 3 つのスコープ レベルでユーザーをロールに割り当てることができます。

* subscription
* resource group
* resource

割り当てたアクセス許可は、サブスクリプションからリソースへと継承されます。 発行したダッシュボードはリソースです。 公開されたダッシュボードに適用するサブスクリプションのロールにユーザーが既に割り当てられている場合があります。

Azure サブスクリプションがあり、チームのさまざまなメンバーに、サブスクリプションの*所有者*、*共同作成者*、または*閲覧者*ロールが割り当てられているとします。 所有者または共同作成者であるユーザーは、サブスクリプション内でダッシュボードを一覧表示、表示、作成、変更、または削除できます。 閲覧者であるユーザーは、ダッシュボードの一覧表示と表示はできますが、変更と削除はできません。 閲覧者アクセス許可を持つユーザーは、問題のトラブルシューティング時などに、公開されたダッシュボードをローカルで編集できますが、それらの変更をサーバーに公開することはできません。 ユーザーはダッシュボードのプライベートコピーを自分で作成できます。

複数のダッシュボードを含むリソースグループや個々のダッシュボードにアクセス許可を割り当てることもできます。 たとえば一連のユーザーについて、サブスクリプション レベルでは権限を制限しつつ、特定のダッシュボードについては、それよりも大きなアクセス権を割り当てることができます。 これらのユーザーをそのダッシュボードのロールに割り当てます。

## <a name="publish-dashboard"></a>ダッシュボードの発行

サブスクリプション内のユーザー グループと共有するダッシュボードを構成するダッシュボードを構成するとします。 次の手順では、ダッシュボードを Storage Managers というグループとダッシュボードを共有する方法を示します。 グループには好きな名前を付けることができます。 詳しくは、[Azure Active Directory でのグループの管理](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)を参照してください。

アクセス権を割り当てる前に、ダッシュボードを発行する必要があります。

1. ダッシュボードで **[共有]** を選択します。

    ![ダッシュボードの共有を選択する](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. **共有 + アクセス制御**で、 **[発行]** を選択します。

    ![ダッシュボードの発行](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     デフォルトでは、共有すると**ダッシュボード**という名前のリソース グループにダッシュボードが発行されます。

ダッシュボードが発行されました。 サブスクリプションから継承したアクセス許可が適切であれば、それ以上何もする必要はありません。 組織内の他のユーザーは、サブスクリプション レベルのロールに基づいてダッシュボードにアクセスし、変更できます。

## <a name="assign-access-to-a-dashboard"></a>ダッシュボードへのアクセス権の割り当て

そのダッシュボードのロールにユーザーのグループを割り当てることができます。

1. ダッシュボードを公開した後、**共有 + アクセス制御**で**ユーザーの管理**を選択します。

    ![ダッシュボードのユーザーを管理する](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    ダッシュボードから**共有 + アクセス制御**にアクセスするには、**共有**または**共有解除**オプションを選択してください。

1. このダッシュボードにすでにロールが割り当てられている既存のユーザーを表示するには、**ロール割り当て**を選択します。

1. 新しいユーザーまたはグループを追加するには、 **[追加]** を選択します。

    ![ダッシュボードにアクセスするためのユーザーを追加する](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 付与する権限を表すロールを選択します。 この例では、 **[共同作成者]** を選択します。

1. ロールに割り当てるユーザーまたはグループを選択します。 目的のユーザーまたはグループが一覧に表示されない場合は、検索ボックスを使用します。 使用できるグループの一覧は、Active Directory で作成したグループによって異なります。

1. ユーザーまたはグループを追加したら、 **[OK]** を選択します。

    新しい割り当てがユーザーの一覧に追加されます。 その**アクセス**は、**継承済み**ではなく**割り当て済み**としてリストされています。

    ![assigned roles](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>次のステップ

* 役割のリストについては、[Azure リソースの組み込みの役割](../role-based-access-control/built-in-roles.md)を参照してください。
* リソースの管理については、[Azure portal を使用したAzureリソースの管理](resource-group-portal.md)を参照してください。

