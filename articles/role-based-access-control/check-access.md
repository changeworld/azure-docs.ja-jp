---
title: クイック スタート - Azure portal を使用してユーザーに割り当てられているロールを表示する | Microsoft Docs
description: ユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てられているロールベースのアクセス制御 (RBAC) のアクセス許可を、Azure portal を使用して表示する方法について説明します。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641868"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>クイック スタート: Azure portal を使用してユーザーに割り当てられているロールを表示する

[ロールベースのアクセス制御 (RBAC)](overview.md) の **[アクセス制御 (IAM)]** ブレードを使用すると、複数のユーザー、グループ、サービス プリンシパル、マネージド ID に対するロールの割り当てを表示できますが、単独のユーザー、グループ、サービス プリンシパル、マネージド ID のロールの割り当てをすばやく表示する必要がある場合があります。 これを行う最も簡単な方法は、Azure portal の **[アクセスの確認]** 機能を使うことです。

## <a name="view-role-assignments"></a>ロールの割り当てを表示する

サブスクリプションのスコープで単独のユーザー、グループ、サービス プリンシパル、またはマネージド ID のロールの割り当てを表示するには、次の手順のようにします。

1. Azure portal で、**[すべてのサービス]**、**[サブスクリプション]** の順にクリックします。

1. お使いのサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[アクセスの確認]** タブをクリックします。

    ![アクセス制御 - [アクセスの確認] タブ](./media/check-access/access-control-check-access.png)

1. **[検索]** 一覧で、アクセス権を確認するセキュリティ プリンシパルの種類を選択します。

1. 検索ボックスに、表示名、メール アドレス、またはオブジェクト識別子のディレクトリを検索するための文字列を入力します。

    ![[アクセスの確認] の選択リスト](./media/check-access/check-access-select.png)

1. セキュリティ プリンシパルをクリックして **[割り当て]** ウィンドウを開きます。

    ![[割り当て] ウィンドウ](./media/check-access/check-access-assignments.png)

    このウィンドウでは、選択したセキュリティ プリンシパルに割り当てられているロールとスコープを確認できます。 このスコープに拒否割り当てがある場合、またはこのスコープに継承されている場合は、それが表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル: RBAC と Azure portal を使用してユーザーにアクセス権を付与する](quickstart-assign-role-user-portal.md)
