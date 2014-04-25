<properties linkid="develop-php-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Azure Active Directory によるシングル サインオン (PHP)" metaKeywords="Azure PHP Web アプリケーション, Azure シングル サインオン, Azure PHP Active Directory" description="Azure Active Directory によるシングル サインオンを使用する PHP Web アプリケーションを作成する方法について説明します。" metaCanonical="" services="active-directory" documentationCenter="PHP" title="PHP と Azure Active Directory による Web シングル サインオン" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

# PHP と Azure Active Directory による Web シングル サインオン

##<a name="introduction"></a>はじめに

このチュートリアルでは PHP 開発者向けに、Azure Active Directory を活用して、Office 365 顧客のユーザーに対してシングル サインオンを有効にする方法について説明します。学習内容: 

* 顧客のテナントでの Web アプリケーションのプロビジョニング
* WS-Federation を使用したアプリケーションの保護

###前提条件
次の開発環境の前提条件はこのチュートリアルで必要になります。

* [Azure Active Directory 用の PHP サンプル コード]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 (Web プラットフォーム インストーラーを使用)
* インターネット インフォメーション サービス (IIS) 7.5 (SSL が有効)
* Windows PowerShell
* [Office 365 PowerShell コマンドレット]

###目次
* [はじめに][]
* [手順 1. PHP アプリケーションを作成する][]
* [手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする][]
* [手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する][]
* [まとめ][]

##<a name="createapp"></a>手順 1. PHP アプリケーションを作成する
この手順では、リソースが保護されたシンプルな PHP アプリケーションを作成する方法について説明します。このリソースへのアクセスは、会社の STS によって管理されるフェデレーション認証を使用して許可されます (後で説明)。

1. Eclipse の新しいインスタンスを開きます。
2. **[File]** メニューの **[New]** をクリックし、**[New PHP Project]** をクリックします。
3. **[New PHP Project]** ダイアログ ボックスで、プロジェクトに *phpSample* という名前を付け、**[Finish]** をクリックします。
4. 左側の **[PHP Explorer]** メニューで *phpProject* を右クリックし、**[New]**、**[PHP File]** の順にクリックします。
5. **[New PHP File]** ダイアログ ボックスで、ファイルに **index.php** という名前を付け、**[Finish]** をクリックします。
6. 生成されたマークアップを次のコードに置き換え、**index.php** を保存します。

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. [ファイル名を指定して実行] のプロンプトで「*inetmgr*」と入力し、Enter キーを押して、**インターネット インフォメーション サービス (IIS) マネージャー**を起動します。

8. IIS マネージャーで、左側のメニューの **Sites** フォルダーを展開し、**[既定の Web サイト]** を右クリックして、**[アプリケーションの追加...]** をクリックします。

9. **[アプリケーションの追加]** ダイアログ ボックスで、**[エイリアス]** に「*phpSample*」と入力します。**[物理パス]** で PHP プロジェクトを作成したファイル パスを指定します。

10. Eclipse で、**[Run]** メニューの **[Run]** をクリックします。

11. **[Run PHP Web Application]** メニューで **[OK]** をクリックします。

12. **index.php** ページが Eclipse の新しいタブで開きます。このページには、*Index Page* というテキストが表示されているだけです。

##<a name="provisionapp"></a>手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする
この手順では、Azure Active Directory 顧客の管理者がテナントで PHP アプリケーションをプロビジョニングし、シングル サインオンを構成する方法について説明します。この手順の完了後、会社の従業員は Office 365 アカウントを使用して Web アプリケーションに対して認証できるようになります。

プロビジョニング プロセスは、アプリケーション用に新しいサービス プリンシパルを作成することから始まります。サービス プリンシパルは Azure Active Directory によって使用されて、アプリケーションがディレクトリに対して登録および認証されます。

1. Office 365 PowerShell コマンドレットをダウンロードしてインストールします (まだインストールしていない場合)。
2. **[スタート]** メニューから **Microsoft Online Services Module for Windows PowerShell ** コンソールを実行します。このコンソールには、Office 365 テナントの属性を設定するためのコマンド ライン環境が用意されています。たとえば、サービス プリンシパルの作成や変更などが可能です。
3. 必要な **MSOnlineExtended** モジュールをインポートするには、次のコマンドを入力し、Enter キーを押します。

		Import-Module MSOnlineExtended –Force
