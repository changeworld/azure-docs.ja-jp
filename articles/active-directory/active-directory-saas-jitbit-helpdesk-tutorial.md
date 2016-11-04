---
title: 'チュートリアル: Azure Active Directory と Jitbit Helpdesk の統合 | Microsoft Docs'
description: Azure Active Directory で Jitbit Helpdesk を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。
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
# チュートリアル: Azure Active Directory と Jitbit Helpdesk の統合
このチュートリアルでは、Azure と Jitbit Helpdesk の統合について説明します。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Jitbit Helpdesk テナント

このチュートリアルを完了すると、Jitbit Helpdesk に割り当てた Azure AD ユーザーは、Jitbit Helpdesk 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Jitbit Helpdesk のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "シナリオ")

## Jitbit Helpdesk のアプリケーション統合の有効化
このセクションでは、Jitbit Helpdesk のアプリケーション統合を有効にする方法を説明します。

### Jitbit Helpdesk のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "アプリケーション")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "アプリケーションの追加")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")
6. **検索ボックス**に、「**Jitbit Helpdesk**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "アプリケーション ギャラリー")
7. 結果ウィンドウで **[Jitbit Helpdesk]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
   ## シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Jitbit Helpdesk に対する認証を行うことができるようにする方法を説明します。この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。この手順に慣れていない場合は、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

> [!IMPORTANT]
> Jitbit Helpdesk テナントでシングル サインオンを構成できるようにするには、まず Jitbit Helpdesk テクニカル サポートに連絡してこの機能を有効にする必要があります。
> 
> 

### シングル サインオンを構成するには、次の手順に従います。
1. Azure クラシック ポータルの **[Jitbit Helpdesk]** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして **[シングルサインオンの構成]** ダイアログを開きます。
   
   ![Configure single sign-on](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")
2. **[ユーザーの Jitbit Helpdesk へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Configure single sign-on](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページの **[Jitbit Helpdesk サインイン URL]** ボックスに、"*https://\<tenant-name>.Jitbit.com*" というパターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "アプリケーション URL の構成")
4. **[Jitbit Helpdesk でのシングル サインオンの構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\Jitbit Helpdesk.cer** としてローカルに保存します。
   
   ![Configure single sign-on](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、Jitbit Helpdesk の企業サイトに管理者としてログインします。
6. 上部のツールバーで **[管理]** をクリックします。
   
   ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
7. **[全般設定]** をクリックします。
   
   ![ユーザー、会社、およびアクセス許可](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "ユーザー、会社、およびアクセス許可")
8. **[認証設定]** 構成セクションで、次の手順を実行します。
   
   ![認証設定](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "認証設定")
   
   1. **OneLogin** を指定してシングル サインオン (SSO) を使用し、**[SAML 2.0 シングル サインオンの有効化]** サインインを選択します。
   2. Azure クラシック ポータルの **[Jitbit Helpdesk でのシングル サインオンの構成]** ダイアログ ページで **[サービス プロバイダー (SP) によって開始されたエンドポイント]** の値をコピーし、**[エンドポイントの URL]** テキストボックスに貼り付けます。
   3. ダウンロードした証明書から **base-64 でエンコードされた**ファイルを作成します。
      
      > [!TIP]
      > 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
      > 
      > 
   4. base-64 でエンコードされた証明書を開き、その内容をクリップボードにコピーして、**[X.509 証明書]** テキスト ボックスに貼り付けます。
   5. **[変更を保存]** をクリックします。
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![Configure single sign-on](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
   
   ## ユーザー プロビジョニングの構成

Azure AD ユーザーが Jitbit Helpdesk にログインできるようにするには、そのユーザーを Jitbit Helpdesk にプロビジョニングする必要があります。Jitbit Helpdesk の場合、プロビジョニングは手動で行います。

### ユーザー アカウントをプロビジョニングするには、次の手順を実行します。
1. **Jitbit Helpdesk** テナントにログインします。
2. 上部のメニューで **[管理]** をクリックします。
   
   ![Administration](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
3. **[ユーザー、会社、およびアクセス許可]** をクリックします。
   
   ![ユーザー、会社、およびアクセス許可](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "ユーザー、会社、およびアクセス許可")
4. **[ユーザーの追加]** をクリックします。
   
   ![ユーザーの追加](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "ユーザーの追加")
5. [作成] セクションで、プロビジョニングする Azure AD アカウントのデータを **[ユーザー名]**、**[電子メール]**、**[名]**、**[姓]** の各テキストボックスに入力します。
   
   ![作成](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "作成")
6. **[作成]** をクリックします。

> [!NOTE]
> Helpdesk から提供されている他の Helpdesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### ユーザーを Jitbit Helpdesk に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Jitbit Helpdesk** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "ユーザーの割り当て")
3. テスト ユーザーを選択し、**[割り当て]**、**[はい]** の順にクリックして、割り当てを確定します。
   
   ![Yes](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。アクセス パネルの詳細については、「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」をご覧ください。

<!---HONumber=AcomDC_0713_2016-->