---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443972"
---
1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. 左側にある **[+ リソースの作成]** を選択し、 **[関数アプリ]** を選択します。

1. **[ホスティング プラン]** で、 **[App Service プラン]** を選択し、 **[App Service プラン/場所]** を選択します。

    ![Function App を作成する](./media/functions-premium-create/create-function-app-resource.png)

1. **[新規作成]** を選択し、 **[App Service プラン]** に名前を入力し、[[リージョン]](https://azure.microsoft.com/regions/) で、自分の近くか使用する関数がアクセスする他のサービスの近くの**場所**を選択し、 **[価格レベル]** を選択します。

    ![Create App Service plan](./media/functions-premium-create/new-app-service-plan.png)

1. **[EP1]** (エラスティック Premium) プランを選択し、 **[適用]** を選択します。

    ![Premium プランの選択](./media/functions-premium-create/hosting-plan.png) 

1. **[OK]** を選択してプランを作成し、残りの関数アプリの設定を、図の下の表に示すように指定します。 

    ![完成した App Service プラン](./media/functions-premium-create/create-function-app.png)  

    | Setting      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Function App を作成するための新しいリソース グループの名前。 推奨値を使用することもできます。 |
    | **OS** | 推奨 OS | Premium プランでは、Linux と Windows の両方がサポートされます。 |
    | **ランタイム スタック** | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 C# および F# 関数用の **[.NET]** を選択します。 選択した **OS** でサポートされている言語のみが表示されます。 |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  グローバルに一意の名前 |  関数アプリで使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](../articles/azure-functions/functions-scale.md#storage-account-requirements)を満たしている必要があります。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 既定値 | 最も近いサポートされているリージョン内に同じ*アプリ名*の Application Insights リソースを作成します。 この設定を展開することによって、 **[新しいリソース名]** を変更するか、またはデータを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **[場所]** を選択することができます。 |

1. 設定が検証されたら、 **[作成]** を選択します。

1. ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

    ![新しい関数アプリの設定を定義する](./media/functions-premium-create/function-app-create-notification.png)

1. **[リソースに移動]** を選択して、新しい関数アプリを確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。