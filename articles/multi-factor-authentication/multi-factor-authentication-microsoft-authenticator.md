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

## Microsoft Authenticator アプリにアカウントを追加する

## 新しい Microsoft Authenticator アプリへの移行

リリースにより、古い Azure Authenticator アプリが置き換えられます。Azure Authenticator アプリは引き続き機能しますが、新しい Microsoft Authenticator アプリに移行することにした場合は、この記事が役に立ちます。


## 新しい Microsoft Authenticator アプリへの移行方法 

**手順 1:** Microsoft Authenticator アプリをインストールします。

![クラウド](./media/multi-factor-authentication-azure-authenticator/home.png)

**手順 2:** 新しいアプリを使用して、アカウントをアクティブ化します。

最初に、アプリに追加するアカウントにとって便利な QR コードまたは、手動入力用のコードおよび URL があることを確認します。

> [AZURE.NOTE] QR コードの取得方法がわからない場合は、 ヘルプ デスクにサポートを要請してください。
> 
> 新しいアプリでアカウントをアクティブ化できない場合は、 ヘルプ デスクにお問い合わせください。
>


QR コードが取得できたら、アプリを起動します。「+」をクリックします。


![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

これによって、QR コードをスキャンするためのカメラが起動します。QR コードをスキャンできない場合は、いつでも手動入力オプションを使用できます。

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

- ここで Microsoft Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- バーコードをスキャンします。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/scan.png)

- アカウントがアクティブ化されるまで待ちます。

- 以上です。これで、[アカウント] ページに新しいアカウントが表示されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)


## Azure アカウントを手動で追加する方法

アカウントを手動で追加する場合は、次の手順に従います。

- まず、セキュリティ確認の設定ページに移動します。このページの表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

- [構成] ボタンをクリックします。
 
![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- バーコードを含む画面が表示されます。バーコードの下にあるコードと URL をメモしておきます。
  
![Scan barcode](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- ここで Microsoft Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 下部にある [手動で入力] ボタンをクリックします。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/scan.png)

- バーコードが表示されているページに記載されたコードと URL を入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/manual.png)

- アカウントがアクティブ化されるまで待ちます。

- 以上です。これで、[アカウント] ページに新しいアカウントが表示されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount2.png)

## Azure 以外のアカウントを手動で追加する方法

Microsoft アカウントなど、Azure 以外のアカウントを手動で追加する場合は、次の手順に従います。


- Azure 以外のアカウントの手動での追加は、QR コードをスキャンするかシークレット キーを入力することで実行できます。
- シークレット キーを手動で入力する場合は、アカウントが関連付けられているサイトからシークレット キーを取得します。たとえば、Outlook.com では、[アカウント設定] のセキュリティに関する設定に移動し、Authentication アプリの設定を選択します。[バーコードをスキャンしてシークレット キーを取得できない] を選択する必要があります。
- 

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Microsoft Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- QR キーをスキャンするか、下部にある [手動で入力] ボタンをクリックします。QR コードをスキャンする場合は次の手順をスキップします。これはアクティブ化がすぐに開始されるためです。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/scan.png)

- シークレット キーを手動で入力する場合は、アカウント名とバーコードと同じページに表示されるシークレット キーを入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/manual.png)

- アカウントがアクティブ化されるまで待ちます。これで、新しいアカウントが追加されたことがわかります。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- アカウントの下にあるコード (この場合は 875 756) を入力し、シークレット キーを受け取ったページのボックスにこのコードを入力するとこのプロセスは完了するため、[次へ] をクリックします。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Touch ID を使用してアカウントを追加する方法
iOS の Microsoft Authenticator モバイル アプリでは、Touch ID がサポートされています。Azure Multi-factor Authentication により、組織は、登録済みデバイスを所有するだけでなく、PIN を要求することもできます。この新機能を使用すると、TouchID 対応デバイスを持つ iOS ユーザーは、これ以上 PIN を入力する必要がなくなります。一度設定したら、ユーザーは、PIN を入力して [承認] をタップする代わりに、指紋をスキャンするだけです。

Microsoft Authenticator による Touch ID の設定はとても簡単で、PIN を使用して通常の検証チャレンジを完了するだけです。デバイスで Touch ID がサポートされている場合は、自動的に設定されます。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知をタップし、PIN を入力する代わりに指紋をスキャンします。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## アカウントを削除する方法

Microsoft Authenticator アプリから個々のアカウントを削除するには、アカウントをタップします。そうすると、「削除」するためのオプションが表示されます。

![Remove account](./media/multi-factor-authentication-azure-authenticator/remove.png)

<!---HONumber=AcomDC_0824_2016-->