---
title: Azure portal を使用して、ユーザー割り当てマネージド ID を管理する方法
description: ユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする方法に関する詳細な手順。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bdbe15a85ad4d2ef6918b7ab7e16942edde5096e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220334"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Azure Portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Azure リソースのマネージド ID は、Azure Active Directory で管理される ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure Portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- この記事の管理操作を実行するアカウントには、次のロールの割り当てが必要です。
    - [マネージド ID 共同作成者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロール。ユーザー割り当てマネージド ID の作成、読み取り (一覧表示)、更新、および削除します。
    - [マネージド ID オペレーター](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロール。ユーザー割り当てマネージド ID のプロパティを読み取ります (一覧表示します)。

## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を作成します。
2. 検索ボックスに "*マネージド ID*" と入力して、**[サービス]** の下で **[マネージド ID]** をクリックします。
3. **[追加]** をクリックして、**[ユーザー割り当てマネージド ID の作成]** ウィンドウの次のフィールドに値を入力します。
   - **[リソース名]**: これは、ユーザー割り当てマネージド ID の名前です。たとえば、UAI1 とします。
   - **[サブスクリプション]**: ユーザー割り当てマネージド ID を作成するサブスクリプションを選択します。
   - **[リソース グループ]**: ユーザー割り当てマネージド ID を含める新しいリソース グループを作成するか、**[既存のものを使用]** を選択して既存のリソース グループにユーザー割り当てマネージド ID を作成します。
   - **[場所]**: ユーザー割り当てマネージド ID をデプロイする場所を選択します。たとえば、**West US** です。
4. **Create** をクリックしてください。

![ユーザー割り当てマネージド ID を作成する](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>ユーザー割り当てマネージド ID を一覧表示する

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を一覧表示します。
2. 検索ボックスに "*マネージド ID*" と入力して、[サービス] の下で **[マネージド ID]** をクリックします。
3. ご使用のサブスクリプションのユーザー割り当てマネージド ID の一覧が表示されます。  ユーザー割り当てマネージド ID の詳細を確認するには、名前をクリックします。

![ユーザー割り当てマネージド ID を一覧表示する](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を削除する

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を削除します。
2. ユーザー割り当てマネージド ID を選択して、**[削除]** をクリックします。
3. 確認ボックスで、**[はい]** を選択します。

![ユーザー割り当てマネージド ID を削除する](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID にロールを割り当てる 

1. Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインして、ユーザー割り当てマネージド ID を一覧表示します。
2. 検索ボックスに "*マネージド ID*" と入力して、[サービス] の下で **[マネージド ID]** をクリックします。
3. ご使用のサブスクリプションのユーザー割り当てマネージド ID の一覧が表示されます。  ロールを割り当てるユーザー割り当てマネージド ID を選択します。
4. **[アクセス制御 (IAM)]** を選択してから、**[追加]** を選択します。

   ![ユーザー割り当てマネージド ID の開始](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. [アクセス許可を追加する] ブレードで、次の値を構成してから **[保存]** をクリックします。
   - **[ロール]** - 割り当てるロール
   - **[アクセスの割り当て先]** - ユーザー割り当てマネージド ID を割り当てるリソース
   - **[選択]** - アクセスを割り当てるメンバー
   
   ![ユーザー割り当てマネージド ID の IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  