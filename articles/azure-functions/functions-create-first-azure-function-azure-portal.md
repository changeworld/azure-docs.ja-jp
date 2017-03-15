---
title: "Azure Portal からの関数の作成 | Microsoft Docs"
description: "初めての Azure 関数、つまりサーバー不要のアプリケーションを&2; 分以内に作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 4ee741cbec8db6b6400ff9f27daa2a0120bd2618
ms.lasthandoff: 03/03/2017


---
# <a name="create-a-function-from-the-azure-portal"></a>Azure Portal からの関数の作成
## <a name="overview"></a>概要
Azure Functions は、他の Azure サービス、SaaS 製品、オンプレミス システムで発生するイベントによってトリガーされるコードを実装する機能により、既存の Azure アプリケーション プラットフォームを拡張する、イベント ドリブンのオンデマンド コンピューティング エクスペリエンスを提供します。 Azure Functions を利用すると、アプリケーションが要求に応じてスケーリングされ、使用したリソースに対してのみ課金が行われます。 Azure Functions では、スケジュールやトリガーが設定されたコード ユニットを、さまざまなプログラミング言語で作成して実装できます。 Azure Functions の詳細については、「 [Azure Functions の概要](functions-overview.md)」を参照してください。

このトピックでは、Azure Portal を使用して、HTTP トリガーによって呼び出される簡単な "hello world" Node.js Azure Function を作成する方法について説明します。 Azure Portal で関数を作成するには、Azure App Service で Function App を明示的に作成しておく必要があります。 Function App を自動的に作成する場合は、[こちらの Azure Functions クイック スタート チュートリアル](functions-create-first-azure-function.md)をご覧ください。このチュートリアルでは、よりシンプルなクイック スタート エクスペリエンスを提供し、ビデオも用意されています。

## <a name="create-a-function-app"></a>Function App を作成する
関数アプリは Azure での関数の実行をホストします。 Azure アカウントがない場合は、[Functions の試用](https://functions.azure.com/try)に関するエクスペリエンスを確認するか、[無料の Azure アカウントを作成](https://azure.microsoft.com/free/)します。 次の手順に従って、Azure Portal で Function App を作成します。

1. [Azure Portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. **[+新規]** > **[Compute]** > **[Function App]** をクリックし、**[サブスクリプション]** を選択します。Function App を識別する一意の**アプリ名**を入力し、次の設定を指定します。
   
   * **[[リソース グループ]](../azure-resource-manager/resource-group-overview.md)**: **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 既存のリソース グループを選択することもできますが、関数アプリ用の使用量ベースの App Service プランを作成できない場合があります。
   * **[ホスティング プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**は、次のいずれかになります。 
     * **従量課金プラン**: Azure Functions の既定のプランの種類です。 従量課金プランを選択した場合は、**[場所]** の選択と **[メモリ割り当て]** (MB 単位) の設定も行う必要があります。 メモリ割り当てのコストへの影響については、 [Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページをご覧ください。 
     * **App Service プラン**: App Service プランの場合、**App Service プラン/場所**を作成するか、既存のものを選択する必要があります。 これらの設定により、アプリに関連付けられる [場所、機能、コスト、コンピューティング リソース](https://azure.microsoft.com/pricing/details/app-service/) が決まります。  
   * **[ストレージ アカウント]**: 各 Function App には、ストレージ アカウントが必要です。 既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 
     
     ![Azure Portal での新しい Function App の作成](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

    有効な**アプリ名**を入力する必要があります。名前に使用できるのは文字、数字、およびハイフンだけです。 アンダースコア (**_**) は使用できません。

3. **[作成]** をクリックして、新しい Function App をプロビジョニングし、デプロイします。  

### <a name="storage-account-requirements"></a>ストレージ アカウントの要件

Function App を作成するときは、BLOB、キュー、および Table Storage をサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 内部的には、Azure Functions はトリガーの管理や関数実行のログなどの操作に Azure Storage を使用します。 BLOB 専用のストレージ アカウント (Premium Storage を含む) や ZRS レプリケーションを使用する汎用ストレージ アカウントなどの一部のストレージ アカウントでは、キューとテーブルがサポートされません。 これらのアカウントは、新しい Function App の作成時に [ストレージ アカウント] ブレードから除外されます。
従量課金ホスティング プランを使用する場合、Function App のコンテンツ (関数コード ファイルやバインディング構成など) はメイン ストレージ アカウントの Azure Files 共有に保存されます。 メイン ストレージ アカウントを削除すると、このコンテンツが削除されて復元できなくなります。

ストレージ アカウントの種類の詳細については、Azure Storage サービスの概要に関する記事 (../storage/storage-introduction.md#introducing-the-azure-storage-services) をご覧ください。

## <a name="create-a-function"></a>関数を作成する
次の手順では、Azure Functions クイック スタートから関数を作成します。

1. **[クイック スタート]** タブで、**[WebHook + API]**、**[JavaScript]**、**[関数を作成する]** の順にクリックします。 新しい定義済みの Node.js 関数が作成されます。 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。    ツアーを完了するかスキップしたら、HTTP トリガーを使用して新しい関数をテストできます。


## <a name="test-the-function"></a>関数をテストする
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


