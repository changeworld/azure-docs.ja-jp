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
	ms.date="05/12/2016" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication での連絡方法としてモバイル アプリを使用する

主要な連絡方法としてモバイル アプリを使用する場合は、この記事を使用できます。モバイル アプリを主要な連絡方法として使用できるよう多要素認証を設定する方法について説明します。

Azure Authenticator アプリは、[Windows Phone](http://www.windowsphone.com/ja-JP/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、[IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) で利用できます。

## 連絡方法としてモバイル アプリを使用するには


- ドロップダウン リストから [モバイル アプリ] を選択します。


![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

- [通知] または [ワンタイム パスワード] のいずれかを選択し、[設定] をクリックします。
- Azure Authenticator アプリがインストールされている電話でアプリを起動し、[バーコードのスキャン] をクリックします。Azure MFA を既に使用しているアカウントまたはサードパーティのアカウントを追加する場合は、「[手動でのアカウントの追加](#手動でのアカウントの追加)」をご覧ください。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

- モバイル アプリの構成画面で表示されるバーコード画像をスキャンします。[完了] をクリックして、バーコードの画面を閉じます。バーコードをスキャンできない場合は、情報を手動で入力することができます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

- 電話で、アクティブ化が開始され完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

- [閉じる] をクリックします。この時点で、認証が完了します。
- モバイル アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。
- ドロップダウン リストからお住まいの国を指定し、国の横のボックスに携帯電話番号を入力します。[次へ] をクリックします。
- この時点で、連絡方法が設定されました。次に、非ブラウザー アプリ (Outlook 2010 以前など) のアプリ パスワードを設定します。これらのアプリを使用していない場合は、**[完了]** をクリックします。使用している場合は、次の手順に進みます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

- これらのアプリを使用してる場合は、指定されたアプリ パスワードをコピーし、非ブラウザー アプリケーションに貼り付けます。Outlook および Lync など、個々のアプリケーションでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。
- [Done] をクリックします。


## 手動でのアカウントの追加
アカウントを手動で追加する場合は、アカウントの手動入力ボタンを選択します。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

Azure MFA を既に使用しているアカウントの場合、バーコードが表示されているページに記載されたコードと URL を入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

アクティブ化が完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。完了するには、上記の 6. 以降の手順に従います。

モバイル アプリでサード パーティのアカウントを使用している場合は、表示されたボックスにアカウント名とセキュリティ キーを入力し、アカウントをアクティブ化します。この操作を完了し、アカウントを認証したら、上記の 6. 以降の手順に従います。


![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/add3rdparty.png)

>[AZURE.NOTE]"職場アカウントの追加" と表示されている場合、これは社内参加用であり、多要素認証用ではありません。これは無視してかまいません。
 

<!---HONumber=AcomDC_0518_2016-->