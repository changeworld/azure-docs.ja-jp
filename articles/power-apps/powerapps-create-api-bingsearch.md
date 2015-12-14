<properties
	pageTitle="PowerApps Enterprise への Bing Search API の追加 | Microsoft Azure"
	description="組織の App Service 環境での新しい Bing Search API の作成または構成"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="gautamt"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 組織の App Service 環境での新しい Bing Search API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーで **[参照]** を選択します。![][4]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][5]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][2]

2. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][3]

3. API のわかりやすい**名前**を入力します。

4. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Bing Search]** を選択します。

	a) **[設定] - [必要な設定の構成]** を選択します。
	
	b) *アカウント キー*を入力します。Bing Search キーを取得していない場合は、無償の [Bing Search サービス][1] を作成してキーを取得します。

	c) **[OK]** を選択します。

5. **[OK]** を選択して手順を完了します。

完了すると、App Service 環境に新しい Bing Search API が追加されます。


## まとめと次のステップ
このトピックでは、Bing Search API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->