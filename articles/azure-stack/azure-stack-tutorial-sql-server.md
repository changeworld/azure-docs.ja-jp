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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 35f4d2adfe3ca64496139cdd708fb5f52f8721ee
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023479"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>チュートリアル: SQL データベースを Azure Stack ユーザーから使用できるようにする

Azure Stack クラウド管理者として、ユーザー (テナント) が自分のクラウド ネイティブなアプリ、Web サイト、およびワークロードで使用できる SQL データベースを作成できるようにするオファーを作成できます。 これらのカスタムの、オンデマンドで、クラウド ベースのデータベースをユーザーに提供することによって、ユーザーの時間とリソースを節約できます。 これを設定するには、次のことを行います。

> [!div class="checklist"]
> * SQL Server リソース プロバイダーのデプロイ
> * オファーの作成
> * オファーのテスト

## <a name="deploy-the-sql-server-resource-provider"></a>SQL Server リソース プロバイダーのデプロイ

このデプロイ プロセスは [Azure Stack 上での SQL データベースの使用の記事](azure-stack-sql-resource-provider-deploy.md)で詳細に説明されており、次の主要な手順で構成されています。

1. [SQL リソース プロバイダーをデプロイします](azure-stack-sql-resource-provider-deploy.md)。
2. [デプロイを検証します](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. ホスティング SQL Server に接続して容量を提供します。 詳細については、[ホスティング サーバーの追加](azure-stack-sql-resource-provider-hosting-servers.md)に関するページを参照してください。

## <a name="create-an-offer"></a>オファーの作成

1.  [クォータを設定し](azure-stack-setting-quotas.md)、それに *SQLServerQuota* という名前を付けます。 **[Namespace] (名前空間)** フィールドの **[Microsoft.SQLAdapter]** を選択します。
2.  [プランを作成します](azure-stack-create-plan.md)。 それに *TestSQLServerPlan* という名前を付け、**[Microsoft.SQLAdapter]** サービスおよび **[SQLServerQuota]** クォータを選択します。

    > [!NOTE]
    > ユーザーが他のアプリを作成できるようにするには、プランに他のサービスが必要になることがあります。 たとえば、Azure Functions ではプランに **Microsoft.Storage** サービスが含まれている必要があるのに対して、WordPress には **Microsoft.MySQLAdapter** が必要です。

3.  [オファーを作成し](azure-stack-create-offer.md)、それに **TestSQLServerOffer** という名前を付け、**[TestSQLServerPlan]** プランを選択します。

## <a name="test-the-offer"></a>オファーのテスト

これで SQL Server リソース プロバイダーをデプロイし、オファーを作成したので、ユーザーとしてサインインし、オファーにサブスクライブして、データベースを作成できます。

### <a name="subscribe-to-the-offer"></a>オファーへのサブスクライブ

1. Azure Stack ポータル (https://portal.local.azurestack.external) にテナントとしてサインインします。
2. **[サブスクリプションの取得]** を選択し、**[表示名]** の下に「**TestSQLServerSubscription**」と入力します。
3. **[オファーの選択]** > **[TestSQLServerOffer]** > **[作成]** の順に選択します。
4. **[すべてのサービス]** > **[サブスクリプション]** > **[TestSQLServerSubscription]** > **[リソース プロバイダー]** の順に選択します。
5. **[Microsoft.SQLAdapter]** プロバイダーの横にある **[登録]** を選択します。

### <a name="create-a-sql-database"></a>SQL Database の作成

1. **+** > **[データ + ストレージ]** > **[SQL Database]** の順に選択します。
2. 次のフィールドについては、既定値のままにするか、これらの例を使用します。
    - **[データベース名]**: SQLdb
    - **[Max Size in MB] \(最大サイズ (MB))**: 100
    - **[サブスクリプション]**: TestSQLOffer
    - **[リソース グループ]**: SQL-RG
3. **[Login Settings]\(ログイン設定\)** を選択し、データベースの資格情報を入力して、**[OK]** をクリックします。
4. **[SKU]** で、SQL ホスティング サーバーに対して作成した SQL SKU を選択して、**[OK]** をクリックします。
5. **作成**を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * SQL Server リソース プロバイダーのデプロイ
> * オファーの作成
> * オファーのテスト

次のチュートリアルに進み、次の操作方法を確認してください。

> [!div class="nextstepaction"]
> [Web、モバイル、および API アプリをユーザーから使用可能にする]( azure-stack-tutorial-app-service.md)