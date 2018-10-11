---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: faa3ad2376935aee4508b814f1b67fdacb98cf6e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843522"
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

