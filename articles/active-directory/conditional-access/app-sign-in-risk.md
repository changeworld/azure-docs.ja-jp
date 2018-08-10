---
title: クイック スタート - Azure Active Directory の条件付きアクセスを使用して、セッションのリスクが検出されたときにアクセスをブロックする | Microsoft Docs
description: このクイック スタートでは、Azure Active Directory (Azure AD) の条件付きアクセス ポリシーを使用して、セッションのリスクに基づいてサインインをブロックする方法について説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
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
ms.date: 07/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2bd52486a78ca103e0070d94ea423c069f845587
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627510"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>クイック スタート: Azure Active Directory の条件付きアクセスを使用して、セッションのリスクが検出されたときにアクセスをブロックする  

環境を保護された状態で維持するために、疑わしいユーザーによるサインイン アクティビティをブロックすることができます。 [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) では、各サインインを分析して、サインイン試行がユーザー アカウントの正当な所有者によって行われなかった可能性を算出します。 可能性 (低、中、高) は、[サインイン リスク レベル](conditions.md#sign-in-risk)と呼ばれる計算値の形で示されます。 サインイン リスクの条件を設定することで、特定のサインイン リスク レベルに応じた条件付きアクセス ポリシーを構成できます。 

このクイック スタートでは、構成されたサインイン リスク レベルが検出されたときにサインインをブロックする[条件付きアクセス ポリシー](../active-directory-conditional-access-azure-portal.md)を構成する方法を説明します。 

![ポリシーの作成](./media/app-sign-in-risk/1000.png)


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。



## <a name="prerequisites"></a>前提条件 

このチュートリアルのシナリオを完了するための要件を次に示します。

- **Azure AD Premium P2 エディションへのアクセス** - Azure AD Premium P1 の条件付きアクセス機能を使用する場合、P2 エディションが必要になります。これは、このクイック スタートのシナリオでは Identity Protection が必要になるためです。 

- **Identity Protection** - このクイック スタートのシナリオでは Identity Protection を有効にする必要があります。 Identity Protection を有効にする方法がわからない場合は、「[Azure Active Directory Identity Protection の有効化](../identity-protection/enable.md)」を参照してください。

- **Tor Browser** - [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) は、オンラインでの自身のプライバシー保護に役立つことを目的としています。 Identity Protection は、Tor Browser からのサインインを、中程度のリスク レベルのある**匿名 IP アドレスからのサインイン**として検出します。 詳細については、「[Azure Active Directory リスク イベント](../reports-monitoring/concept-risk-events.md)」を参照してください。  

- **Alain Charon というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウドベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-cloud-based-users)」を参照してください。


## <a name="test-your-sign-in"></a>サインインをテストする 

この手順の目的は、Tor Browser を使用してテスト アカウントでテナントにアクセスできることを確認することです。

**サインインをテストするには:**

