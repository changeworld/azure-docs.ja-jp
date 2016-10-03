<properties
	pageTitle="Azure Multi-Factor Authentication での連絡方法としてモバイル アプリを使用する | Microsoft Azure"
	description="この記事では、Azure Multi-Factor Authentication の主要な連絡方法としてモバイル アプリを使用する方法を説明します。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication での連絡方法としてモバイル アプリを使用する

主要な連絡方法として Microsoft Authenticator アプリを使用する場合は、この記事を使用できます。モバイル アプリを主要な連絡方法として使用できるよう Azure Multi-Factor Authentication を設定する方法について説明します。

Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[iOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## 連絡方法として Microsoft Authenticator を使用する


1. **[追加のセキュリティ確認]** 画面で、ドロップダウン リストから **[モバイル アプリ]** を選択します。
2. **[通知]** または **[ワンタイム パスワード]** のいずれかを選択し、**[設定]** を選択します。

	![[追加のセキュリティ確認] 画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Microsoft Authenticator アプリがインストールされている電話でアプリを起動し、**[+]** を選択してアカウントを追加します。
4. [職場または学校アカウント] をタップします。QR コード スキャナーが開きます。カメラが正常に動作しない場合は、会社情報を手動で入力することもできます。詳細については、「[手動でのアカウントの追加](#add-an-account-manually)」を参照してください。

	![アカウントを選択する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

	![QR コードをスキャンするかアカウントを手動で入力するかを選択する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan4.png)

5. モバイル アプリを構成するために、画面に表示された QR コード画像をスキャンします。**[完了]** を選択して、QR コードの画面を閉じます。

	![QR コード画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	QR コードをスキャンできない場合は、情報を手動で入力することができます。

	![情報を手動で入力する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)

6. 電話でアクティブ化が完了したら、 **[連絡してください]** を選択します。これによって、通知コードまたは認証コードが電話に送信されます。**[認証]** を選択します。

	![[認証] を選択してサインインする画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

7. 会社によっては、検証時に暗証番号 (PIN) の入力を求められる場合があります。

	![PIN を入力するボックス](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. PIN の入力が完了したら、**[閉じる]** を選択します。この時点で、認証が完了します。
9. モバイル アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。ドロップダウン リストからお住まいの国を指定し、国名の横のボックスに携帯電話番号を入力します。**[次へ]** を選択します。
10. この時点で、連絡方法が設定されました。ここで、非ブラウザー アプリ (Outlook 2010 以前など) のアプリ パスワードを設定します。これらのアプリを使用しない場合は **[完了]** を選択します。使用する場合は、次の手順に進みます。

	![アプリ パスワードを作成する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

11. 非ブラウザー アプリを使用している場合は、指定されたアプリ パスワードをコピーし、アプリに貼り付けます。Outlook および Lync など、個々のアプリでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。
12. **[Done]** を選択します。


## 手動でのアカウントの追加
アカウントを手動で追加する場合は、次の手順に従います。

1. **[Enter account manually (アカウントの手動入力)]** ボタンを選択します。

	![コードと URL を入力する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)

	![コードと URL を入力する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

2. Azure Multi-Factor Authentication を既に使用しているアカウントの場合、バーコードが表示されているページに記載されたコードと URL を入力します。この情報は、モバイル アプリの**コード**および **URL** ボックスに移動します。

	![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. アクティブ化が完了したら、**[連絡してください]** を選択します。これによって、通知コードまたは認証コードが電話に送信されます。**[認証]** を選択します。

<!---HONumber=AcomDC_0921_2016-->