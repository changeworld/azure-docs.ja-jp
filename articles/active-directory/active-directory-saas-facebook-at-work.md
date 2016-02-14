<properties
	pageTitle="チュートリアル: Azure Active Directory と Facebook at Work の統合 | Microsoft Azure"
	description="Azure Active Directory と Facebook at Work の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="asmalser"/>


# チュートリアル: Azure Active Directory と Facebook at Work の統合

このチュートリアルの目的は、Facebook at Work と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Facebook at Work と Azure AD の統合には、次の利点があります。

- Facebook at Work にアクセスする Azure AD ユーザーを制御できます 
- Facebook at Work へのアクセス権が付与されているユーザーには、アカウントを自動的にプロビジョニングできます
- ユーザーが自分の Azure AD アカウントで自動的に Facebook at Work にサインオン (シングル サインオン) する機能を有効にすることができます
- 1 つの場所でアカウントを管理できます 

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。


## 前提条件 

CS Stars と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンを有効にした Facebook at Work

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 


## ギャラリーから Facebook at Work を追加する
Azure AD への Facebook at Work の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Facebook at Work を追加する必要があります。

**ギャラリーから Facebook at Work を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。<br><br>![Active Directory][1]<br>

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br>![アプリケーション][2]<br>

4. ページの下部にある **[追加]** をクリックします。<br><br>![アプリケーション][3]<br>

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br>![アプリケーション][4]<br>

6. 検索ボックスに「**Facebook at Work**」と入力します。

7. 結果ウィンドウで **[Facebook at Work]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。


### Azure AD シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure Active Directory のアカウントで Facebook at Work に対する認証を行えるようにする方法を説明します。

**Facebook at Work で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1.	Microsoft Azure 管理ポータルで Facebook at Work を追加したら、**[シングル サインオンの構成]** をクリックします。

2.	**[アプリケーション URL の構成]** 画面に、ユーザーが Facebook at Work にサインインする URL を入力します。これが Facebook at Work テナント URL です (例: https://example.facebook.com/)。完了したら、**[次へ]** をクリックします。

3.	別の Web ブラウザーのウィンドウで、Facebook at Work 企業サイトに管理者としてログインします。

4. ID プロバイダーとして Azure AD を使用するように Facebook at Work を構成するには、[https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers) の手順に従ってください。

5.	完了したら、Microsoft Azure 管理ポータルが表示されているブラウザー ウィンドウに戻り、手順を完了したことを確認するチェックボックスをオンにして、**[次へ]** と **[完了]** をクリックします。


## ユーザーを Facebook at Work に自動的にプロビジョニングする

Azure AD は、割り当てられたユーザーのアカウントの詳細を Facebook at Work と自動的に同期する機能をサポートしています。この自動同期機能によって、ユーザーが初めてサインインする前に、Facebook at Work がユーザーのアクセスを承認するために必要なデータを取得することができます。また、Azure AD のアクセス権が取り消された場合、Facebook at Work からユーザーのプロビジョニングを解除することができます。

自動プロビジョニングを設定するには、Microsoft Azure 管理ポータル ウィンドウで **[アカウント プロビジョニングの構成]** をクリックします。

自動プロビジョニングの詳細な構成方法については、[https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers) を参照してください。


## ユーザーを Facebook at Work に割り当てる

プロビジョニングされた AAD ユーザーがアクセス パネルに Facebook at Work を表示できるようにするには、Microsoft Azure 管理ポータル内でアクセス権を割り当てる必要があります。

**ユーザーを Facebook at Work に割り当てるには:**

1.	Microsoft Azure 管理ポータルの Facebook at Work のスタート ページで、**[アカウントの割り当て]** をクリックします。

2.	**[表示]** メニューで、Facebook at Work にユーザーとグループのどちらを割り当てるかを選択し、チェックマーク ボタンをクリックします。

3.	結果の一覧で、Facebook at Work を割り当てるユーザーまたはグループを選択します。

4.	ページ フッターにある **[割り当て]** をクリックします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=AcomDC_0204_2016-->