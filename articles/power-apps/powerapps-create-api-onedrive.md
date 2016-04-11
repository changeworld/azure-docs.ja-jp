<properties
	pageTitle="PowerApps Enterprise への OneDrive API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい OneDrive API を作成または構成します。"
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
   ms.date="03/29/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい OneDrive API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

組織の (テナント) App Service 環境に新しい OneDrive API を追加します。

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[OneDrive]** を選択します。![OneDrive API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![OneDrive API 設定の構成][4]

9. OneDrive アプリケーションの*アプリケーション キー*と*アプリケーション シークレット*を入力します。これらがない場合は、このトピックの「PowerApps で使用する OneDrive アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい OneDrive API が追加されます。

## 省略可能: PowerApps で使用する OneDrive アプリケーションの登録

キーとシークレットの値が割り当てられた既存の OneDrive アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. _Microsoft アカウント デベロッパー センター_の[アプリケーション作成ページ][5]に移動し、_Microsoft アカウント_でサインインします。

2. **アプリケーション名**を入力し、**[同意する]** をクリックします。![新しい OneDrive アプリケーション][6]

3. 設定ページで次の操作を行います。

	1. **[API 設定]** を選択します。  
	2. Redirect URL を、(このトピックで) Azure ポータルを使用して新しい OneDrive API を追加したときに受け取ったリダイレクト URL に設定します。  
	3. [**保存**] を選択します。  

	![OneDrive アプリケーションの API 設定][7]

新しい OneDrive アプリケーションが作成されます。Azure ポータルの OneDrive API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Onedrive REST API](../connectors/connectors-create-api-onedrive.md) リファレンス。

## まとめと次のステップ
このトピックでは、OneDrive API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0330_2016-->