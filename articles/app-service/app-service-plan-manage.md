---
title: "Azure で App Service プランを管理する | Microsoft Docs"
description: "App Service プランを管理するためのさまざまなタスクを実行する方法について説明します。"
keywords: "App Service, Azure App Service, スケール, App Service プラン, 変更する, 作成する, 管理する, 管理"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 5369946b1e8a4851ee940cf6fe91a1bdb94db5f3
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="manage-an-app-service-plan-in-azure"></a>Azure で App Service プランを管理する

[App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)は、App Service アプリの実行に必要なリソースを提供します。 このハウツー ガイドでは、App Service プランを管理する方法を示します。 

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

> [!TIP]
> App Service 環境がある場合は、 「[App Service 環境で App Service プランを作成する](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)」をご覧ください。

空の App Service プランを作成したり、またはアプリ作成の一部として作成したりすることができます。

[Azure Portal](https://portal.azure.com) で **[新規]** > **[Web + モバイル]** の順にクリックし、**[Web アプリ]** またはその他の App Service アプリの種類をクリックします。

![Azure Portal でアプリを作成します。][createWebApp]

その後、新しいアプリ用に既存の App Service プランを選んだりプランを作成したりできます。

 ![Create an App Service plan.][createASP]

App Service プランを作成するには、**[+ 新規作成]** をクリックし、**App Service プラン**名を入力して、適切な**場所**を選択します。 **[価格レベル]**をクリックし、サービスに適切な価格レベルを選択します。 **[すべて表示]** を選択して、**Free** や **Shared** などの価格オプションをさらに表示します。 

価格レベルを選択したら、 **[選択]** をクリックします。

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>アプリを別の App Service プランに移動する

別の App Service プランへのアプリの移動は、移動元プランと移動先プランが "_同じリソース グループおよび同じ地理的リージョン_" に存在している場合に限り可能です。

アプリを別のプランに移動するには、[Azure Portal](https://portal.azure.com) で移動するアプリに移ります。

**[メニュー]** で、**[App Service プラン]** セクションを探します。

**[App Service プランの変更]** を選択して処理を開始します。

**[App Service プランの変更]** で **[App Service プラン]** セレクターが表示されます。 このアプリの移動先の既存プランを選びます。 

> [!IMPORTANT]
> **[Select App Service plan]\(App Service プランの選択\)** ページは、次の条件でフィルター処理されます。 
> - 同じリソース グループに存在する 
> - 同じ地理的リージョンに存在する 
> - 同じ Web スペースに存在する  
> 
> "_Web スペース_" は、サーバー リソースのグループ化を定義する App Service 内の論理コンストラクトです。 地理的リージョン (米国西部など) には、App Service の顧客を割り当てる目的で、さまざまな Web スペースが含まれています。 現在のところ、App Service リソースを Web スペース間で移動することはできません。 
> 

![App Service plan selector.][change]

各プランには価格レベルが割り当てられています。 たとえば、サイトを **Free** レベルから **Standard** レベルに移動すると、サイトに割り当てられたすべてのアプリで **Standard** レベルの機能とリソースを使うことができるようになります。 ただし、高いレベルのプランから低いレベルのプランにアプリを移動すると、特定の機能にアクセスできなくなります。 移動後のプランで利用できない機能をアプリが使っている場合、該当する機能を示すエラーが表示されます。 たとえば、アプリの 1 つが SSL 証明書を使っている場合、次のようなエラー メッセージが表示される可能性があります。`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`この場合は、移動先プランにアプリを移動する前に、移動先プランの価格レベルを **Basic** 以上にスケールアップするか、アプリに対するすべての SSL 接続を削除する必要があります。

## <a name="move-an-app-to-a-different-region"></a>アプリを異なるリージョンに移動する

アプリが実行されるリージョンは、アプリを含む App Service プランのリージョンです。 ただし、App Service プランのリージョンを変更することはできません。 アプリを異なるリージョンに移動する必要がある場合の 1 つの方法として、アプリの複製の作成があります。 複製によって、任意のリージョンの新規または既存の App Service プランにアプリをコピーすることができます。

メニューの **[開発ツール]** セクションに **[アプリの複製]** があります。

> [!IMPORTANT]
> 複製にはいくつかの制限があります。これらの制限については、[Azure App Service アプリの複製](app-service-web-app-cloning.md)に関する記事をご覧ください。

## <a name="scale-an-app-service-plan"></a>App Service プランのスケーリング

App Service プランの価格レベルをスケールアップする方法については、「[Azure でのアプリのスケールアップ](web-sites-scale.md)」を参照してください。

アプリのインスタンスの数をスケールアウトする方法については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)」をご覧ください。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service プランを削除する

予期しない課金を避けるため、App Service プランの最後のアプリを削除すると、プランも既定で削除されます。 プランを削除せずに残しておく場合は、課金されないように、プランを **Free** レベルに変更する必要があります。

> [!IMPORTANT]
> **App Service プラン**にアプリが関連付けられていない場合でも、構成済みの VM インスタンスが引き続き確保されるため、料金が発生します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure でのアプリのスケールアップ](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
