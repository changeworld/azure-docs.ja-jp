<properties umbracoNaviHide="0" pageTitle="Google アプリケーションとの Azure AD 統合" metaKeywords="Azure の Google アプリケーションとの統合" description="Azure AD の Google アプリケーションとの統合について説明します。" linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Google アプリケーションとの Azure AD 統合" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Google アプリケーションとの Azure AD 統合" authors="" />

# Google アプリケーションとの Azure AD 統合
このチュートリアルの目的は、Azure と Google アプリケーションの統合を示すことです。このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

- 有効な Azure サブスクリプション
- Googe アプリケーションでのテスト テナント

Google アプリケーションでの有効なテナントがない場合は、Google Apps for Business の Web サイトで試用アカウントにサインアップすることもできます。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

Google アプリケーションのアプリケーション統合の有効化
シングル サインオンの構成
Google アプリケーションの API アクセスの有効化
カスタム ドメインの追加
ユーザー プロビジョニングの構成

# Google アプリケーションのアプリケーション統合の有効化#
このセクションでは、Google アプリケーションのアプリケーション統合を有効にする方法を説明します。

## Google アプリケーションのアプリケーション統合を有効にするには、次の手順に従います。##

1.  Azure 管理ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
2.  **[ディレクトリ]** 一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3.  アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
4.  下部にある **[追加]** をクリックして、**[アプリケーションの追加]** ダイアログを開きます。
5.  **[Azure AD でのアプリケーションの統合]** ダイアログで、**[アプリケーションへのアクセスの管理]** をクリックします。
6.  **[管理するアプリケーションの選択]** ページで、アプリケーションの一覧から **[Google Apps]** を選択します。
7.  **[完了]** をクリックして、アプリケーションを追加してダイアログ ボックスを閉じます。

# シングル サインオンの構成#
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Google アプリケーションに対する認証を行えるようにする方法を説明します。

## フェデレーション シングル サインオンを構成するのには、次の手順に従います。

1. Azure 管理ポータルで、左側のナビゲーション ウィンドウの <strong>[Active Directory]</strong> を選択して、Active Directory のダイアログ ページを開きます。
2. ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
3. 最上位メニューから <strong>[アプリケーション]</strong> を選択します。
4. アプリケーションの一覧から <strong>[Google Apps]</strong> を選択して、Google アプリケーションの構成ダイアログ ボックスを開きます。
5. <strong>[シングル サインオンの構成]</strong> ダイアログ ボックスを開くには、<strong>[シングル サインオンの構成]</strong> をクリックします。

	![Google_Tutorial_01](./media/integration-azure-google-apps/Google_Tutorial_01.png)

6. [このアプリケーションのシングル サインオンの選択] ダイアログ ページで、[モード] として [ユーザーは Azure AD のアカウントを使用して認証] を選択し、[次へ] をクリックします。

	![Google_Tutorial_02](./media/integration-azure-google-apps/Google_Tutorial_02.png)

7. <strong>[アプリケーション URL の構成]</strong> ダイアログ ページで、<strong>[GOOGLE アプリケーション テナントの URL]</strong> に Google アプリケーション テナントの URL を入力し、<strong>[次へ]</strong> をクリックします。

	![Google_Tutorial_03](./media/integration-azure-google-apps/Google_Tutorial_03.png)

8. <strong>[Google アプリケーションでのシングル サインオンの構成]</strong> ダイアログ ページで、次の手順に従い、<strong>[完了]</strong> をクリックします。
		
	+ <strong>[証明書のダウンロード]</strong> をクリックし、<strong>c:\googleapps.cer</strong> として証明書を保存します。
	+ Google アプリケーションのログイン ページを開き、サインオンします。

	![Google_Tutorial_04](./media/integration-azure-google-apps/Google_Tutorial_04.png)

	+ <strong>管理コンソール</strong>で [セキュリティ] をクリックします。

	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	[セキュリティ] アイコンが表示されていない場合は、ページの下部にある [その他のコントロール] をクリックしてください。

