---
title: 'チュートリアル: PowerApps と Logic Flows で ASP.Net Web アプリを使用してカスタム API を作成する | Microsoft Docs'
description: PowerApps と Logic Flows でカスタム API を作成する方法を示す ASP.Net Web アプリのチュートリアル
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
# チュートリアル: カスタム AAD で保護された PowerApps および Logic Flows 用 Web API を作成する
このチュートリアルでは、ASP.Net Web API を作成して Azure Webapps でホストし、簡単認証によって AAD 認証を有効にした後、その Web API を PowerApps と Logic Flows に登録する方法を示します。

> [!IMPORTANT]
> このトピックは、powerapps.microsoft.com の[カスタム AAD で保護された PowerApps および Flows 用 Web API の作成に関するチュートリアル](https://powerapps.microsoft.com/tutorials/customapi-web-api-tutorial/)のページに移動しました。最新版については PowerApps をご覧ください。この Azure のリンクはアーカイブされます。
> 
> 

## はじめにやるべきこと
* Azure サブスクリプション
* PowerApps アカウント
* Visual Studio 2013 以降

## 手順 1: Web API を作成して Azure にデプロイする
1. Visual Studio を開き、新しい C# ASP.NET Web アプリケーションを作成します。![](./media/powerapps-web-api-tutorial/newwebapp.png "新しい Web アプリ")
2. 次の画面で Web API テンプレートを選択し、**[認証なし]** を選択します。![](./media/powerapps-web-api-tutorial/noauth.png "承認なし")
   
   > [!IMPORTANT]
   > 認証を [認証なし] に設定したことを確認してください。
   > 
   > 
3. プロジェクトを作成するときは、リソース用の Web API をビルドする必要があります。このチュートリアルでは、Web API のビルドについて、その詳細は説明しません。
4. 次に、Web API 用の Swagger ファイルを生成します。これは、**パッケージ マネージャー コンソール__を開いて __Swashbuckle** をインストールすることで簡単に実行できます。![](./media/powerapps-web-api-tutorial/swashbuckle-console.png "Swashbuckle コンソール")
5. インストールと有効化が終わったら、次の Swagger ドキュメントと UI エンドポイントを参照します。**<your-root-url>/swagger/docs/v1**
   
     **<your-root-url>/swagger**
6. Web API が完成したら、Azure に発行します。Visual Studio で発行するには、**[ビルド]** に移動し、**[発行]** を選択します。
7. ***https://\<azure-webapp-url>/swagger/docs/v1*** に移動して、swagger json を抽出します。
   
   > [!IMPORTANT]
   > operation-id が重複している swagger ドキュメントは無効です。サンプルの C# テンプレートを使用した場合、operation-id "Values\_Get" が 2 回繰り返されます。片方のインスタンスを "Value\_Get" に変更してください。
   > 
   > 

このチュートリアルで使用する swagger は、[こちら][6]からダウンロードできます。使用する前に、コメントを置き換えるか削除してください。コメントは `//` で始まります。

## 手順 2: AAD 認証を設定する
このチュートリアルは、Azure で AAD アプリケーションを作成する方法を知っていることを前提としています。AAD アプリケーションを作成する方法の詳細については、[Azure Resource Manager のチュートリアル](powerapps-azure-resource-manager-tutorial.md)をご覧ください。このチュートリアルでは 2 つの AAD アプリケーションが必要です。

1. 1 つ目の AAD アプリケーションは、Web API をセキュリティで保護するために使用されます。このアプリケーションには **webAPI** という名前を付けます。
2. 2 つ目の AAD アプリケーションは、カスタム API の登録をセキュリティで保護し、1 つ目のアプリによって保護されている Web API への委任アクセスを取得するために使用されます。このアプリケーションには **webAPI-customAPI** という名前を付けます。
3. **webAPI** では、次の構成を使用します。
   
   1. サインオン URL: ***https://login.windows.net***
   2. アプリ ID URI: ***https://\<your-root-url>*** (通常は Azure にデプロイした Web サイトの URL)
   3. 応答 URL: ***https://\<your-root-url>/.auth/login/aad/callback***
      
      > [!IMPORTANT]
      > このアプリのクライアント ID は後で必要になるため、メモしておいてください。
      > 
      > 
4. **webAPI-customAPI** では、次の構成を使用します。
   
   1. サインオン URL: **https://login.windows.net**
   2. アプリ ID URI: **一意の URL を指定**
   3. 応答 URL: ***https://msmanaged-na.consent.azure-apim.net/redirect***
   4. webAPI への委任アクセスを持たせるアクセス許可を追加します。
   5. このアプリのクライアント ID も後で必要になるため、メモしておいてください。
   6. キーを生成し、安全な場所に保存します。このキーは後で必要です。

> [!IMPORTANT]
> 両方のアプリは、同じディレクトリに配置される必要があります。
> 
> 

## 手順 3: Web アプリの簡単認証を設定する
1. [Azure ポータル](https://portal.azure.com)にサインインし、このトピックの**手順 1** でデプロイした Web アプリに移動します。
2. **[設定]** で、**[認証/承認]** を選択します。
3. **[App Service の認証]** をオンにし、**[Azure Active Directory]** を選択します。次のブレードで **[Express]** を選択します。
4. **[既存の AD アプリ]** をクリックし、手順 2 で作成した 1 つ目の AAD アプリケーションを選択します。ここでは **webAPI** を選択します。

これで、Web アプリに対する AAD 認証が設定されました。

## 手順 4: カスタム API を設定する
1. swagger を少し変更して、Web アプリで使用される `securityDefintions` オブジェクトと AAD 認証を入力します。次のコード行を追加します。
   
    ```javascript
   "host": "<your-root-url>",
   "schemes": [
    "https"                        //Change scheme to https 
   ],
   "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
   },
    ```
2. PowerApps [Web ポータル][1]に移動し、カスタム API を追加します。手順については、[Logic Flows と PowerApps でのカスタム API の使用](powerapps-register-custom-api.md)に関する記事をご覧ください。
3. Swagger をアップロードすると、webAPI で AAD 認証を使用していることがウィザードによって自動的に検出されます。
4. カスタム API 用の AAD 認証を構成します。
   
   1. クライアント ID: このトピックの**手順 2** の 4.e でメモしておいた **webAPI-CustomAPI のクライアント ID**
   2. シークレット: このトピックの**手順 2** の 4.f で生成した **webAPI-CustomAPI のキー**
   3. ログイン URL: ***https://login.windows.net***
   4. ResourceUri: このトピックの**手順 2** の 3 でメモしておいた**webAPI のクライアント ID**
5. **[作成]** を選択し、カスタム API に接続できるかどうかを試します。すべてが正しく設定されていれば、サインインは成功します。

PowerApps と Logic Flows の作成方法の詳細については、以下をご覧ください。

* [Connect to Office 365, Twitter, and Microsoft Translator (Office 365、Twitter、Microsoft Translator に接続する)][5]
* [Show data from Office 365 (Office 365 のデータを表示する)][4]
* [Create an app from data (データからアプリを作成する)][3]
* [Logic Flows の概要に関するページ][2]

ご質問やご意見があれば、[customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) まで電子メールでお送りください。

<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: http://pwrappssamples.blob.core.windows.net/samples/webAPI.json

<!---HONumber=AcomDC_0713_2016-->