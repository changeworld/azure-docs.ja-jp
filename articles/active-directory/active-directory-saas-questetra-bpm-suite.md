<properties
	pageTitle="チュートリアル: Azure Active Directory と Questetra BPM Suite の統合 | Microsoft Azure"
	description="Azure Active Directory と Questetra BPM Suite の間でシングル サインオンを構成する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="markusvi"/>


# チュートリアル: Azure Active Directory と Questetra BPM Suite の統合

このチュートリアルの目的は、Questetra BPM Suite と Azure Active Directory (Azure AD) を統合する方法を示すことです。<br>Questetra BPM Suite と Azure AD の統合には、次の利点があります。

- Questetra BPM Suite にアクセスする Azure AD ユーザーを制御できます。 
- ユーザーが自分の Azure AD アカウントで自動的に Questetra BPM Suite にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Active Directory ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件 

Questetra BPM Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE]このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。 

 
## シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。<br>このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Questetra BPM Suite の追加 
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの Questetra BPM Suite の追加
Azure AD への Questetra BPM Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Questetra BPM Suite を追加する必要があります。

**ギャラリーから Questetra BPM Suite を追加するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 <br><br> ![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br> ![アプリケーション][2]
4. ページの下部にある **[追加]** をクリックします。<br><br> ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。<br><br> ![アプリケーション][4]
6. 検索ボックスに、「**Questetra BPM Suite**」と入力します。<br> ![アプリケーション][5]
7. 結果ウィンドウで **[Questetra BPM Suite]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。<br>



##  Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Questetra BPM Suite ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと Questetra BPM Suite の関連ユーザーの間で、リンクの関係が確立されている必要があります。<br> このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Questetra BPM Suite の **[Username]** の値として割り当てます。
 
Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[単一の Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Questetra BPM Suite のテスト ユーザーの作成](#creating-a-halogen-software-test-user)** - Questetra BPM Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### 単一の Azure AD シングル サインオンの構成

このセクションの目的は、Azure AD ポータルで Azure AD のシングル サインオンを有効にすることと、Questetra BPM Suite アプリケーションでシングル サインオンを構成するにです。<br>

**Questetra BPM Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure AD ポータルの **Questetra BPM Suite** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。<br><br> ![Configure Single Sign-On][8]

2. **[ユーザーの Questetra BPM Suite へのアクセスを設定してください]** ページで、**[Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。<br><br> ![Azure AD Single Sign-On][9]


3. 別の Web ブラウザーのウィンドウで、管理者として **Questetra BPM Suite** 企業サイトにログインします。

4. 上部のメニューで **[システム設定]** をクリックします。<br><br> ![Azure AD Single Sign-On][10]

5. **[SingleSignOnSAML]** ページを開くには、**[SSO (SAML)]** をクリックします。 <br><br> ![Azure AD Single Sign-On][11]


6. Azure ポータルで、**[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行します。<br><br>![Configure App Settings][13]
 
    a.**Questetra BPM Suite** 企業サイトの [SP 情報] セクションで **[ACS URL]** をコピーし、**[サインオン URL]** テキスト ボックスに貼り付けます。

    b.**Questetra BPM Suite** 企業サイトの [SP 情報] セクションで **[エンティティ ID]** をコピーし、**[発行者の URL]** テキスト ボックスおよび **[応答 URL]** テキスト ボックスに貼り付けます。

    c.**Questetra BPM Suite** 企業サイトの [SP 情報] セクションで **[エンティティ ID]** をコピーし、**[応答 URL]** テキスト ボックスおよび **[応答 URL]** テキスト ボックスに貼り付けます。

    d.**[次へ]** をクリックします。

 
7. **[Questetra BPM Suite でのシングル サインオン構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。<br><br>![Configure Single Sign-On][14]


8. **Questetra BPM Suite** 企業サイトで、次の手順を実行します。 <br><br>![Configure Single Sign-On][15]

    a.**[シングル サインオンを有効にする]** を選択します。
     
    b.Azure ポータルで、**[発行者の URL]** の値をコピーし、**[エンティティ ID]** テキスト ボックスに貼り付けます。

    c.Azure ポータルで、**[シングル サインオン サービス URL]** の値をコピーし、**[サインイン ページの URL]** テキスト ボックスに貼り付けます。

    d.Azure ポータルで、**[シングル サインアウト サービス URL]** の値をコピーし、**[サインアウト ページの URL]** テキスト ボックスに貼り付けます。

    e.**[NameID 形式]** テキストボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。


    f.ダウンロードした証明書から base-64 でエンコードされたファイルを作成します。

    >[AZURE.TIP]詳細については、「[バイナリ証明書をテキスト ファイルに変換する方法](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

    g.base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[検証証明書]** テキスト ボックスに貼り付けます。

    h.**[保存]** をクリックします。


9. Azure AD ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。<br><br>![What is Azure AD Connect][17]


10. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。 <br><br>![What is Azure AD Connect][18]




### Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure ポータルで Britta Simon というテスト ユーザーを作成することです。

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 <br><br>![Azure AD のテスト ユーザーの作成][100] 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. ユーザーの一覧を表示するには、上部のメニューで **[ユーザー]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成][101]

4. **[ユーザーの追加]** ダイアログを開くには、下部にあるツール バーで **[ユーザーの追加]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成][102]

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成][103]
 
    a.**[ユーザーの種類]** として **[組織内の新しいユーザー]** を選択します。
  
    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.[次へ] をクリックします。
6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成][104] 
  
    a.**[名]** ボックスに「**Britta**」と入力します。
 
    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。<br><br>![Azure AD のテスト ユーザーの作成][105]

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。<br><br>![Azure AD のテスト ユーザーの作成][106]
  1. **[新しいパスワード]** の値を書き留めます。
  2. **[完了]** をクリックします。   
  
 
### Questetra BPM Suite のテスト ユーザーの作成

このセクションの目的は、Questetra BPM Suite で Britta Simon というユーザーを作成することです。

**Questetra BPM Suite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1.	Questetra BPM Suite 企業サイトに管理者としてサインオンします。
2.	**[システム設定]、[ユーザー一覧]、[新規ユーザー]** の順に移動します。 
3.	[新規ユーザー] ダイアログ ページで、次の手順に従います。<br><br>![テスト ユーザーの作成][300] 

    a.**[名前]** テキスト ボックスに、Britta の Azure AD でのユーザー名を入力します。

    b.**[メール]** テキスト ボックスに、Britta の Azure AD でのユーザー名を入力します。

    c.**[パスワード]** テキスト ボックスに、パスワードを入力します。

4.	**[新しいユーザーの追加]** をクリックします。



### Azure AD テスト ユーザーの割り当て

このセクションの目的は、Britta Simon に Questetra BPM Suite へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。<br><br>![What is Azure AD Connect][200]

**Questetra BPM Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure ポータルでアプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。<br><br>![What is Azure AD Connect][201]
2. アプリケーションの一覧で **[Questetra BPM Suite]** を選択します。<br><br>![What is Azure AD Connect][205]
1. 上部のメニューで **[ユーザー]** をクリックします。<br><br>![What is Azure AD Connect][202]
1. ユーザーの一覧で **[Britta Simon]** を選択します。<br><br>![What is Azure AD Connect][203]
2. 下部にあるツール バーで **[割り当て]** をクリックします。<br><br>![What is Azure AD Connect][204]



### シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。<br> アクセス パネルで Questetra BPM Suite のタイルをクリックすると、自動的に Questetra BPM Suite アプリケーションにサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=Sept15_HO3-->