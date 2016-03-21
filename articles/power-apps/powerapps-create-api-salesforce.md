<properties
	pageTitle="PowerApps Enterprise への Salesforce API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Salesforce API を作成または構成します。"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい Salesforce API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

組織の (テナント) App Service 環境に新しい Salesforce API を追加します。

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Salesforce]** を選択します。![Salesforce API を選択する][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Dropbox API の設定を構成する][7]

9. Salesforce アプリケーションの*アプリケーション キー*と*アプリケーション シークレット*を入力します。これらがない場合は、このトピックの「PowerApps で使用する Salesforce アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Salesforce API が追加されます。


## 省略可能: PowerApps で使用する Salesforce アプリケーションの登録

キーとシークレットの値が割り当てられた既存の Salesforce アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [Salesforce 開発者ホーム ページ][5]を開き、Salesforce アカウントでサインインします。 

2. ホーム ページで、自分のプロファイルを選択し、**[Setup]** を選択します。 ![Salesforce のホーム ページ][6]

3. **[Create]** を選択し、**[Apps]** を選択します。**[Apps]** ページで、**[Connected Apps]** の **[New]** を選択します。 ![Salesforce がアプリを作成する][7]

4. **[New Connected App]** で次のようにします。

	1. **[Connected App Name]** に値を入力します。  
	2. **[API Name]** に値を入力します。  
	3. **[Contact Email]** に値を入力します。  
	4. _[API (Enable OAuth Settings)]_ で **[Enable OAuth Settings]** を選択し、**[Callback URL]** を、(このトピックで) Azure ポータルを使用して新しい Salesforce API を追加したときに表示されたリダイレクト URL に設定します。  

5. _[Selected OAuth scopes]_ で、次のスコープを **[Selected OAuth Scopes]** に追加します。

	- 自分の Chatter データにアクセスして管理する (chatter\_api)
	- 自分のデータにアクセスして管理する (api)
	- 自分の一意の識別子へのアクセスを許可する (openid)
	- いつでも自分に代わって要求を実行する (refresh\_token、offline\_access)

6. 変更を**保存**します。 ![Salesforce の新しいアプリ][8]

新しい Salesforce アプリが作成されます。Azure ポータルの Salesforce API 構成でこのアプリケーションを使用できます。

## REST API に関するページを参照してください。

[Salesforce REST API](../connectors/create-api-salesforce.md) リファレンスを参照してください。

## まとめと次のステップ
このトピックでは、Salesforce API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_0309_2016-->