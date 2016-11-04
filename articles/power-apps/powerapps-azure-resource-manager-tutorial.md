---
title: 'チュートリアル: PowerApps と Logic Flows で Azure Resource Manager を使用してカスタム API を作成する | Microsoft Docs'
description: PowerApps と Logic Flows でカスタム API を作成する方法を示す Azure Resource Manager のチュートリアル
services: ''
suite: powerapps
documentationcenter: ''
author: sunaysv
manager: erikre
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: mandia

---
# チュートリアル: カスタム AAD で保護された PowerApps および Logic Flows 用 ARM API を作成する
このチュートリアルでは、[ARM API][6] を記述する Swagger ファイルを登録し、PowerApps でカスタム API に接続するために必要な手順について説明します。

> [!IMPORTANT]
> このトピックは、powerapps.microsoft.com の[カスタム AAD で保護された PowerApps および Flows 用 ARM API の作成に関するチュートリアル](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)のページに移動しました。最新版については PowerApps をご覧ください。この Azure のリンクはアーカイブされます。
> 
> 

## はじめにやるべきこと
* Azure サブスクリプション
* PowerApps アカウント

## Azure Active Directory で認証を有効にする
まず、ARM API エンドポイントの呼び出し時に認証を実行する、Azure Active Directory (AAD) アプリケーションを作成する必要があります。

1. AAD アプリケーションを作成するには、[Azure サブスクリプション][7]にサインインし、**[Active Directory]** に移動します。![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")
2. このページでは、AAD アプリケーションを作成するディレクトリを選択します。ディレクトリを選択し、**[アプリケーション]** タブに移動して、**[追加]** をクリックします。![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Azure アプリケーション")
3. アプリケーションの名前を入力し、**[Web アプリケーションや Web API]** を選択して、**[次へ]** ボタンをクリックします。![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "新しいアプリケーション")
4. **[サインオン URL]** に「*http://login.windows.net*」と入力します。*[アプリケーション ID/URI] に一意の URI を入力します。次に、[完了] ボタンをクリックします。![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "2 つ目の新しいアプリケーション")
5. AAD アプリケーションが作成されたら、**[構成]** タブに移動します。このタブで、アプリケーションに対するアクセス許可を構成します。
6. **[他のアプリケーションに対するアクセス許可]** で **[アプリケーションの追加]** を選択し、アクセス許可を次のように入力します。![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "アクセス許可")
   
    アプリケーションに必要なアクセス許可が委任されていることを確認します。![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "アクセス許可の委任")
7. **[キー]** で期間を選択します。**キーをコピーして安全な場所に保存します。**このキーは後で必要になります。**クライアント ID** もメモしておきます。![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "キーの構成")
8. **[シングル サインオン]** で、**[応答 URL]** に「https://msmanaged-na.consent.azure-apim.net/redirect」と入力します。![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "リダイレクト URL")
9. 変更を**保存**します。**キーをコピーして安全な場所に保存します**。

## PowerApps または Logic Flows で接続を追加する
これで、AAD アプリケーションが構成されました。次に、カスタム API を追加しましょう。

1. [PowerApps Web ポータル][1]を開き、**[Connections]** タブに移動して、右上隅にある **[Add a connection]** をクリックします。![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "カスタム API の作成")
2. **[Connect to custom API]** をクリックします。![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "カスタム API の作成")
3. ARM Swagger ファイルをアップロードします。このファイルは、[こちらでダウンロード][8]できます。![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "カスタム API の作成")
4. 次の画面では、AAD 認証を使用するために Swagger ファイルが検出されるので、AAD クライアント ID、クライアント シークレット (安全な場所に保存した**キー**)、その他の設定を入力する必要があります。![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "OAuth 設定")
5. すべてが正しく設定されていれば、接続を作成し、PowerApp または Logic Flow の作成時に参照することで、ARM カスタム API を使用できます。![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "作成されたカスタム API")

RESTful API を使用して公開され、AAD OAuth2 を使用して認証されたどのデータにも同様にアクセスできます。

PowerApps と Logic Flows の作成方法の詳細については、以下をご覧ください。

* [Connect to Office 365、Twitter、Microsoft Translator に接続する][5]
* [Office 365 のデータを表示する][4]
* [データからアプリケーションを作成する][3]
* [Logic Flows を使ってみる][2]

カスタム API に関するご質問やご意見があれば、[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) まで電子メールでお送りください。

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0713_2016-->