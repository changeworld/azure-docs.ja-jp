<properties
	pageTitle="PowerApps Enterprise への Office 365 Users API の追加 | Microsoft Azure"
	description="組織の App Service 環境で、新しい Office 365 Users API を作成または構成します。"
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
   ms.date="03/29/2016"
   ms.author="litran"/>

# PowerApps Enterprise で新しい Office 365 Users API を作成する

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

組織の (テナント) App Service 環境に Office 365 Users API を追加する

## Azure ポータルでの API の作成

1. [Azure ポータル](https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。
 
2. タスク バーの **[参照]** をクリックします。 ![][14]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。 ![][15]

4. **[PowerApps]** で、**[API の管理]** を選択します。![登録されている API の参照][1]

5. **[API の管理]** で、**[追加]** をクリックして新しい API を追加します。![Add API][2]

6. API のわかりやすい**名前**を入力します。
	
7. **[ソース]** で、**[利用可能な API]** を選択して既成の API を選択し、**[Office 365 Users]** を選択します。![Office 365 Users API の選択][3]

8. **[設定] - [必要な設定の構成]** を選択します。![Office 365 Users API 設定の構成][4]

9. Office 365 Azure Active Directory (AAD) アプリケーションの*クライアント ID* と*クライアント シークレット*を入力します。これらがない場合は、このトピックの「PowerApps Office 365 Users API で使用する AAD アプリケーションの登録」を参照して、必要な ID とシークレットの値を作成します。

	> [AZURE.IMPORTANT] **リダイレクト URL** を保存しておいてください。この値は、このトピックで後ほど必要になる場合があります。

10. **[OK]** をクリックして、手順を完了します。

完了すると、App Service 環境に新しい Office 365 Users API が追加されます。

## 省略可能: PowerApps Office 365 Users API で使用する AAD アプリケーションの登録

キーとシークレットの値が割り当てられた既存の AAD アプリケーションがない場合は、次の手順に従ってアプリケーションを作成し、必要な値を取得します。

1. [Azure ポータル][5]を開きます。

2. **[参照]** をクリックし、**[Active Directory]** を選択します。

	> [AZURE.NOTE] これにより、Azure クラシック ポータルで Active Directory が開きます。

3. 組織のテナント名を選択します。![Azure Active Directory の起動][6]

4. **[アプリケーション]** タブを選択し、**[追加]** をクリックします。![AAD テナント アプリケーション][7]

5. **[アプリケーションの追加]** で次の操作を行います。

	1. アプリケーションの **[名前]** を入力します。  
	2. アプリケーションの種類は **[Web]** のままにします。  
	3. **[次へ]** を選択します。  

	![AAD アプリケーションの追加 - アプリケーション情報][8]

6. **[アプリケーションのプロパティ]** で次の操作を行います。

	1. アプリケーションの**サインオン URL** を入力します。PowerApps の AAD に対して認証するので、サインオン URL を _https://login.windows.net_ に設定します。
2. アプリの有効な**アプリ ID URI** を入力します。  
	3. **[OK]** を選択します。  

	![AAD アプリケーションの追加 - アプリケーションのプロパティ][9]

7. 操作が正常に完了すると、新しい AAD アプリケーションにリダイレクトされます。**[構成]** をクリックします。![Contoso AAD アプリケーション][10]

8. _OAuth 2_ セクションの **[応答 URL]** を、(このトピックで) Azure ポータルで新しい Office 365 Users API を追加したときに受け取ったリダイレクト URL に設定します。**[アプリケーションの追加]** をクリックします。![Contoso AAD アプリケーションの構成][11]

9. **[他のアプリケーションに対するアクセス許可]** ウィンドウで、**[Office 365 統合 API (プレビュー)]** を選択し、**[OK]** をクリックします。

10. 構成ページに戻り、_[他のアプリケーションに対するアクセス許可]_ の一覧に _Office 365 統合 API (プレビュー)_ が追加されていることを確認します。

11. _Office 365 統合 API (プレビュー)_ の **[デリゲートされたアクセス許可]** を選択し、**[すべてのユーザーの基本プロファイルの読み取り]** アクセス許可を選択します。

新しい Azure Active Directory アプリケーションが作成されます。Azure ポータルの Office 365 Users API 構成でこのアプリケーションを使用できます。

「[アプリケーションを Azure AD に追加する方法と理由](../active-directory/active-directory-how-applications-are-added.md)」に AAD アプリケーションに関する有用な情報があります。

## REST API に関するページを参照してください。

[Office 365 Users REST API](../connectors/connectors-create-api-office365-users.md) リファレンス。

## まとめと次のステップ
このトピックでは、Office 365 Users API を PowerApps Enterprise に追加しました。次に、この API をユーザーのアプリケーションに追加できるように、ユーザーに API へのアクセスを許可します。

[接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0330_2016-->