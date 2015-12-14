<properties
	pageTitle="PowerApps Enterprise への Office 365 Outlook API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Office 365 Outlook API を作成または構成します。"
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
   ms.date="11/25/2015"
   ms.author="litran"/>

# 組織の App Service 環境での新しい Office 365 Outlook API の作成

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*ユーザー名*@*会社名*.com でサインインします。これにより、自動的に会社のサブスクリプションにサインインされます。
 
2. タスク バーの **[参照]** をクリックします。![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「powerapps」と入力します。![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Office 365 Outlook]** を選択します。![Office 365 Outlook API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Office 365 Outlook API 設定の構成][4]

9. Office 365 Azure Active Directory (AAD) アプリケーションの*アプリケーション キー*と*アプリケーション シークレット*の値を入力します。これらがない場合は、このトピックの「PowerApps Office 365 API で使用する AAD アプリケーションの登録」を参照して、必要なキーとシークレットの値を作成します。
 
	> [AZURE.IMPORTANT]**リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Office 365 Outlook API が追加されます。


## 省略可能: PowerApps Office 365 API で使用する AAD アプリケーションの登録

キーとシークレットの値が割り当てられた既存の AAD アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [Azure ポータル][5]を開きます。

2. **[参照]** をクリックし、**[Active Directory]** を選択します。

	>[AZURE.NOTE]これにより、Azure クラシック ポータルで Active Directory が開きます。

3. 組織のテナント名を選択します。![Azure Active Directory の起動][6]

4. **[アプリケーション]** タブを選択し、**[追加]** をクリックします。![AAD テナント アプリケーション][7]

5. **[アプリケーションの追加]** で次の操作を行います。

	a) アプリケーションの**名前**を入力します。b) アプリケーションの種類は **Web** のままにしておきます。c) **[次へ]** をクリックします。

	![AAD アプリケーションの追加 - アプリケーション情報][8]

6. **[アプリケーションのプロパティ]** で次の操作を行います。

	a) アプリケーションの**サインオン URL** を入力します。AAD を使用して PowerApps に対する認証を行うので、サインオン URL を _https://login.windows.net_ に設定します。b) アプリケーションの有効な**アプリケーション ID URI** を入力します。c) **[OK]** をクリックします。

	![AAD アプリケーションの追加 - アプリケーションのプロパティ][9]

7. 操作が正常に完了すると、新しい AAD アプリケーションにリダイレクトされます。**[構成]** をクリックします。![Contoso AAD アプリケーション][10]

8. _OAuth 2_ セクションの **[応答 URL]** を、(このトピックで) Azure ポータルで新しい Office 365 Outlook API を追加したときに受け取ったリダイレクト URL に設定します。次に、**[アプリケーションの追加]** をクリックします。![Contoso AAD アプリケーションの構成][11]

9. **[他のアプリケーションに対するアクセス許可]** ウィンドウで、**[Office 365 Exchange Online]** を選択し、**[OK]** をクリックします。![Contoso アプリケーションでのデリゲート][12]

10. 構成ページに戻り、_[他のアプリケーションに対するアクセス許可]_ の一覧に _Office 365 Exchange Online_ が追加されていることを確認します。

11. _Office 365 Exchange Online_ の **[デリゲートされたアクセス許可]** を選択し、次のアクセス許可を選択します。

	- ユーザーの連絡先の読み取りと書き込み
	- ユーザーの連絡先の読み取り
	- ユーザーの予定表の読み取りと書き込み
	- ユーザーの予定表の読み取り
	- ユーザーとしてのメールの送信
	- ユーザーのメールの読み取りと書き込み
	- ユーザーのメールの読み取り

	![Contoso アプリケーションでのアクセス許可のデリゲート][13]

新しい Azure Active Directory アプリケーションが作成されます。Azure ポータルの Office 365 Outlook API 構成でこのアプリケーションを使用できます。

## まとめと次のステップ
このトピックでは、Office 365 Users API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->