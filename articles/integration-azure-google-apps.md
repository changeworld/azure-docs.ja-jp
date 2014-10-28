<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Azure AD と Google Apps との統合

このチュートリアルの目的は、Azure と Google アプリケーションの統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

-   有効な Azure サブスクリプション
-   Googe Apps でのテスト テナント

Googe Apps での有効なテナントがない場合は、Google Apps for Business の Web サイトで試用アカウントにサインアップすることもできます。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

Google Apps のアプリケーション統合の有効化 シングル サインオンの構成 Google Apps の API アクセスの有効化 カスタム ドメインの追加 ユーザー プロビジョニングの構成

# Google アプリケーションのアプリケーション統合の有効化

このセクションでは、Google アプリケーションのアプリケーション統合を有効にする方法を説明します。

## Google アプリケーションのアプリケーション統合を有効にするには、次の手順に従います。

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
2.  **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
4.  下部にある **[追加]** をクリックして、**[アプリケーションの追加]** ダイアログを開きます。
5.  **[Azure AD でのアプリケーションの統合]** ダイアログで、**[アプリケーションへのアクセスの管理]** をクリックします。
6.  **[管理するアプリケーションの選択]** ページで、アプリケーションの一覧から **[Google Apps]** を選択します。
7.  **[完了]** をクリックして、アプリケーションを追加してダイアログ ボックスを閉じます。

# シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Google アプリケーションに対する認証を行えるようにする方法を説明します。

## フェデレーション シングル サインオンを構成するには、次の手順に従います。

1.  Azure 管理ポータルで、左側のナビゲーション ウィンドウの **[Active Directory]** を選択して、Active Directory のダイアログ ページを開きます。
2.  ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
3.  最上位メニューから **[アプリケーション]** を選択します。
4.  アプリケーションの一覧から **[Google Apps]** を選択して、Google Apps の構成ダイアログ ボックスを開きます。
5.  **[シングル サインオンの構成]** ダイアログ ボックスを開くには、**[シングル サインオンの構成]** をクリックします。

    ![Google\_Tutorial\_01][Google\_Tutorial\_01]

6.  [このアプリケーションのシングル サインオンの選択] ダイアログ ページで、[モード] として [ユーザーは Azure AD のアカウントを使用して認証] を選択し、[次へ] をクリックします。

    ![Google\_Tutorial\_02][Google\_Tutorial\_02]

7.  **[アプリケーション URL の構成]** ダイアログ ページで、**[Google Apps テナントの URL]** に Google Apps テナントの URL を入力し、**[次へ]** をクリックします。

    ![Google\_Tutorial\_03][Google\_Tutorial\_03]

