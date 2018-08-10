---
title: クイック スタート - Azure Active Directory の条件付きアクセスで保護されているクラウド アプリにアクセスする前に、使用条件への同意を要求する | Microsoft Docs
description: このクイック スタートでは、選択したクラウド アプリへのアクセスが Azure Active Directory の条件付きアクセスによって許可される前に、使用条件への同意が要求されるようにする方法を学習します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー, 使用条件
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: a7aeecc84a3629b43f2c1eb40030866a941d0d3b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627986"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>クイック スタート: クラウド アプリにアクセスする前に使用条件への同意を要求する 

環境内の特定のクラウド アプリにアクセスする前に、使用条件 (ToU) を受け入れる形式でユーザーからの同意を得る場合があります。 Azure Active Directory (Azure AD) の条件付きアクセスでは、次のものが提供されます。 

- ToU を構成する簡単な方法
- 条件付きアクセス ポリシーを介して使用条件に同意を要求するオプション  

このクイック スタートでは、環境内で選択したクラウド アプリに対して ToU への同意を必要とする [Azure AD の条件付きアクセス ポリシー](../active-directory-conditional-access-azure-portal.md)を構成する方法について説明します。

![ポリシーの作成](./media/require-tou/5555.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。



## <a name="prerequisites"></a>前提条件 

このクイック スタートのシナリオを完了するための要件を次に示します。

- **Azure AD Premium エディションへのアクセス** - Azure AD の条件付きアクセスは Azure AD Premium の機能です。 

- **Isabella Simonsen というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウド ベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users)」を参照してください。


## <a name="test-your-sign-in"></a>サインインをテストする

この手順の目的は、条件付きアクセス ポリシーがないサインイン エクスペリエンスの印象を確認することです。

**サインインをテストするには:**

1. Isabella Simonsen として [Azure portal](https://portal.azure.com/) にサインインします。

2. サインアウトします。




## <a name="create-your-terms-of-use"></a>使用条件を作成する

このセクションでは、サンプル ToU を作成する手順について説明します。 ToU を作成するときに、**[条件付きアクセス ポリシー テンプレートを使用して適用します]** の値を選択します。 **[カスタム ポリシー]** を選択すると、ToU が作成されるとすぐに、新しい条件付きアクセス ポリシーを作成するためのダイアログが開かれます。


**使用条件を作成するには:**

1. Microsoft Word で、新しいドキュメントを作成します。

2. 「**My terms of use**」と入力し、ドキュメントをコンピューターに **mytou.pdf** として保存します。

3. [Azure portal](https://portal.azure.com) に全体管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。

4. Azure Portal の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。 

    ![Azure Active Directory](./media/require-tou/02.png)

5. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/require-tou/03.png) 

6. **[管理]** セクションで、**[使用条件]** をクリックします。

    ![使用条件](./media/require-tou/04.png) 

7. 上部のメニューで **[新しい条件]** をクリックします。

    ![使用条件](./media/require-tou/05.png) 

8. **[新しい利用規約]** ページで、次の操作を行います。

    ![使用条件](./media/require-tou/112.png) 

    a. **[名前]** ボックスに「**My TOU**」と入力します。

    b. **[表示名]** ボックスに、「**My TOU**」と入力します。

    c. 使用条件 PDF ファイルをアップロードします。

    d. **[言語]** で **[英語]** を選択します。

    e. **[ユーザーは使用条件を展開する必要があります]** で、**[オン]** を選択します。

    f. **[条件付きアクセス ポリシー テンプレートを使用して適用します]** で **[カスタム ポリシー]** を選択します。

    g. **Create** をクリックしてください。
 


## <a name="create-your-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する 

このセクションでは、必要な条件付きアクセス ポリシーを作成する方法について説明します。 このクイック スタートのシナリオでは、以下を使用します。

- ToU の同意を必要とするクラウド アプリのプレースホルダーとしての Azure portal。 
- 条件付きアクセス ポリシーをテストするサンプル ユーザー。  

ポリシーに以下の内容を設定します。

|Setting |値|
|---     | --- |
|[概要] | Isabella Simonsen |
|クラウド アプリ | Microsoft Azure の管理 |
|アクセス権の付与 | My TOU |
 

![ポリシーの作成](./media/require-tou/1234.png)

 


**条件付きアクセス ポリシーを構成するには:**

1. **[新規]** ページの **[名前]** ボックスに、「**Require TOU for Isabella**」と入力します。

    ![Name](./media/require-tou/71.png)

2. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

    ![[概要]](./media/require-tou/06.png)

3. **[ユーザーとグループ]** ページで、次の手順に従います。

    ![[概要]](./media/require-tou/24.png)

    a. **[ユーザーとグループの選択]** をクリックし、**[ユーザーとグループ]** を選択します。

    b. **[選択]** をクリックします。

    c. **[選択]** ページで **[Isabella Simonsen]** を選択し、**[選択]** をクリックします。

    d. **[ユーザーとグループ]** ページで、**[完了]** をクリックします。

4. **[クラウド アプリ]** をクリックします。

    ![クラウド アプリ](./media/require-tou/08.png)

5. **[クラウド アプリ]** ページで、次の手順に従います。

    ![クラウド アプリを選択](./media/require-tou/26.png)

    a. **[アプリを選択]** をクリックします。

    b. **[選択]** をクリックします。

    c. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、**[選択]** をクリックします。

    d. **[クラウド アプリ]** ページで、**[完了]** をクリックします。


6. **[アクセス制御]** セクションで、**[許可]** をクリックします。

    ![アクセス制御](./media/require-tou/10.png)

7. **[許可]** ページで、次の手順に従います。

    ![許可](./media/require-tou/111.png)

    a. **[アクセス権の付与]** を選択します。

    a. **[My TOU]** を選択します。

    b. **[選択]** をクリックします。

8. **[ポリシーを有効にする]** セクションで **[オン]** をクリックします。

    ![ポリシーを有効にする](./media/require-tou/18.png)

9. **Create** をクリックしてください。


## <a name="evaluate-a-simulated-sign-in"></a>シミュレートされたサインインを評価する

条件付きアクセス ポリシーを構成したら、期待どおりに動作しているかどうかを確認してみましょう。 最初の手順として、条件付きアクセスの What If ポリシー ツールを使用して、テスト ユーザーのサインインをシミュレートします。 シミュレーションでは、サインインがポリシーに与える影響を推定し、シミュレーション レポートが生成されます。  

What If ポリシー評価ツールを初期化するには、次のように設定します。

- ユーザーに「**Isabella Simonsen**」 
- クラウド アプリに「**Microsoft Azure の管理**」


**[What If]** をクリックすると、シミュレート レポートが作成され、次のように表示されます。

- **[適用するポリシー]** の下に "**Require TOU for Isabella**" 
- **[制御の許可]** として "**My TOU**"

![What If ポリシー ツール](./media/require-tou/79.png)



**条件付きアクセス ポリシーを評価するには:**

1. [[条件付きアクセス - ポリシー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) ページで、上部のメニューの **[What If ]** をクリックします。  
 
    ![What If](./media/require-tou/14.png)

2. **[ユーザー]** をクリックし、**[Isabella Simonsen]** を選択し、**[選択]** をクリックします。

    ![User](./media/require-tou/15.png)

2. クラウド アプリを選択するには:

    ![クラウド アプリ](./media/require-tou/16.png)

    a. **[クラウド アプリ]** をクリックします。

    b. **[クラウド アプリ]** ページで、**[アプリの選択]** をクリックします。

    c. **[選択]** をクリックします。

    d. **[選択]** ページで **[Microsoft Azure の管理]** を選択し、**[選択]** をクリックします。

    e. [クラウド アプリ] ページで、**[完了]** をクリックします。

3. **[What If]** をクリックします。


## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

前のセクションでは、シミュレートされたサインインを評価する方法を学びました。 期待どおりに動作することを確認するには、シミュレーションに加え、条件付きアクセス ポリシーもテストすることをお勧めします。 

ポリシーをテストするには、**Isabella Simonsen** テスト アカウントを使用して [Azure Portal](https://portal.azure.com) にサインインしてみます。 使用条件への同意を求めるダイアログが表示されるはずです。

![使用条件](./media/require-tou/57.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、テスト ユーザーと条件付きアクセス ポリシーを削除します。

- Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad)」を参照してください。

- ポリシーを削除するには、ポリシーを選択し、クイック アクセス ツール バーの **[削除]** をクリックします。

    ![多要素認証](./media/require-tou/33.png)

- 使用条件を削除するには、使用条件を選択し、上部のツール バーの **[条件の削除]** をクリックします。 

    ![多要素認証](./media/require-tou/29.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [特定のアプリに対して MFA を必要にする](app-based-mfa.md)
> [セッションのリスクが検出されたときにアクセスをブロックする](app-sign-in-risk.md)

