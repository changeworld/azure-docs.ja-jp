---
title: Web アプリおよび API アプリを Azure Stack ユーザーが使用できるようにする | Microsoft Docs
description: App Service リソース プロバイダーをインストールし、Azure Stack ユーザーが Web アプリおよび API アプリを作成できるようにするオファーを作成するためのチュートリアル。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.openlocfilehash: f8a54436a61889e6d39c7e491e45c25e3b38236f
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036788"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>チュートリアル: Web アプリおよび API アプリを Azure Stack ユーザーが使用できるようにする

Azure Stack クラウド管理者は、ユーザー (テナント) が Azure Functions、Web アプリケーション、API アプリケーションを作成できるようにするオファーを作成できます。 これらのオンデマンドで、クラウド ベースのアプリへのアクセスをユーザーに提供することによって、ユーザーの時間とリソースを節約できます。

これを設定するには、次のことを行います。

> [!div class="checklist"]
> * App Service リソース プロバイダーをデプロイする
> * オファーの作成
> * オファーのテスト

## <a name="deploy-the-app-service-resource-provider"></a>App Service リソース プロバイダーをデプロイする

1. [Azure Stack Development Kit ホストを準備します](azure-stack-app-service-before-you-get-started.md)。 これには、一部のアプリの作成に必要な SQL Server リソース プロバイダーのデプロイが含まれます。
2. [インストーラおよびヘルパー スクリプトをダウンロードします](azure-stack-app-service-deploy.md)。
3. [ヘルパー スクリプトを実行して、必要な証明書を作成します](azure-stack-app-service-deploy.md)。
4. [App Service リソース プロバイダーをインストールします](azure-stack-app-service-deploy.md) (インストールし、すべての worker ロールが表示されるには数時間かかります)。
5. [インストールを検証します](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)。

## <a name="create-an-offer"></a>オファーの作成

例として、ユーザーが DNN Web コンテンツ管理システムを作成できるようにするオファーを作成できます。 それには、SQL Server リソース プロバイダーをインストールすることによって既に有効にしている SQL Server サービスが必要です。

1.  [クォータを設定し](azure-stack-setting-quotas.md)、それに *AppServiceQuota* という名前を付けます。 **[Namespace] (名前空間)** フィールドの **[Microsoft.Web]** を選択します。
2.  [プランを作成します](azure-stack-create-plan.md)。 *TestAppServicePlan* という名前を付け、**[Microsoft.SQL]** サービスと **[AppService Quota]\(AppService クォータ\)** クォータを選択します。

    > [!NOTE]
    > ユーザーが他のアプリを作成できるようにするには、プランに他のサービスが必要になることがあります。 たとえば、Azure Functions ではプランに **Microsoft.Storage** サービスが含まれている必要があるのに対して、WordPress には **Microsoft.MySQL** が必要です。

3.  [オファーを作成し](azure-stack-create-offer.md)、それに **TestAppServiceOffer** という名前を付け、**[TestAppServicePlan]** プランを選択します。

## <a name="test-the-offer"></a>オファーのテスト

これで App Service リソース プロバイダーをデプロイし、オファーを作成したので、ユーザーとしてサインインし、オファーにサブスクライブして、アプリを作成できます。

この例では、DNN プラットフォーム コンテンツ管理システムを作成します。 まず、SQL データベース、次に DNN Web アプリを作成する必要があります。

### <a name="subscribe-to-the-offer"></a>オファーへのサブスクライブ

1. Azure Stack ポータル (https://portal.local.azurestack.external) にテナントとしてサインインします。
2. **[サブスクリプションの取得]** を選択し、**[表示名]** > **[オファーの選択]** > **[TestAppServiceOffer]** > **[作成]** の下に「**TestAppServiceSubscription**」と入力します。

### <a name="create-a-sql-database"></a>SQL Database の作成

1. **+** > **[データ + ストレージ]** > **[SQL Database]** の順に選択します。
2. 次のフィールドを除き、既定値のままにします。

    - **[データベース名]**: DNNdb
    - **[Max Size in MB] (最大サイズ (MB))**: 100
    - **[サブスクリプション]**: TestAppServiceOffer
    - **[リソース グループ]**: DNN-RG

3. **[Login Settings]\(ログイン設定\)** を選択し、データベースの資格情報を入力して、**[OK]** をクリックします。 これらの資格情報は、このチュートリアルで後ほど使用します。
4. **[SKU]** で、SQL ホスティング サーバーに対して作成した SQL SKU を選択して、**[OK]** をクリックします。
5. **作成**を選択します。

### <a name="create-a-dnn-app"></a>DNN アプリの作成

1. **+** > **[See all]\(すべてを表示\)** > **[DNN Platform preview]\(DNN プラットフォームのプレビュー\)** > **[作成]** の順に選択します。
2. **[アプリ名]** の下に「*DNNapp*」と入力し、**[サブスクリプション]** の下にある **[TestAppServiceOffer]** を選択します。
3. **[必要な設定の構成]** > **[新規作成]** の順に選択し、**[App Service プラン]** の名前を入力します。
4. **[価格レベル]** > **[F1 Free]\(F1 無料\)** > **[選択]** > **[OK]** の順に選択します。
5. **[データベース]** を選択し、以前に作成した SQL データベースの情報を入力します。
6. **作成**を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * App Service リソース プロバイダーをデプロイする
> * オファーの作成
> * オファーのテスト

次のチュートリアルに進み、次の操作方法を確認してください。

> [!div class="nextstepaction"]
> [Azure と Azure Stack へのアプリのデプロイ](user/azure-stack-solution-pipeline.md)
