---
title: "Azure App Service Web Apps on Linux での .NET Core の使用 | Microsoft Docs"
description: "Azure App Service Web Apps on Linux での .NET Core の使用について説明します。"
keywords: "Azure App Service, Web アプリ, .NET, コア, Linux, OSS"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Web Apps on Linux での .NET Core の使用 #

バックエンドの最新の更新で、.NET Core v.1.0 のサポートが導入されました。 Linux の Web アプリの構成を設定することで、アプリケーション スタックを変更できます。


## <a name="using-xplat-cli"></a>XPlat CLI の使用 ##

最新の Azure クロス プラットフォーム CLI を使用することで、**azure webapp config set** コマンドを使用し、アプリケーション スタックを変更することができます。 次に例を示します。

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** ファイルはアプリの dll であることに注意してください。 アプリでは任意の名前を使用することができます。

これで .Net Core イメージが読み込まれ、Web アプリが開始されます。 設定が正しいかどうかを確認するには、**azure webapp config show** を使用します。 次に例を示します。

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>次のステップ
* [App Service on Linux の概要](./app-service-linux-intro.md) 
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](./app-service-linux-how-to-create-a-web-app.md)
* [Azure Web アプリのクロス プラットフォーム CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


