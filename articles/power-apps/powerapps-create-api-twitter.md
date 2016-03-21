<properties
	pageTitle="PowerApps Enterprise への Twitter API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Twitter API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい Twitter API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

組織の (テナント) App Service 環境に新しい Twitter API を追加します。

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。 

2. タスク バーの **[参照]** をクリックします。![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Twitter]** を選択します。![Twitter API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Twitter API の設定の構成][4]

9. Twitter アプリケーションの*コンシューマー キー*と*コンシューマー シークレット*を入力します。これらがない場合は、このトピックの「PowerApps で使用する Twitter アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Twitter API が追加されます。


## 省略可能: PowerApps で使用する Twitter アプリケーションの登録

キーとシークレットの値が割り当てられた既存の Twitter アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [https://apps.twitter.com/](https://apps.twitter.com) にアクセスし、Twitter アカウントでサインインします。

2. **[Create New App]** を選択します。 ![Twitter アプリ ページ][6]

3. **[Create an application]** では次のようにします。
   
	1. **[Name]** の値を入力します。  
	2. **[Description]** の値を入力します。  
	3. **[Website]** の値を入力します。  
	4. **[Callback url]** を、(このトピックで) Azure ポータルを使用して新しい Twitter API を追加したときに受け取ったリダイレクト URL に設定します。  
	5. 開発者規約に同意し、**[Create your Twitter application]** を選択します。  

	![Twitter アプリの作成][7]

4. アプリが正常に作成されると、アプリのページにリダイレクトされます。

新しい Twitter アプリが作成されます。Azure ポータルの Twitter API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Twitter REST API](../connectors/create-api-twitter.md) リファレンスを参照してください。


## まとめと次のステップ
このトピックでは、Twitter API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->