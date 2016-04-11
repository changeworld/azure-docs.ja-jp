<properties
	pageTitle="PowerApps Enterprise への Microsoft Translator API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Microsoft Translator API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
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

# PowerApps Enterprise に新しい Microsoft Translator API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

組織の (テナント) App Service 環境に Microsoft Translator API を追加する

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][7]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][8]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Microsoft Translator]** を選択します。![Microsoft Translator API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Microsoft Translator API 設定の構成][4]

9. Microsoft Translator アプリケーションの*クライアント ID* と*クライアント シークレット*を入力します。これらがない場合は、このトピックの「PowerApps で使用する Microsoft Translator アプリケーションの登録」を参照して、必要な ID とシークレットの値を作成します。

9. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Microsoft Translator API が追加されます。


## 省略可能: PowerApps で使用する Microsoft Translator アプリケーションの登録

ID とシークレットの値が割り当てられた既存の Microsoft Translator アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。


1. [Azure Data Market 開発者ページ][5]に移動し、Microsoft アカウントでサインインします。 

2. **[登録]** をクリックします。

3. **[アプリケーションの登録]**で次の操作を行います。

	1. **[クライアント ID]** の値を入力します。  
	2. アプリケーションの**名前**を入力します。  
	3. **リダイレクト URL** のダミー値を入力します。たとえば、「**https://contosoredirecturl*」と入力します。
4. **説明**を入力します。  
	5. **[作成]** を選択します。  

	![アプリケーションの登録][6]

新しい Microsoft Translator アプリケーションが作成されます。Azure ポータルの Microsoft Translator API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Microsoft Translator REST API](../connectors/connectors-create-api-microsofttranslator.md) リファレンス。

## まとめと次のステップ
このトピックでは、Microsoft Translator API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_0330_2016-->