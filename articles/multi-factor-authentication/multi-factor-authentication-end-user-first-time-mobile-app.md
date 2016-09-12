<properties 
	pageTitle="Azure MFA での連絡方法としてモバイル アプリを使用する" 
	description="このページは、Azure MFA の主要な連絡方法としてモバイル アプリを使用する方法を示します。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenp" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication での連絡方法としてモバイル アプリを使用する

主要な連絡方法として Microsoft Authenticator アプリを使用する場合は、この記事を使用できます。モバイル アプリを主要な連絡方法として使用できるよう多要素認証を設定する方法について説明します。

Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## 連絡方法として Microsoft Authenticator を使用するには


- [追加のセキュリティ確認] 画面で、ドロップダウン リストから [モバイル アプリ] を選択します。


![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- [通知] または [ワンタイム パスワード] のいずれかを選択し、[設定] をクリックします。
- Microsoft Authenticator アプリがインストールされている電話でアプリを起動し、[+] をクリックして新しいアカウントを追加します。次に、[職場または学校アカウント] をタップすると、QR コード スキャナーが起動します。カメラが正常に動作しない場合は、会社情報を手動で入力することもできます。「[手動でのアカウントの追加](#adding-an-account-manually)」を参照してください。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- モバイル アプリの構成画面に表示される QR コード画像をスキャンします。[完了] をクリックして、QR コードの画面を閉じます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- QR コードをスキャンできない場合は、情報を手動で入力することができます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- 電話で、アクティブ化が開始され完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- 企業によっては、認証時に PIN の入力が求められる場合があります。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)


- この操作が完了したら [閉じる] をクリックします。この時点で、認証が完了します。
- モバイル アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。
- ドロップダウン リストからお住まいの国を指定し、国の横のボックスに携帯電話番号を入力します。[次へ] をクリックします。
- この時点で、連絡方法が設定されました。次に、非ブラウザー アプリ (Outlook 2010 以前など) のアプリ パスワードを設定します。これらのアプリを使用していない場合は、**[完了]** をクリックします。使用している場合は、次の手順に進みます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- これらのアプリを使用してる場合は、指定されたアプリ パスワードをコピーし、非ブラウザー アプリケーションに貼り付けます。Outlook および Lync など、個々のアプリケーションでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。
- [Done] をクリックします。


## 手動でのアカウントの追加
アカウントを手動で追加する場合は、アカウントの手動入力ボタンを選択します。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Azure MFA を既に使用しているアカウントの場合、バーコードが表示されているページに記載されたコードと URL を入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

アクティブ化が完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。



 

<!---HONumber=AcomDC_0831_2016-->