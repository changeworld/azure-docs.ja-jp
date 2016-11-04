---
title: 'チュートリアル: Azure Active Directory と Freshdesk の統合 | Microsoft Docs'
description: Azure Active Directory で Freshdesk を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。
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
# チュートリアル: Azure Active Directory と Freshdesk の統合
このチュートリアルでは、Azure と Freshdesk の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Freshdesk テナント

このチュートリアルを完了すると、Freshdesk に割り当てた Azure AD ユーザーは、Freshdesk 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Freshdesk のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "シナリオ")

## Freshdesk のアプリケーション統合の有効化
このセクションでは、Freshdesk のアプリケーション統合を有効にする方法について説明します。

### Freshdesk のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
   ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "アプリケーション")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "アプリケーションの追加")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")
6. **検索ボックス**に、「**Freshdesk**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "アプリケーション ギャラリー")
7. 結果ウィンドウで **[Freshdesk]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
   
   ## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Freshdesk に対する認証を行うことができるようにする方法を説明します。Freshdesk にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。この手順に慣れていない場合は、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

### シングル サインオンを構成するには、次の手順に従います。
1. Azure クラシック ポータルの **Freshdesk** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configure Single Sign-On")
2. **[ユーザーの Freshdesk へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Freshdesk サインイン URL]** ボックスに、"*https://\<テナント名>.Freshdesk.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![Configure App URL](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "アプリケーション URL の構成")
4. **[Freshdesk でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルを **c:\\Freshdesk.cer** としてローカルに保存します。
   
   ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。
6. 上部のメニューで **[管理者]** をクリックします。
   
   ![管理者](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "管理者")
7. **[全般設定]** タブで [**セキュリティ]** をクリックします。
   
   ![Security](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")
8. **[セキュリティ]** セクションで、次の手順を実行します。
   
   ![シングル サインオン](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "シングル サインオン")
   
   1. **[シングル サインオン (SSO)]** で **[オン]** を選択します。
   2. **[SAML SSO]** を選択します。
   3. Azure クラシック ポータルの **[Freshdesk でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[SAML ログイン URL]** ボックスに貼り付けます。
   4. Azure クラシック ポータルの **[Freshdesk でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
   5. エクスポートした証明書から **[拇印]** の値をコピーして、**[セキュリティ証明書フィンガープリント]** ボックスに貼り付けます。
      
      > [!TIP]
      > 詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。
      > 
      > 
   6. **[保存]** をクリックします。
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configure Single Sign-On")
   
   ## ユーザー プロビジョニングの構成

Azure AD ユーザーが Freshdesk にログインできるようにするには、そのユーザーを Freshdesk にプロビジョニングする必要があります。Freshdesk の場合、プロビジョニングは手動で行います。

### ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. **Freshdesk** テナントにログインします。
2. 上部のメニューで **[管理者]** をクリックします。
   
   ![管理者](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "管理者")
3. **[全般設定]** タブで [**エージェント]** をクリックします。
   
   ![エージェント](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "エージェント")
4. **[新しいエージェント]** をクリックします。
   
   ![新しいエージェント](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "新しいエージェント")
5. [エージェント情報] ダイアログで、次の手順を実行します。
   
   ![エージェント情報](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "エージェント情報")
   
   1. **[フル ネーム]** ボックスに、プロビジョニングする Azure AD のアカウントの名前を入力します。
   2. **[電子メール]** ボックスに、プロビジョニングする Azure AD アカウントの Azure AD 電子メール アドレスを入力します。
   3. **[タイトル]** ボックスに、プロビジョニングする Azure AD アカウントのタイトルを入力します。
   4. **[エージェント ロール]** を選択し、**[割り当て]** をクリックします。
   5. **[保存]** をクリックします。
      
      > [!NOTE]
      > Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
      > 
      > 

> [!NOTE]
> Freshdesk から提供されている他の Freshdesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Freshdesk に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Freshdesk** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "ユーザーの割り当て")
3. テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。
   
   ![Yes](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を参照してください。

<!---HONumber=AcomDC_0713_2016-->