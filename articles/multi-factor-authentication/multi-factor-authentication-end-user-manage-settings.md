<properties 
	pageTitle="Azure MFA 設定の管理" 
	description="このドキュメントでは、どこで Azure MFA 設定の管理を行うのかについて記します。" 
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
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# Azure Multi-Factor Authentication による設定の変更
多要素認証の設定を管理するために役立つ情報を次に示します。

## 多要素認証の使用法によって異なる設定の変更方法
多要素認証の設定の変更を行える場所はいくつかあります。その場所は、多要素認証の使用法によって異なります。お客様の操作内容に最も近いものを選択してください。

多要素認証の使用方法|説明
:------------- | :------------- | 
[Office 365 で使用する](#changing-your-settings-with-office-365)| この場合には、Office 365 ポータルを使用して設定を変更します。
[わからない](#changing-your-settings-with-the-myapps-portal)|この場合には、[http://myapps.microsoft.com](http://myapps.microsoft.com) にサインインし、設定を変更します。
[Microsoft Azure で使用する](#changing-your-settings-with-microsoft-azure)| この場合には、Azure ポータルを使用して設定を変更します。


 
## Office 365 で設定を変更する


Office 365 で多要素認証を使用する場合は、Office 365 ポータルで、追加のセキュリティ確認の設定を管理できます。

### Office 365 ポータルで設定を変更するには

1. [Office 365 ポータル](https://login.microsoftonline.com/)にログオンします
2. 右上隅でウィジェットを選択し、Office 365 設定を選択します。
3. [追加のセキュリティ確認] をクリックします。
4. 右側の **[アカウントのセキュリティのために使用する電話番号を更新]** というリンクをクリックします。![セットアップ](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 設定を変更できるページに移動します。![クラウド](./media/multi-factor-authentication-end-user-manage/o365b.png)


## Myapps ポータルで設定を変更する

多要素認証を使用する方法がわからない場合、いつでも Myapps ポータルで設定を変更できます。

### Myapps ポータルで設定を変更するには

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。	
2. 上部にある [プロファイル] を選択します。
3. 追加のセキュリティ確認を選択します。![クラウド](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 設定を変更できるページに移動します。

![セットアップ](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

## Microsoft Azure で設定を変更する

Azure で多要素認証を使用している場合は、Azure ポータルで設定を変更することになります。

### Azure ポータルで追加のセキュリティ確認を設定する方法


1. Azure ポータルにログオンします。
2. Azure ポータルの上部に示されているユーザー名をクリックします。これにより、ドロップ ダウン ボックスが表示されます。
3. ドロップダウン ボックスで、[追加のセキュリティ確認] を選択します。![セットアップ](./media/multi-factor-authentication-end-user-manage/azure1.png)
4. 設定を変更できるページに移動します。![セットアップ](./media/multi-factor-authentication-end-user-manage-azure/proofup.png)

 

<!---HONumber=Nov15_HO4-->