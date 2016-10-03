<properties
	pageTitle="携帯電話向けの Microsoft Authenticator アプリ | Microsoft Azure"
	description="Azure Authenticatior の最新バージョンにアップグレードする方法について説明します。"
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
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Microsoft Authenticator

Microsoft Authenticator アプリは、Azure アカウント (bsimon@contoso.onmicrosoft.com など)、オンプレミス職場アカウント (bsimon@contoso.com など)、または Microsoft アカウント (bsimon@outlook.com など) でセキュリティ レベルを追加で提供します。

アプリは次のいずれかの方法で機能します。

- **通知**。スマートフォンまたはタブレットに通知をプッシュして、アカウントへの不正アクセスを防止したり、不正なトランザクションを停止させることができます。通知を確認し、適切である場合は **[認証]** を選択するだけです。適切でない場合は、**[拒否]** を選択します。拒否通知について詳しくは、「Multi-Factor Authentication における [認証を拒否して不正を通報] 機能の使用法」をご覧ください。

- **パスワードと検証コード**。アプリをソフトウェア トークンとして使用して、OAuth 検証コードを生成できます。入力を求められたら、アプリによって提供されたコードを、ユーザー名、パスワードと共にサインイン画面に入力します。検証コードにより、2 番目の形式の認証が行われます。

Microsoft Authenticator アプリのリリースに伴い、古い Azure Authenticator アプリが置き換えられます。Azure Authenticator アプリは引き続き機能しますが、新しい Microsoft Authenticator アプリに移行することにした場合は、この記事が役に立ちます。

## アプリのインストール

Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## アプリへのアカウントの追加

Microsoft Authenticator アプリに追加する各アカウントについて、次のいずれかの手順を実行します。

### QR コード スキャナーを使ってアプリにアカウントを追加する

1. セキュリティ確認の設定画面に移動します。この画面の表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

2. **[構成]** をクリックします。

	![セキュリティ確認の設定画面の [構成] ボタン](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	QR コードを記載した画面が表示されます。

	![QR コードを提供する画面](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Microsoft Authenticator アプリを開きます。**[アカウント]** 画面で、**[+]** を選択し、職場アカウントまたは学校アカウントの追加を指定します。

	![アカウント画面と正符号](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![会社または学校のアカウントを指定する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. カメラを使って QR コードをスキャンし、**[完了]** を選択して QR コードの画面を閉じます。

	![QR コードをスキャンする画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

	カメラが適切に動作しない場合は、QR コードと URL を手動で入力できます。詳細については、「[アプリにアカウントを手動で追加する](#add-an-account-to-the-app-manually)」を参照してください。

5. アカウントがアクティブ化されるまで待ちます。アクティブ化が完了したら、**[連絡してください]** を選択します。これによって、通知コードまたは認証コードが電話に送信されます。**[認証]** を選択します。

	![[認証] を選択してサインインする画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. 会社によっては、検証時に暗証番号 (PIN) の入力を求められる場合があります。

	![PIN を入力するボックス](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. PIN の入力が完了したら、**[閉じる]** を選択します。この時点で、認証が完了します。
8. アプリにアクセスできなくなった場合に備えて、携帯電話番号を入力しておくことをお勧めします。ドロップダウン リストからお住まいの国を指定し、国名の横のボックスに携帯電話番号を入力します。**[次へ]** を選択します。
9. この時点で、連絡方法が設定されました。ここで、非ブラウザー アプリ (Outlook 2010 以前など) のアプリ パスワードを設定します。これらのアプリを使用しない場合は **[完了]** を選択します。使用する場合は、次の手順に進みます。

	![アプリ パスワードを作成する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. 非ブラウザー アプリを使用している場合は、指定されたアプリ パスワードをコピーし、アプリに貼り付けます。Outlook および Lync など、個々のアプリでの手順については、「電子メールのパスワードをアプリ パスワードに変更する」と「アプリケーションのパスワードをアプリ パスワードに変更する」を参照してください。
11. **[完了]** を選択します。

これで、**[アカウント]** 画面に新しいアカウントが表示されます。

![[アカウント] 画面](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### アプリにアカウントを手動で追加する

1. セキュリティ確認の設定画面に移動します。この画面の表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

2. **[構成]** をクリックします。

	![セキュリティ確認の設定画面の [構成] ボタン](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

	QR コードを記載した画面が表示されます。コードと URL を書き留めます。

	![QR コードと URL を提供する画面](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Microsoft Authenticator アプリを開きます。**[アカウント]** 画面で、**[+]** を選択し、職場アカウントまたは学校アカウントの追加を指定します。

	![アカウント画面と正符号](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

	![会社または学校のアカウントを指定する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. スキャナーで、**[enter code manually (コードを手動で入力)]** を選択します。

	![QR コードをスキャンする画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. アプリの適切なボックスにコードと URL を入力します。

	![コードと URL を入力する画面](./media/multi-factor-authentication-azure-authenticator/manual.png)

	![コードと URL を入力する画面](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. アカウントがアクティブ化されるまで待ちます。アクティブ化が完了したら、**[連絡してください]** を選択します。これによって、通知コードまたは認証コードが電話に送信されます。**[認証]** を選択します。

これで、**[アカウント]** 画面に新しいアカウントが表示されます。

![[アカウント] 画面](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### Touch ID を使ってアプリにアカウントを追加する

iOS の Microsoft Authenticator アプリでは、Touch ID がサポートされています。Azure Multi-Factor Authentication により、組織はデバイスに PIN を要求することができます。Touch ID では、iOS ユーザーは PIN を入力する必要がありません。代わりに、指紋をスキャンして、**[承認]** を選択します。

Microsoft Authenticator による Touch ID の設定はシンプルです。PIN を使用して通常の検証チャレンジを完了するだけです。デバイスで Touch ID がサポートされている場合は、Microsoft Authenticator によりそのアカウントに Touch ID が自動的に設定されます。

![Touch ID セットアップの検証](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知を選択し、PIN を入力する代わりに指紋をスキャンします。

![プッシュ通知](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## 古い Azure の認証アプリのアンインストール

すべてのアカウントを新しいアプリに追加したら、古いアプリを電話からアンインストールできます。

## アカウントの削除

Microsoft Authenticator アプリからアカウントを削除するには、アカウントを選択し、**[削除]** を選択します。

![[削除] ボタン](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0921_2016-->