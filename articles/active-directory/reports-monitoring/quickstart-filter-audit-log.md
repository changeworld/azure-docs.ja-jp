---
title: Azure AD 監査ログをフィルター処理する
description: このクイックスタートでは、Azure AD 監査ログのエントリをフィルター処理する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/11/2021
ms.author: markvi
author: MarkusVi
manager: karenhoran
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51bc001c5f6984e855adb11b1bdb7c938d2d5783
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995504"
---
# <a name="quickstart-filter-your-azure-ad-audit-log"></a>クイックスタート: Azure AD 監査ログをフィルター処理する 

Azure AD 監査ログの情報を使用すると、コンプライアンスのためのシステム アクティビティのレコードにアクセスできます。 このクイックスタートでは、監査ログで新しく作成されたユーザー アカウントを見つける方法について説明します。


## <a name="prerequisites"></a>前提条件

このクイック スタートのシナリオを完了するための要件を次に示します。

- **Azure AD テナントへのアクセス** - Azure AD テナントにアクセスできない場合は、「[Azure の無料アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)」を参照してください。 
- **Isabella Simonsen というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウド ベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)」を参照してください。

## <a name="find-the-new-user-account"></a>新しいユーザー アカウントを見つける

このセクションでは、監査ログをフィルター処理する手順について説明します。


**新しいユーザーを見つけるには:**

1. [監査ログ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)に移動します。

2. Isabella Simonsen のレコードのみを一覧表示するには:

    a. ツール バーの **[フィルターの追加]** をクリックします。
    
    ![ユーザー フィルターを追加する](./media/quickstart-analyze-sign-in/add-filters.png)   

    b. **[フィールドの選択]** リストで **[ターゲット]** を選択し、 **[適用]** をクリックします。

    c. **[ターゲット]** テキストボックスに「**Isabella Simonsen**」という **[ユーザー プリンシパル名]** を入力し、 **[適用]** をクリックします。

3. フィルター処理された項目をクリックします。

    ![フィルター処理された項目](./media/quickstart-filter-audit-log/audit-log-list.png)  

4.  **[Audit Log Details]\(監査ログの詳細\)** を確認します。
 
    ![監査ログの詳細](./media/quickstart-filter-audit-log/audit-log-details.png)  
 
  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、テスト ユーザーを削除します。 Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-a-user)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Active Directory レポートの概要](overview-reports.md)
