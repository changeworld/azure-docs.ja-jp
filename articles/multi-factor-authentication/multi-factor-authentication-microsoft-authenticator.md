<properties 
	pageTitle="携帯電話向けの Microsoft Authenticator アプリ" 
	description="Azure Authenticatior の最新バージョンにアップグレードする方法について説明します。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="femila" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="billmath"/>

# Microsoft Authenticator

Microsoft Authenticator アプリは、セキュリティ レベルを追加で提供します。このセキュリティ レベルは、Azure アカウント (bsimon@contoso.onmicrosoft.comなど)、オンプレミス職場アカウント (bsimon@contoso.com など)、または Microsoft アカウント (bsimon@outlook.com など) で使用することができます。

## Microsoft Authenticator アプリをダウンロードする

Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

## Microsoft Authenticator アプリのしくみ
このアプリは、ユーザーのスマート フォンまたはタブレット上の Microsoft Authenticator アプリに通知をプッシュすることによって動作します。ユーザーがアプリで [確認] をタップするだけで、認証が行われます。

このアプリでは、代わりに確認コードを使用することもできます。入力を求められたら、アプリによって提供されたコードをサインイン画面に入力します。

この 2 種類のモードとは次のような機能です。

**通知** - このモードの場合、Microsoft Authenticator アプリはアカウントに対する未承認のアクセスを防止し、不正なトランザクションを停止します。電話または登録されたデバイスに対するプッシュ通知によって行われます。通知を確認し、適切である場合は [認証] を選択するだけです。または、[拒否] を選択することもできます。あるいは、[拒否] を選択して不正通知を報告することも可能です。不正通知の報告方法については、「Multi-Factor Authentication における [認証を拒否して不正を通報] 機能の使用法」をご覧ください。

**ワンタイム パスワード** - このモードでは、Azure Authenticator アプリをソフトウェア トークンとして使用して、OATH 確認コードを生成できます。この確認コードをユーザー名とパスワードと共に入力することにより、2 番目の形式の認証が行われます。

## QR コード スキャナーを使用して Microsoft Authenticator アプリにアカウントを追加する

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


## Microsoft Authenticator アプリにアカウントを手動で追加する
アカウントを手動で追加する場合は、アカウントの手動入力ボタンを選択します。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount.png)


![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

Azure MFA を既に使用しているアカウントの場合、バーコードが表示されているページに記載されたコードと URL を入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

アクティブ化が完了したら、[連絡してください] をクリックします。これによって、通知コードまたは認証コードが電話に送信されます。[認証] をクリックします。

## 新しい Microsoft Authenticator アプリへの移行

Microsoft Authenticator アプリのリリースに伴い、古い Azure Authenticator アプリが置き換えられます。Azure Authenticator アプリは引き続き機能しますが、新しい Microsoft Authenticator アプリに移行することにした場合は、この記事が役に立ちます。


### 新しい Microsoft Authenticator アプリへの移行方法 

**手順 1:** Microsoft Authenticator アプリをインストールします。


**手順 2:** 新しいアプリを使用して、アカウントをアクティブ化します。

最初に、アプリに追加するアカウントにとって便利な QR コードまたは、手動入力用のコードおよび URL があることを確認します。

> [AZURE.NOTE] QR コードの取得方法がわからない場合は、 ヘルプ デスクにサポートを要請してください。
> 
> 新しいアプリでアカウントをアクティブ化できない場合は、 ヘルプ デスクにお問い合わせください。
>


QR コードが取得できたら、アプリを起動します。「+」をクリックします。

[職場または学校アカウント] をタップします。これによって、QR コードをスキャンするためのカメラが起動します。QR コードをスキャンできない場合は、いつでも手動入力オプションを使用できます。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

アカウントが正常にアクティブ化されたことを確認するには、アカウント ページに新しいアカウントが表示されることを確認してください。


新しいアプリに移行するすべてのアカウントについて、この手順に従います。



**手順 3:** 古い Multi-Factor Authentication アプリを電話からアンインストールします。

すべてのアカウントを新しいアプリに追加したら、古いアプリを電話からアンインストールします。



## バーコード スキャナーを使用してアカウントを追加する方法



- まず、セキュリティ確認の設定ページに移動します。このページの表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

- [構成] ボタンをクリックします。
 
![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- バーコードを含む画面が表示されます。
  
![Scan barcode](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- ここで Microsoft Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、+ 記号をクリックし、[職場または学校アカウント] をタップします。これでスキャナーが開きます。

- バーコードをスキャンします。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- アカウントがアクティブ化されるまで待ちます。

- 以上です。これで、[アカウント] ページに新しいアカウントが表示されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/accounts.png)

## Azure アカウントを手動で追加する方法

アカウントを手動で追加する場合は、次の手順に従います。

- まず、セキュリティ確認の設定ページに移動します。このページの表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

- [構成] ボタンをクリックします。
 
![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- バーコードを含む画面が表示されます。バーコードの下にあるコードと URL をメモしておきます。
  
![Scan barcode](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- ここで Microsoft Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、+ 記号をクリックし、[職場または学校アカウント] をタップします。これでスキャナーが開きます。これでスキャナーが開きます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 下部にある [またはコードを手動で入力] をクリックします。

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

- バーコードが表示されているページに記載されたコードと URL を入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/manual.png)

![セットアップ](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

- アカウントがアクティブ化されるまで待ちます。

- 以上です。これで、[アカウント] ページに新しいアカウントが表示されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/accounts.png)


## Touch ID を使用してアカウントを追加する方法
iOS の Microsoft Authenticator モバイル アプリでは、Touch ID がサポートされています。Azure Multi-factor Authentication により、組織は、登録済みデバイスを所有するだけでなく、PIN を要求することもできます。この新機能を使用すると、TouchID 対応デバイスを持つ iOS ユーザーは、これ以上 PIN を入力する必要がなくなります。一度設定したら、ユーザーは、PIN を入力して [承認] をタップする代わりに、指紋をスキャンするだけです。

Microsoft Authenticator による Touch ID の設定はとても簡単で、PIN を使用して通常の検証チャレンジを完了するだけです。デバイスで Touch ID がサポートされている場合は、自動的に設定されます。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知をタップし、PIN を入力する代わりに指紋をスキャンします。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## アカウントを削除する方法

Microsoft Authenticator アプリから個々のアカウントを削除するには、アカウントをタップします。そうすると、「削除」するためのオプションが表示されます。

![Remove account](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0831_2016-->