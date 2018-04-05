---
title: SQL データベースを Azure Stack ユーザーから使用可能にする | Microsoft Docs
description: SQL Server リソース プロバイダーをインストールし、Azure Stack ユーザーが SQL データベースを作成できるようにするオファーを作成するためのチュートリアル。
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
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL データベースを Azure Stack ユーザーから使用可能にする
Azure Stack クラウド管理者として、ユーザー (テナント) が自分のクラウド ネイティブなアプリ、Web サイト、およびワークロードで使用できる SQL データベースを作成できるようにするオファーを作成できます。 これらのカスタムの、オンデマンドで、クラウド ベースのデータベースをユーザーに提供することによって、ユーザーの時間とリソースを節約できます。 これを設定するには、次のことを行います。

> [!div class="checklist"]
> * SQL Server リソース プロバイダーのデプロイ
> * オファーの作成
> * オファーのテスト

## <a name="deploy-the-sql-server-resource-provider"></a>SQL Server リソース プロバイダーのデプロイ

このデプロイ プロセスは [Azure Stack 上での SQL データベースの使用の記事](azure-stack-sql-resource-provider-deploy.md)で詳細に説明されており、次の主要な手順で構成されています。

1. [SQL リソース プロバイダーをデプロイします]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider)。
2. [デプロイを検証します]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. ホスティング SQL Server に接続して容量を提供します。

## <a name="create-an-offer"></a>オファーの作成

1.  [クォータを設定し](azure-stack-setting-quotas.md)、それに *SQLServerQuota* という名前を付けます。 **[Namespace] (名前空間)** フィールドの **[Microsoft.SQLAdapter]** を選択します。
2.  [プランを作成します](azure-stack-create-plan.md)。 それに *TestSQLServerPlan* という名前を付け、**[Microsoft.SQLAdapter]** サービスおよび **[SQLServerQuota]** クォータを選択します。

    > [!NOTE]
    > ユーザーが他のアプリを作成できるようにするには、プランに他のサービスが必要になることがあります。 たとえば、Azure Functions ではプランに **Microsoft.Storage** サービスが含まれている必要があるのに対して、Wordpress には **Microsoft.MySQLAdapter** が必要です。
    > 
    >

3.  [オファーを作成し](azure-stack-create-offer.md)、それに **TestSQLServerOffer** という名前を付け、**[TestSQLServerPlan]** プランを選択します。

## <a name="test-the-offer"></a>オファーのテスト

これで SQL Server リソース プロバイダーをデプロイし、オファーを作成したので、ユーザーとしてサインインし、オファーにサブスクライブして、データベースを作成できます。

### <a name="subscribe-to-the-offer"></a>オファーへのサブスクライブ
1. Azure Stack ポータル (https://portal.local.azurestack.external) にテナントとしてサインインします。
2. **[Get a subscription] \(サブスクリプションの取得)** をクリックし、**[Display Name] \(表示名)** の下に「**TestSQLServerSubscription**」と入力します。
3. **[Select an offer] \(オファーの選択)** > **[TestSQLServerOffer]** > **[作成]** をクリックします。
4. **[その他のサービス]** > **[サブスクリプション]** > **[TestSQLServerSubscription]** > **[Resource providers] \(リソース プロバイダー)** をクリックします。
5. **[Microsoft.SQLAdapter]** プロバイダーの横にある **[Register] \(登録)** をクリックします。

### <a name="create-a-sql-database"></a>SQL Database の作成

1. **+** > **[データ + ストレージ]** > **[SQL Database]** をクリックします。
2. 各フィールドの既定値のままにします。または、次の例を使用できます。
    - **[データベース名]**: SQLdb
    - **[Max Size in MB] \(最大サイズ (MB))**: 100
    - **[サブスクリプション]**: TestSQLOffer
    - **[リソース グループ]**: SQL-RG
3. **[Login Settings] \(ログイン設定)** をクリックし、データベースの資格情報を入力して、**[OK]** をクリックします。
4. **[SKU]** をクリックし、SQL ホスティング サーバーに対して作成した SQL SKU を選択して、**[OK]** をクリックします。
5. **Create** をクリックしてください。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * SQL Server リソース プロバイダーのデプロイ
> * オファーの作成
> * オファーのテスト

次のチュートリアルに進み、次の操作方法を確認してください。

> [!div class="nextstepaction"]
> [Web、モバイル、および API アプリをユーザーから使用可能にする]( azure-stack-tutorial-app-service.md)

