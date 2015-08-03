<properties 
	pageTitle="Azure MFA での連絡方法としてモバイル アプリを使用する" 
	description="このページは、Azure MFA の主要な連絡方法としてモバイル アプリを使用する方法を示します。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication での連絡方法としてモバイル アプリを使用する

主要な連絡方法としてモバイル アプリを使用する場合は、この記事を使用できます。モバイル アプリを主要な連絡方法として使用できるよう多要素認証を設定する方法について説明します。

Azure Authenticator アプリは、[Windows Phone](http://www.windowsphone.com/ja-jp/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、[IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) で利用できます。

## 連絡方法としてモバイル アプリを使用するには
<ol>
<li>ドロップダウン リストから [モバイル アプリ] を選択します。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>[通知] または [ワンタイム パスワード] のいずれかを選択し、[設定] をクリックします。</li>
<li>Azure Authenticator アプリがインストールされている電話でアプリを起動し、[バーコードのスキャン] をクリックします。</li>


<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>モバイル アプリの構成画面で表示されるバーコード画像をスキャンします。[完了] をクリックして、バーコードの画面を閉じます。バーコードをスキャンできない場合は、情報を手動で入力することができます。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>電話で、アクティブ化が開始され完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>[閉じる] をクリックします。この時点で、認証が完了します。</li>
<li>モバイル アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。<li>ドロップダウン リストからお住まいの国を指定し、国の横のボックスに携帯電話番号を入力します。[次へ] をクリックします。</li> <li>この時点で、連絡方法が設定されました。次に非ブラウザー アプリ (Outlook 2010 以降など) のアプリ パスワードを設定します。これらのアプリを使用していない場合は、**[完了]** をクリックします。使用している場合は、次の手順に進みます。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>これらのアプリを使用してる場合は、指定されたアプリ パスワードをコピーし、非ブラウザー アプリケーションに貼り付けます。Outlook および Lync など、個々のアプリケーションでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。</li>
<li>[Done] をクリックします。</li>

<!---HONumber=July15_HO4-->