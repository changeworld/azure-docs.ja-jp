<properties
	pageTitle="PowerApps Enterprise への Excel API の追加 | Microsoft Azure"
	description="組織の App Service 環境での新しい Excel API の作成または構成"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい Excel API を作成する

組織の (テナント) App Service 環境に新しい Excel API を追加する

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][4]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][5]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Excel]** を選択します。![Excel API の選択][3]

8. **[OK]** を選択して手順を完了します。

完了すると、App Service 環境に新しい Excel API が追加されます。

## まとめと次のステップ
このトピックでは、Excel API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!---HONumber=AcomDC_0309_2016-->