1. [Azure portal](https://portal.azure.com) に **Alain Charon** としてサインインします。

2. サインアウトします。 


## <a name="create-your-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する 

このクイック スタートのシナリオでは、Tor Browser からのサインインを使用して、検出された**匿名 IP アドレスからのサインイン**というリスク イベントを生成します。 このリスク イベントのリスク レベルは中です。 このリスク イベントに応答するには、サインイン リスクの条件を [中] に設定します。 運用環境では、サインイン リスクの条件を高、または中および高に設定してください。     

このセクションでは、必要な条件付きアクセス ポリシーを作成する方法について説明します。 ポリシーに以下の内容を設定します。

|Setting |値|
|---     | --- |
| [概要] | Alain Charon  |
| クラウド アプリ | すべてのクラウド アプリ |
| サインイン リスク | Medium |
| 許可 | アクセスのブロック |
 

![ポリシーの作成](./media/app-sign-in-risk/130.png)

 


**条件付きアクセス ポリシーを構成するには:**

1. [Azure portal](https://portal.azure.com) に全体管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。

2. Azure Portal の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。 

    ![Azure Active Directory](./media/app-sign-in-risk/02.png)

3. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/app-sign-in-risk/03.png)
 
4. **[条件付きアクセス]** ページで、ツール バーの上部の **[追加]** をクリックします。

    ![Name](./media/app-sign-in-risk/108.png)

5. **[新規]** ページの **[名前]** ボックスに、「**中程度のリスク レベルのアクセスをブロックします**」と入力します。

    ![Name](./media/app-sign-in-risk/104.png)

6. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

    ![[概要]](./media/app-sign-in-risk/06.png)

7. **[ユーザーとグループ]** ページで、次の手順に従います。

    ![条件付きアクセス](./media/app-sign-in-risk/107.png)

    a. **[ユーザーとグループの選択]** をクリックし、**[ユーザーとグループ]** を選択します。

    b. **[選択]** をクリックします。

    c. **[選択]** ページで **[Alain Charon]** を選択し、**[選択]** をクリックします。

    d. **[ユーザーとグループ]** ページで、**[完了]** をクリックします。

8. **[クラウド アプリ]** をクリックします。

    ![クラウド アプリ](./media/app-sign-in-risk/08.png)

9. **[クラウド アプリ]** ページで、次の手順に従います。

    ![条件付きアクセス](./media/app-sign-in-risk/109.png)

    a. **［すべてのクラウド アプリ］** をクリックします。

    b. **[Done]** をクリックします。

10. **[条件]** をクリックします。 

    ![アクセス制御](./media/app-sign-in-risk/19.png)

11. **[条件]** ページで次の手順を行います。

    ![サインインのリスク レベル](./media/app-sign-in-risk/21.png)

    a. **[サインイン リスク]** をクリックします。
 
    b. **[構成]** で、**[はい]** をクリックします。

    c. [サインイン リスク レベル] で **[中]** を選択します。

    d. **[選択]** をクリックします。

    e. **[条件]** ページで、**[完了]** をクリックします。



10. **[アクセス制御]** セクションで、**[許可]** をクリックします。

    ![アクセス制御](./media/app-sign-in-risk/10.png)

11. **[許可]** ページで、次の手順に従います。

    ![条件付きアクセス](./media/app-sign-in-risk/105.png)

    a. **[アクセスのブロック]** を選択します。

    b. **[選択]** をクリックします。

12. **[ポリシーを有効にする]** セクションで **[オン]** をクリックします。

    ![ポリシーを有効にする](./media/app-sign-in-risk/18.png)

13. **Create** をクリックしてください。


## <a name="evaluate-a-simulated-sign-in"></a>シミュレートされたサインインを評価する

条件付きアクセス ポリシーを構成したら、期待どおりに動作しているかどうかを確認してみましょう。 最初の手順として、条件付きアクセスの **What If ポリシー ツール**を使用して、テスト ユーザーのサインインをシミュレートします。 シミュレーションでは、サインインがポリシーに与える影響を推定し、シミュレーション レポートが生成されます。  

このシナリオに対して **What If ポリシー ツール**を実行すると、"**中程度のリスク レベルのアクセスをブロックします**" が **[適用するポリシー]** の下に一覧表示されます。 

![User](./media/app-sign-in-risk/117.png)


**条件付きアクセス ポリシーを評価するには:**

1. [[条件付きアクセス - ポリシー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) ページで、上部のメニューの **[What If ]** をクリックします。  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. **[ユーザー]** をクリックし、**[ユーザー]** ページで **[Alan Charon]** を選択して、**[選択]** をクリックします。

    ![User](./media/app-sign-in-risk/116.png)

3. **[サインイン リスク]** で **[中]** を選択します。

    ![User](./media/app-sign-in-risk/119.png)


3. **[What If]** をクリックします。


## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

前のセクションでは、シミュレートされたサインインを評価する方法を学びました。 期待どおりに動作することを確認するには、シミュレーションに加え、条件付きアクセス ポリシーもテストすることをお勧めします。 

ポリシーをテストするには、Tor Browser を使用して、**Alan Charon** として [Azure portal](https://portal.azure.com) にサインインしてみます。 そのサインイン試行は、条件付きアクセス ポリシーによってブロックされる必要があります。

![多要素認証](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、テスト ユーザー、Tor Browser、条件付きアクセス ポリシーを削除します。

- Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-users-from-azure-ad)」を参照してください。

- ポリシーを削除するには、ポリシーを選択し、クイック アクセス ツール バーの **[削除]** をクリックします。

    ![多要素認証](./media/app-sign-in-risk/33.png)

- Tor Browser を削除する手順については、「[Uninstalling (アンインストール)](https://tb-manual.torproject.org/en-US/uninstalling.html)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [使用条件への同意を要求する](require-tou.md)
> [特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)

