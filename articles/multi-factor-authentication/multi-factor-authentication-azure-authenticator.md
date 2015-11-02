<properties 
	pageTitle="携帯電話向けの Azure Authenticator アプリ" 
	description="Azure Authenticatior の最新バージョンにアップグレードする方法について説明します。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2015" 
	ms.author="billmath"/>



# 新しい Azure Authenticator アプリへの移行

[Windows Phone](http://www.windowsphone.com/ja-JP/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、および [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) で利用可能な Azure Authenticator アプリのリリースにより、古い Multi-Factor Authentication アプリが置き換えられます。Multi-Factor Authentication アプリは引き続き機能しますが、新しい Azure Authenticator アプリに移行することにした場合は、この記事が役に立ちます。


## 新しい Azure Authenticator アプリへの移行方法 

**手順 1:** Azure Authenticator をインストールします。

![クラウド](./media/multi-factor-authentication-azure-authenticator/home.png)

**手順 2:** 新しいアプリを使用して、アカウントをアクティブ化します。

最初に、アプリに追加するアカウントにとって便利な QR コードまたは、手動入力用のコードおよび URL があることを確認します。

> [AZURE.NOTE]QR コードの取得方法がわからない場合は、 ヘルプ デスクにサポートを要請してください。
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

古いアプリから個々のアカウントを削除する場合は、 アカウントをタップします。そうすると、「削除」するためのオプションが表示されます。

![Remove account](./media/multi-factor-authentication-azure-authenticator/remove.png)

## バーコード スキャナーを使用してアカウントを追加する方法



- まず、セキュリティ確認の設定ページに移動します。このページの表示方法の詳細については、[セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。

- [構成] ボタンをクリックします。
 
![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

- バーコードを含む画面が表示されます。
  
![Scan barcode](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

- ここで Azure Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

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

- ここで Azure Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

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


- まず、アカウントが関連付けられているサイトからシークレット キーを取得する必要があります。たとえば、Outlook.com では、[アカウント設定] のセキュリティに関する設定に移動し、Authentication アプリの設定を選択します。[バーコードをスキャンしてシークレット キーを取得できない] を選択する必要があります。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/secretkey.png)

- Azure Authenticator アプリを開くと、[アカウント] ページが表示されます。ここでは、設定したアカウントの一覧が表示されます。新しいアカウントを追加する場合は、"+" (プラス) ボタンをクリックします。これでスキャナーが開きます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

- 下部にある [手動で入力] ボタンをクリックします。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/scan.png)

- バーコードが表示されているページに記載されたアカウント名とシークレット キーを入力します。この操作により、モバイル アプリのコードおよび URL ボックスに移動します。これにより、アクティブ化が開始されます。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/manual.png)

- アカウントがアクティブ化されるまで待ちます。これで、新しいアカウントが追加されたことがわかります。

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/msaccount.png)

- アカウントの下にあるコード (この場合は 875 756) を入力し、シークレット キーを受け取ったページのボックスにこのコードを入力するとこのプロセスは完了するため、[次へ] をクリックします。  

![アカウントの追加](./media/multi-factor-authentication-azure-authenticator/verify.png)

## Touch ID を使用してアカウントを追加する方法
iOS の Azure Authenticator モバイル アプリでは、Touch ID がサポートされています。Azure Multi-factor Authentication により、組織は、登録済みデバイスを所有するだけでなく、PIN を要求することもできます。この新機能を使用すると、TouchID 対応デバイスを持つ iOS ユーザーは、これ以上 PIN を入力する必要がなくなります。一度設定したら、ユーザーは、PIN を入力して [承認] をタップする代わりに、指紋をスキャンするだけです。

Azure Authenticator による Touch ID の設定はとても簡単で、PIN を使用して通常の検証チャレンジを完了するだけです。デバイスで Touch ID がサポートされている場合は、自動的に設定されます。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知をタップし、PIN を入力する代わりに指紋をスキャンします。

![Touch ID](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

<!---HONumber=Oct15_HO4-->