---
title: 'チュートリアル: Azure Active Directory と Jobscience の統合 | Microsoft Docs'
description: Azure Active Directory で Jobscience を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2016
ms.author: jeedes

---
# チュートリアル: Azure Active Directory と Jobscience の統合
このチュートリアルの目的は、Azure と Jobscience の統合を示すことです。
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Jobscience でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Jobscience に割り当てた Azure AD ユーザーは、Jobscience 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Jobscience のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-jobscience-tutorial/IC784341.png "シナリオ")

## Jobscience のアプリケーション統合の有効化
このセクションでは、Jobscience のアプリケーション統合を有効にする方法を説明します。

### Jobscience のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-jobscience-tutorial/IC700994.png "アプリケーション")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-jobscience-tutorial/IC749321.png "アプリケーションの追加")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-jobscience-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")
6. **検索ボックス**に、「**jobscience**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-jobscience-tutorial/IC784342.png "アプリケーション ギャラリー")
7. 結果ウィンドウで **[Jobscience]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
   ## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Jobscience に対する認証を行えるようにする方法を説明します。  
Jobscience のシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。  
この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

### シングル サインオンを構成するには、次の手順に従います。
1. Jobscience の企業サイトに管理者としてログインします。
2. **[セットアップ]** に移動します。
   
   ![セットアップ](./media/active-directory-saas-jobscience-tutorial/IC784358.png "セットアップ")
3. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして**[マイ ドメイン]** ページを開きます。
   
   ![マイ ドメイン](./media/active-directory-saas-jobscience-tutorial/IC767825.png "マイ ドメイン")
4. ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。
   
   ![ユーザーにデプロイされたドメイン](./media/active-directory-saas-jobscience-tutorial/IC784377.png "ユーザーにデプロイされたドメイン")
5. 別の Web ブラウザー ウィンドウで、Azure クラシック ポータルにログインします。
6. **Jobscience** のアプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")
7. **[Jobscience にサインオンする方法について尋ねる]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Configure Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")
8. **[アプリ URL の構成]** ページの **[Jobscience サインイン URL]** ボックスに、"*http://company.my.salesforce.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![Configure App URL](./media/active-directory-saas-jobscience-tutorial/IC784362.png "アプリケーション URL の構成")
9. **[Jobscience でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![Configure Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")
10. Jobscience の企業サイトで、**[セキュリティ コントロール]** をクリックしてから、**[シングル サインオンの設定]** をクリックします。
    
    ![セキュリティ コントロール](./media/active-directory-saas-jobscience-tutorial/IC784364.png "セキュリティ コントロール")
11. **[シングル サインオンの設定]** セクションで、次の手順に従います。
    
    ![シングル サインオンの設定](./media/active-directory-saas-jobscience-tutorial/IC781026.png "シングル サインオンの設定")
    
    1. **[SAML Enabled]** を選択します。
    2. **[新規]** をクリックします。
12. **[SAML シングル サインオン設定の編集]** ページで、次の手順に従います。
    
    ![SAML シングル サインオンの設定](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML シングル サインオンの設定")
    
    1. [**名前**] テキスト ボックスに、構成の名前を入力します。
    2. Azure クラシック ポータルの **[Jobscience でのシングル サインオンの構成]** ダイアログ ページで **[発行者 URL]** の値をコピーし、それを **[発行者]** テキストボックスに貼り付けます。
    3. **[エンティティ ID]** ボックスに、「**https://salesforce-jobscience.com**」と入力します。
    4. **[参照]** をクリックして、Azure AD の証明書をアップロードします。
    5. **[SAML ID の種類]** として、**[アサーション には、ユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
    6. **[SAML ID の場所]** として、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。
    7. Azure クラシック ポータルの **[Jobscience でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[ID プロバイダー ログイン URL]** ボックスに貼り付けます。
    8. Azure クラシック ポータルの **[Jobscience でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダー ログアウト URL]** ボックスに貼り付けます。
    9. **[保存]** をクリックします。
13. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして**[マイ ドメイン]** ページを開きます。
    
    ![マイ ドメイン](./media/active-directory-saas-jobscience-tutorial/IC767825.png "マイ ドメイン")
14. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)**] をクリックして、**[編集]** をクリックします。
    
    ![ログイン ページのブランド](./media/active-directory-saas-jobscience-tutorial/IC767826.png "ログイン ページのブランド")
15. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、**[SAML SSO 設定]** の名前が表示されます。それを選んで、**[保存]** をクリックします。
    
    ![ログイン ページのブランド](./media/active-directory-saas-jobscience-tutorial/IC784366.png "ログイン ページのブランド")
16. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![Configure Single Sign-On](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")

SP によって開始されるシングル サインオンのログイン URL を取得するには、**[Security Controls (セキュリティ コントロール)**] メニュー セクションの **[シングル サインオンの設定]** をクリックします。

![セキュリティ コントロール](./media/active-directory-saas-jobscience-tutorial/IC784368.png "セキュリティ コントロール")

前の手順で作成した SSO プロファイルをクリックします。
このページに、会社のシングル サインオン URL が表示されます (例: *https://companyname.my.salesforce.com?so=companyid*)。

## ユーザー プロビジョニングの構成
Azure AD ユーザーが Jobscience にログインできるようにするには、そのユーザーを Jobscience にプロビジョニングする必要があります。
Jobscience の場合、プロビジョニングは手動で行います。

### ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **Jobscience** の企業サイトに管理者としてログインします。
2. [セットアップ] に移動します。
   
   ![セットアップ](./media/active-directory-saas-jobscience-tutorial/IC784358.png "セットアップ")
3. **[ユーザーの管理] の [ユーザー]** に移動します。
   
   ![Users](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")
4. **[新しいユーザー]** をクリックします。
   
   ![すべてのユーザー](./media/active-directory-saas-jobscience-tutorial/IC784370.png "すべてのユーザー")
5. **[ユーザーの編集]** ダイアログで、次の手順に従います。
   
   ![ユーザーの編集](./media/active-directory-saas-jobscience-tutorial/IC784371.png "ユーザーの編集")
   
   1. 名、姓、名、エイリアス、メール、プロビジョニングする Azure AD ユーザーのユーザー名とニックネームのプロパティを該当するボックスに入力します。
   2. **[保存]** をクリックします。
   
   > [!NOTE]
   > Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
   > 
   > 

> [!NOTE]
> 他の Jobscience ユーザー アカウント作成ツールや Jobscience から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Jobscience に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Jobscience** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-jobscience-tutorial/IC784372.png "ユーザーの割り当て")
3. テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。
   
   ![Yes](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->