9. <strong>[セキュリティ]</strong> ページで、<strong>[詳細設定]</strong> をクリックします。

	![Google_Tutorial_06](./media/integration-azure-google-apps/Google_Tutorial_06.png)

10. ページの <strong>[詳細設定]</strong> セクションで、<strong>[シングル サインオンの設定]</strong> を選択します。

	![Google_Tutorial_07](./media/integration-azure-google-apps/Google_Tutorial_07.png)

11. [シングル サインオンの設定] ページで、次の手順に従います。

	![Google_Tutorial_08](./media/integration-azure-google-apps/Google_Tutorial_08.png)

		+ <strong>[シングル サインオンを有効にする]</strong> を選択します。
		+ Azure AD ポータルの <strong>[Google アプリケーションでのシングル サインオンの構成]</strong> ページで、<strong>[シングル サインオン URL]</strong> をコピーし、それを Google アプリケーションの<strong>管理コンソール</strong>の <strong>[セキュリティ]</strong> ページにある関連テキスト ボックスに貼り付けます。
		+ Azure AD ポータルの <strong>[Google アプリケーションでのシングル サインオンの構成]</strong> ページで、<strong>[シングル サインアウト サービス URL]</strong> をコピーし、それを Google アプリケーションの<strong>管理コンソール</strong>の <strong>[セキュリティ]</strong> ページにある関連テキスト ボックスに貼り付けます。
		+ Azure AD ポータルの <strong>[Google アプリケーションでのシングル サインオンの構成]</strong> ページで、<strong>[パスワードの URL の変更]</strong> をコピーし、それを Google アプリケーションの<strong>管理コンソール</strong>の <strong>[セキュリティ]</strong> ページにある関連テキスト ボックスに貼り付けます。
		+ <strong>[参照]</strong> をクリックして <strong>[検証証明書]</strong> を指定し、[アップロード] をクリックします。
		+ <strong>[保存]</strong> をクリックして、変更を保存します。


12. Azure AD ポータルの <strong>[Google アプリケーションでのシングル サインオンの構成]</strong> ページで、[完了] をクリックします。

これで、[アクセス パネル](https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633)に移動し、Google アプリケーションへのシングル サインオンをテストすることができます。

# Google アプリケーションの API アクセスの有効化
ユーザー プロビジョニングのために Azure の Active Directory を Google アプリケーションと統合する際は、Google アプリケーションのテナントに対する API アクセスを有効にする必要があります。

## Google アプリケーションの API アクセスを有効にするには

1. Google アプリケーション テナントにサインオンします。
+ 1. <strong>管理コンソール</strong>で <strong>[セキュリティ]</strong> をクリックします。
<p></p>
	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	[セキュリティ] アイコンが表示されていない場合は、管理コンソールの下部にある [その他のコントロール] をクリックします。
1. [セキュリティ] ページで、<strong>[API リファレンス]</strong> をクリックして関連する構成ダイアログ ページを開きます。
1. <strong>[API アクセスの有効化]</strong> 選択します。
<p></p>
	![Google_Tutorial_09](./media/integration-azure-google-apps/Google_Tutorial_09.png)


# カスタム ドメインの追加
Google アプリケーションでユーザー プロビジョニングを構成するには、Azure AD のドメインと Google アプリケーションのドメインが、同じ完全修飾ドメイン名 (FQDN) を持っている必要があります。ただし、このチュートリアルのシナリオをテストするために試用テナントを使用している場合などは、通常、テナントの FQDN は一致しません。この問題に対処するために、Azure AD と Google アプリケーションにカスタム ドメインを構成することができます。
カスタム ドメインの構成には、パブリック ドメインの DNS ゾーン ファイルへのアクセスが必要です。

![Google_Tutorial_10](./media/integration-azure-google-apps/Google_Tutorial_10.png)

##Azure AD でカスタム ドメインを追加するには、次の手順に従います。

