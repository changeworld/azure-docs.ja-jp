---
title: ISV ツールまたは Movere と連携するように Azure Migrate を準備する
description: この記事では、ISV ツールまたは Movere と連携するように Azure Migrate を準備する方法と、ツールの使用を開始する方法について説明します。
ms.topic: how-to
ms.date: 05/07/2020
ms.openlocfilehash: 9513e783d4f9d7be83f1434d4dd9011844af8993
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682652"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>ISV ツールまたは Movere を使用するための準備を行う

[ISV ツール](migrate-services-overview.md#isv-integration)または Movere を Azure Migrate プロジェクトに追加した場合、そのツールをリンクして Azure Migrate にデータを送信する前に、いくつかの手順を実行する必要があります。 

## <a name="check-azure-ad-permissions"></a>Azure AD のアクセス許可を確認する

Azure ユーザー アカウントには、次のアクセス許可が必要です。

- Azure Active Directory (Azure AD) アプリを Azure テナントに登録するためのアクセス許可
- ロールを Azure AD アプリにサブスクリプション レベルで割り当てるためのアクセス許可


### <a name="set-permissions-to-register-an-azure-ad-app"></a>Azure AD アプリを登録するためのアクセス許可を設定する

1. Azure AD で、お使いのアカウントのロールを確認します。
2. ユーザー ロールがある場合、左側にある **[ユーザー設定]** を選択し、ユーザーがアプリケーションを登録できるかどうかを確認します。 **[はい]** に設定されている場合は、Azure AD テナント内のすべてのユーザーがアプリを登録できます。 **[いいえ]** に設定されている場合は、管理者ユーザーのみがアプリを登録できます。   
3. アクセス許可がない場合、管理者ユーザーがユーザー アカウントに[アプリケーション管理者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator)ロールを提供することで、ユーザーはアプリを登録できるようになります。
4. ツールが Azure Migrate にリンクされると、管理者はユーザーのアカウントからロールを削除できます。

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>ロールを Azure AD アプリに割り当てるためのアクセス許可を設定する
 
お使いの Azure サブスクリプションでは、お使いの アカウントに、Azure AD アプリにロールを割り当てるための **Microsoft.Authorization/*/Write** アクセス権が必要です。 

1. Azure portal で **[サブスクリプション]** を開きます。
2. 関連するサブスクリプションを選択します。 表示されない場合、 **[グローバル サブスクリプション フィルター]** を選択します。 
3. **[アクセス許可]** を選択します。 次に、 **[Click here to view complete access details for this subscription] (このサブスクリプションの完全なアクセスの詳細を表示するにはここをクリック)** を選択します。
4. **[ロールの割り当て]**  >  **[View]\(表示\)** で、アクセス許可を確認します。 お使いのアカウントにアクセス許可がない場合は、サブスクリプション管理者に依頼して、[ユーザー アクセス管理者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)ロールまたは[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)ロールにご自身を追加してもらってください。
 

## <a name="start-using-the-tool"></a>ツールの使用を開始する

1. ツールのライセンスまたは無料試用版をまだお持ちでない場合は、Azure Migrate のツール エントリの **[登録]** で、 **[詳細情報]** を選択します。
2. ツールで指示に従い、ツールから Azure Migrate プロジェクトにリンクし、Azure Migrate にデータを送信します。

## <a name="next-steps"></a>次のステップ

ISV または Movere からの指示に従い、Azure Migrate にデータを送信します。

   
