---
title: "Azure App Service Web Apps on Linux での .NET Core の使用 | Microsoft Docs"
description: "Azure App Service Web Apps on Linux で .NET Core を使用します。"
keywords: "Azure App Service, Web アプリ, .NET, コア, Linux, OSS"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Azure Web App on Linux で .NET Core を使用する #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


バックエンドの最新の更新で、.NET Core v.1.0 のサポートが導入されました。 Linux の Web アプリの構成を設定することで、アプリケーション スタックを変更できます。


## <a name="using-the-azure-cli"></a>Azure CLI の使用 ##

[最新の Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用することで、**azure webapp config set** コマンドを使用してアプリケーション スタックを変更することができます。 たとえば次のようになります。

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

**aspnetcore.dll** ファイルはアプリの dll です。 アプリでは任意の名前を使用することができます。

これで .Net Core イメージが読み込まれ、Web アプリが開始されます。 設定が正しいかどうかを確認するには、**azure webapp config show** を使用します。 たとえば次のようになります。

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>次のステップ
* [Azure Web App on Linux とは](app-service-linux-intro.md)
* [Azure Web App on Linux で Web Apps を作成する](./app-service-linux-how-to-create-web-app.md)
* [Azure Web アプリのクロス プラットフォーム CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Azure App Service Web App on Linux の FAQ](app-service-linux-faq.md)