1. Azure 管理ポータルで、左側のナビゲーション ウィンドウの <strong>[Active Directory]</strong> を選択して、<strong>Active Directory</strong> のダイアログ ページを開きます。
1. ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
1. 最上位メニューから <strong>[ドメイン]</strong> を選択します。
1. <strong>[ドメイン名の追加]</strong> ボックス開くには、ドメイン名を入力し、<strong>[追加]</strong>クリックします。
1. <strong>[次へ]</strong> をクリックして、<strong>[ドメイン名の確認]</strong> ダイアログ ページを開きます。

	![Google_Tutorial_11](./media/integration-azure-google-apps/Google_Tutorial_11.png)
1. <strong>[レコードの種類]</strong> を選択し、選択したレコードを DNS ゾーン ファイルに登録します。

	![Google_Tutorial_12](./media/integration-azure-google-apps/Google_Tutorial_12.png)
1. <strong>nslookup</strong> コマンドを使用して、DNS レコードが正常に登録されたかどうかを確認してください。

	![Google_Tutorial_13](./media/integration-azure-google-apps/Google_Tutorial_13.png)

## Google アプリケーションでカスタム ドメインを追加するには、次の手順に従います。

1.  Google アプリケーション テナントにサインオンします。
1. **管理コンソール**で **[ドメイン]** をクリックします。

	![Google_Tutorial_14](./media/integration-azure-google-apps/Google_Tutorial_14.png)

1. <strong>[カスタム ドメインの追加]</strong> をクリックします。

	![Google_Tutorial_15](./media/integration-azure-google-apps/Google_Tutorial_15.png)

1. <strong>[既に所有しているドメインを使用する]</strong> をクリックし、<strong>[続行]</strong> をクリックします。

	![Google_Tutorial_16](./media/integration-azure-google-apps/Google_Tutorial_16.png)

1. カスタム ドメインの名前を入力し、<strong>[続行]</strong> をクリックします。

	![Google_Tutorial_17](./media/integration-azure-google-apps/Google_Tutorial_17.png)

1. ドメインの所有権を確認する手順に従います。
	
	既にフェデレーション シングル サインオンを構成している場合は、フェデレーション シングル サインオン構成での Google アプリケーション テナントの URL を更新する必要があります。



# ユーザー プロビジョニングの構成
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Google アプリケーションに対して有効にする方法を説明します。

## ユーザー プロビジョニングを構成するには、次の手順に従います。

1. Azure 管理ポータルで、左側のナビゲーション ウィンドウの <strong>[Active Directory]</strong> を選択して、<strong>Active Directory</strong> のダイアログ ページを開きます。
1. ディレクトリの一覧で、目的のディレクトリを選択してディレクトリの構成ページを開きます。
1. 最上位メニューから <strong>[アプリケーション]</strong> を選択します。
アプリケーションの一覧から <strong>[Google Apps]</strong> を選択して、<strong>Google アプリケーション</strong>の構成ダイアログ ボックスを開きます。
1. <strong>[アカウントの同期の構成]</strong> ダイアログを開くには、<strong>[アカウントの同期の構成]</strong> をクリックします。
1. <strong>[アカウントの同期の構成]</strong> ダイアログ ページで、Google アプリケーションのドメイン名、Google アプリケーションのユーザー名、および Google アプリケーションのパスワードを入力し、<strong>[次へ]</strong> をクリックします。

	![Google_Tutorial_18](./media/integration-azure-google-apps/Google_Tutorial_18.png)

1. <strong>[確認]</strong> ダイアログ ページで、<strong>[完了]</strong> をクリックして <strong>[アカウントの同期の構成]</strong>ダイアログを閉じます。

ここで、テスト アカウントを作成して 10 分間待機し、アカウントが Google アプリケーションに同期されたことを確認します。

関連項目
[Best Practices for Managing the Application access enhancements for Azure Active Directory (Azure Active Directory のアプリケーション アクセスの強化を管理するためのベスト プラクティス)](http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx)
 