4. Office 365 ディレクトリに接続するには、会社の管理者の資格情報を指定する必要があります。次のコマンドを入力して Enter キーを押したら、プロンプトで資格情報を入力します。

		Connect-MsolService
5. この時点でアプリケーション用に新しいサービス プリンシパルを作成します。次のコマンドを入力し、Enter キーを押します。

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013"
この手順では、次のような出力が表示されます。

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Web Site
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [WACOM.NOTE] 
> この出力のうち特に生成された対称キーを保存しておく必要があります。このキーはサービス プリンシパルの作成時にしか表示されず、以降に取得することはできません。その他の値は、グラフ API を使用してディレクトリ内の情報を読み書きするために必要です。

6. 最後の手順では、アプリケーションの応答 URL を設定します。応答 URL は、認証の試行後に応答が送信される場所です。次のコマンドを入力し、Enter キーを押します。

		$replyUrl = New-MsolServicePrincipalAddresses â€“Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal â€“AppPrincipalId "7829c758-2bef-43df-a685-717089474505" â€“Addresses $replyUrl 
	
これで、Web アプリケーションはディレクトリでプロビジョニングされ、会社の従業員が Web シングル サインオンに使用できるようになりました。

##<a name="protectapp"></a>手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する
この手順では、前提条件にあるサンプル コードと共にダウンロードした Windows Identity Foundation (WIF) と simpleSAML.php ライブラリを使用して、フェデレーテッド ログインのサポートを追加する方法について説明します。また、ログイン ページを追加し、アプリケーションとディレクトリ テナントとの信頼を構成します。

1. Eclipse で、**phpSample** プロジェクトを右クリックし、**[New]**、**[File]** の順にクリックします。

2. **[New File]** ダイアログ ボックスで、ファイルに **federation.ini** という名前を付け、**[Finish]** をクリックします。

3. 新しい **federation.ini** ファイルで次の情報を入力します。この情報の値には、「手順 2.」でサービス プリンシパルの作成時に保存した情報を指定します。

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 

		<div class="dev-callout"><strong>Note</strong><p>The <b>audienceuris</b> and <b>realm</b> values must be prefaced by "spn:".</p></div>

4. Eclipse で、**phpSample** プロジェクトを右クリックし、**[New]**、**[File]** の順にクリックします。

5. **[New PHP File]** ダイアログ ボックスで、ファイルに **secureResource.php** という名前を付け、**[Finish]** をクリックします。

6. 新しい **secureResource.php** ファイルで、次のコードを入力して、**c:\phpLibraries** パスを、サンプル コードをダウンロードした root 場所に置き換えます。root 場所には **simpleSAML.php** ファイルと **federation** フォルダーがあることが必要です。

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. **index.php** ページを開き、ページがセキュリティで保護されるように内容を更新したら、そのページを保存します。

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. **[Run]** メニューの **[Run]** をクリックします。Office 365 の ID プロバイダー ページに自動的にリダイレクトされます。このページでディレクトリ テナントの資格情報を使用してログインできます。たとえば、*john.doe@fabrikam.onmicrosoft.com* を使用します。

##<a name="summary"></a>まとめ
このチュートリアルでは、シングル サインオン Azure Active Directory 機能を使用する 1 つのテナント PHP アプリケーションを作成および構成する方法について説明しました。

PHP Web サイト用に Azure Active Directory とシングル サインオンを使用する方法を示すサンプルは、<https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP> で入手できます。


[手順 1. PHP アプリケーションを作成する]: #createapp
[手順 2. 会社のディレクトリ テナントでアプリケーションをプロビジョニングする]: #provisionapp
[手順 3. WS-Federation を従業員のサインインに使用してアプリケーションを保護する]: #protectapp
[まとめ]: #summary
[はじめに]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory (Azure Active Directory によるマルチテナント クラウド アプリケーションの開発)]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/ja-jp/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 ランタイム]: http://www.microsoft.com/ja-jp/download/details.aspx?id=17331
[Office 365 PowerShell コマンドレット]: http://onlinehelp.microsoft.com/ja-jp/office365-enterprises/ff652560.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/ja-jp/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[Azure Active Directory 用の PHP サンプル コード]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP

