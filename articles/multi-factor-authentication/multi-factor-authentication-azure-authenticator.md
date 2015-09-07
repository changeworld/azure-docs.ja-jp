<properties 
	pageTitle="携帯電話向けの Azure Authenticator アプリ"
	description="Azure Authenticatior の最新バージョンにアップグレードする方法をについて説明します。"
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


![クラウド](./media/multi-factor-authentication-azure-authenticator/addaccount.png)

これによって、QR コードをスキャンするためのカメラが起動します。QR コードをスキャンできない場合は、いつでも手動入力オプションを使用できます。

アカウントが正常にアクティブ化されたことを確認するには、アカウント ページに新しいアカウントが表示されることを確認してください。


新しいアプリに移行するすべてのアカウントについて、この手順に従います。



**手順 3:** 古い Multi-Factor Authentication アプリを電話からアンインストールします。

すべてのアカウントを新しいアプリに追加したら、古いアプリを電話からアンインストールします。

古いアプリから個々のアカウントを削除する場合は、 アカウントをタップします。そうすると、「削除」するためのオプションが表示されます。

![クラウド](./media/multi-factor-authentication-azure-authenticator/remove.png)

**その他のリソース**

* [Azure Multi-Factor Authentication (MSDN)](https://msdn.microsoft.com/library/azure/dn249471.aspx) 
* [Windows Phone](http://www.windowsphone.com/ja-JP/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)、および [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) 用の Azure Authenticator アプリ。

<!---HONumber=August15_HO9-->