8.  **[Google Apps でのシングル サインオンの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします。

    -   **[証明書のダウンロード]** をクリックし、**c:\\googleapps.cer** として証明書を保存します。
    -   Google Apps のログイン ページを開き、サインオンします。

    ![Google\_Tutorial\_04][Google\_Tutorial\_04]

    -   **管理コンソール**で [セキュリティ] をクリックします。

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    [セキュリティ] アイコンが表示されていない場合は、ページの下部にある [その他のコントロール] をクリックしてください。

9.  **[セキュリティ]** ページで、**[詳細設定]** をクリックします。

    ![Google\_Tutorial\_06][Google\_Tutorial\_06]

10. ページの **[詳細設定]** セクションで、**[シングル サインオンの設定]** を選択します。

    ![Google\_Tutorial\_07][Google\_Tutorial\_07]

11. [シングル サインオンの設定] ページで、次の手順に従います。

    ![Google\_Tutorial\_08][Google\_Tutorial\_08]

        + Select <strong>Enable Single Sign-on</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>SINGLE SIGN-ON URL</strong>, and then paste it into the related textbox on the <strong>Security page</strong> in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Single sign-out service URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + On the <strong>Configure single sign-on at Google Apps</strong> page in the Azure AD Portal, copy the <strong>Change password URL</strong>, and then paste it into the related textbox on the <strong>Security</strong> page in the Google Apps <strong>Admin console</strong>.
        + Click the <strong>Browse</strong> button to locate the <strong>Verification certificate</strong>, and then click Upload.
        + Click <strong>Save</strong> changes.

12. Azure AD ポータルの **[Google Apps でのシングル サインオンの構成]** ページで、[完了] をクリックします。

これで、[アクセス パネル][アクセス パネル]に移動し、Google アプリケーションへのシングル サインオンをテストすることができます。

# Google アプリケーションの API アクセスの有効化

ユーザー プロビジョニングのために Azure の Active Directory を Google アプリケーションと統合する際は、Google アプリケーションのテナントに対する API アクセスを有効にする必要があります。

## Google Apps の API アクセスを有効にするには

1.  Google Apps テナントにサインオンします。
2.  **管理コンソール**で **[セキュリティ]** をクリックします。

    ![Google\_Tutorial\_05][Google\_Tutorial\_05]

    [セキュリティ] アイコンが表示されていない場合は、管理コンソールの下部にある [その他のコントロール] をクリックします。

3.  [セキュリティ] ページで、**[API リファレンス]** をクリックして関連する構成ダイアログ ページを開きます。
4.  **[API アクセスの有効化]** 選択します。

    ![Google\_Tutorial\_09][Google\_Tutorial\_09]

    </p>

# カスタム ドメインの追加

Google アプリケーションでユーザー プロビジョニングを構成するには、Azure AD のドメインと Google アプリケーションのドメインが、同じ完全修飾ドメイン名 (FQDN) を持っている必要があります。ただし、このチュートリアルのシナリオをテストするために試用テナントを使用している場合などは、通常、テナントの FQDN は一致しません。この問題に対処するために、Azure AD と Google Apps にカスタム ドメインを構成することができます。
カスタム ドメインの構成には、パブリック ドメインの DNS ゾーン ファイルへのアクセスが必要です。

![Google\_Tutorial\_10][Google\_Tutorial\_10]

## Azure AD でカスタム ドメインを追加するには、次の手順に従います。

1.  Azure 管理ポータルで、左側のナビゲーション ウィンドウの **[Active Directory]** を選択して、**Active Directory** のダイアログ ページを開きます。
2.  ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
3.  最上位メニューから **[ドメイン]** を選択します。
4.  **[ドメイン名の追加]** ボックスを開くには、ドメイン名を入力し、**[追加]** をクリックします。
5.  **[次へ]** をクリックして、**[ドメイン名の確認]** ダイアログ ページを開きます。

    ![Google\_Tutorial\_11][Google\_Tutorial\_11]

6.  **[レコードの種類]** を選択し、選択したレコードを DNS ゾーン ファイルに登録します。

    ![Google\_Tutorial\_12][Google\_Tutorial\_12]

7.  **nslookup** コマンドを使用して、DNS レコードが正常に登録されたかどうかを確認してください。

    ![Google\_Tutorial\_13][Google\_Tutorial\_13]

## Google Apps でカスタム ドメインを追加するには、次の手順に従います。

1.  Google Apps テナントにサインオンします。
2.  **管理コンソール**で **[ドメイン]** をクリックします。

    ![Google\_Tutorial\_14][Google\_Tutorial\_14]

3.  **[カスタム ドメインの追加]** をクリックします。

    ![Google\_Tutorial\_15][Google\_Tutorial\_15]

4.  **[既に所有しているドメインを使用する]** をクリックし、**[続行]** をクリックします。

    ![Google\_Tutorial\_16][Google\_Tutorial\_16]

5.  カスタム ドメインの名前を入力し、**[続行]** をクリックします。

    ![Google\_Tutorial\_17][Google\_Tutorial\_17]

6.  ドメインの所有権を確認する手順に従います。

    既にフェデレーション シングル サインオンを構成している場合は、フェデレーション シングル サインオン構成での Google アプリケーション テナントの URL を更新する必要があります。

# ユーザー プロビジョニングの構成

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Google アプリケーションに対して有効にする方法を説明します。

## ユーザー プロビジョニングを構成するには、次の手順に従います。

1.  Azure 管理ポータルで、左側のナビゲーション ウィンドウの **[Active Directory]** を選択して、**Active Directory** のダイアログ ページを開きます。
2.  ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
3.  最上位メニューから **[アプリケーション]** を選択します。
    アプリケーションの一覧から **[Google Apps]** を選択して、**Google Apps** の構成ダイアログ ボックスを開きます。
4.  **[アカウントの同期の構成]** ダイアログを開くには、**[アカウントの同期の構成]** をクリックします。
5.  **[アカウントの同期の構成]** ダイアログ ページで、Google Apps のドメイン名、Google Apps のユーザー名、および Google Apps のパスワードを入力し、**[次へ]** をクリックします。

    ![Google\_Tutorial\_18][Google\_Tutorial\_18]

6.  **[確認]** ダイアログ ページで、**[完了]** をクリックして **[アカウントの同期の構成]**ダイアログを閉じます。

ここで、テスト アカウントを作成して 10 分間待機し、アカウントが Google アプリケーションに同期されたことを確認します。

関連項目
[Azure Active Directory のアプリケーション アクセスの強化を管理するためのベスト プラクティス アプリケーション アクセス][Azure Active Directory のアプリケーション アクセスの強化を管理するためのベスト プラクティス アプリケーション アクセス]

  [Google\_Tutorial\_01]: ./media/integration-azure-google-apps/Google_Tutorial_01.png
  [Google\_Tutorial\_02]: ./media/integration-azure-google-apps/Google_Tutorial_02.png
  [Google\_Tutorial\_03]: ./media/integration-azure-google-apps/Google_Tutorial_03.png
  [Google\_Tutorial\_04]: ./media/integration-azure-google-apps/Google_Tutorial_04.png
  [Google\_Tutorial\_05]: ./media/integration-azure-google-apps/Google_Tutorial_05.png
  [Google\_Tutorial\_06]: ./media/integration-azure-google-apps/Google_Tutorial_06.png
  [Google\_Tutorial\_07]: ./media/integration-azure-google-apps/Google_Tutorial_07.png
  [Google\_Tutorial\_08]: ./media/integration-azure-google-apps/Google_Tutorial_08.png
  [アクセス パネル]: https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633
  [Google\_Tutorial\_09]: ./media/integration-azure-google-apps/Google_Tutorial_09.png
  [Google\_Tutorial\_10]: ./media/integration-azure-google-apps/Google_Tutorial_10.png
  [Google\_Tutorial\_11]: ./media/integration-azure-google-apps/Google_Tutorial_11.png
  [Google\_Tutorial\_12]: ./media/integration-azure-google-apps/Google_Tutorial_12.png
  [Google\_Tutorial\_13]: ./media/integration-azure-google-apps/Google_Tutorial_13.png
  [Google\_Tutorial\_14]: ./media/integration-azure-google-apps/Google_Tutorial_14.png
  [Google\_Tutorial\_15]: ./media/integration-azure-google-apps/Google_Tutorial_15.png
  [Google\_Tutorial\_16]: ./media/integration-azure-google-apps/Google_Tutorial_16.png
  [Google\_Tutorial\_17]: ./media/integration-azure-google-apps/Google_Tutorial_17.png
  [Google\_Tutorial\_18]: ./media/integration-azure-google-apps/Google_Tutorial_18.png
  [Azure Active Directory のアプリケーション アクセスの強化を管理するためのベスト プラクティス アプリケーション アクセス]: http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx
