---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7c052f30e564700b0a99b76342dc6c34ec5983b7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33901280"
---
## <a name="register-your-application"></a>アプリケーションの登録

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加する場合、2 つのオプションがあります。

### <a name="option-1-express-mode"></a>オプション 1: 簡易モード

次の手順を実行すると、アプリケーションをすばやく登録できます。

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します
4.  指示に従って、アプリケーションにリダイレクト URL を追加します

### <a name="option-2-advanced-mode"></a>オプション 2: 詳細モード

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. ガイド付きセットアップのオプションがオフになっていることを確認します
4. [`Add Platform`] \(プラットフォームの追加) をクリックし、[`Web`] を選択します
5. Visual Studio に戻り、ソリューション エクスプローラーでプロジェクトを選択して [プロパティ] ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は、F4 キーを押します)
6. [SSL 有効] を `True` に変更します
7. Visual Studio でプロジェクトを右クリックし、**[プロパティ]**、および **[Web]** タブを選択します。*[サーバー]* セクションで、*[プロジェクト URL]* が SSL URL になるように変更します
8. SSL URL をコピーし、登録ポータルのリダイレクト URL の一覧にこの URL を追加します<br/><br/>![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. `configuration\appSettings` セクションの下のルート フォルダーにある `web.config` に、以下のコードを追加します。

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. `ClientId` を、さきほど登録したアプリケーション ID に置き換えます
11. `redirectUri` を、プロジェクトの SSL URL に置き換えます

