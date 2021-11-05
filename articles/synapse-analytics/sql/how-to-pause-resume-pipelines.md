---
title: Synapse パイプラインを使用して専用 SQL プールを一時停止および再開する方法
description: Azure Synapse Analytics で Synapse パイプラインを使用して、専用 SQL プールの一時停止と再開を自動化する方法について学習します。
author: julieMSFT
ms.author: jrasnick
ms.service: synapse-analytics
ms.reviewer: wiassaf
ms.subservice: sql
ms.topic: how-to
ms.date: 08/12/2021
ms.custom: template-how-to
ms.openlocfilehash: d31cb6d07958c5f14ac5e45f0094e772b168a535
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847928"
---
# <a name="pause-and-resume-dedicated-sql-pools-with-synapse-pipelines"></a>Synapse パイプラインを使用して専用 SQL プールを一時停止および再開する

専用 SQL プールの一時停止と再開は、Azure Synapse Analytics で Synapse パイプラインを使用して自動化できます。 一時停止と再開を使用して、専用 SQL プールのコストを削減することができます。 このソリューションは、既存のデータ オーケストレーション プロセスに簡単に含めることができます。 

次の手順に従って、自動一時停止と再開を設定します。

1. パイプラインを作成します。
1. パイプラインでパラメーターを設定します。
1. Azure Synapse ワークスペースの専用 SQL プールの一覧を確認します。
1. 一覧から一時停止または再開したくない専用 SQL プールをフィルター処理します。 
1. 各専用 SQL プールをループしてから、次の操作を行います。
    1. 専用 SQL プールの状態を確認します。
    1. 専用 SQL プールの状態を評価します。
    1. 専用 SQL プールを一時停止または再開します。

これらの手順は、Azure Synapse のシンプルなパイプラインにレイアウトされています。

![シンプルな Synapse パイプライン](./media/how-to-pause-resume-pipelines/simple-pipeline.png)


環境の性質によっては、ここで説明するプロセス全体が適用されない場合があり、適切な手順を選択するだけで済む場合があります。 ここで説明するプロセスは、開発、テスト、または PoC 環境ですべてのインスタンスを一時停止または再開するために使用できます。 運用環境では、インスタンスごとに一時停止または再開をスケジュールする可能性が高くなります。そのため、必要になるのは手順 5a から 5c までのみとなります。

上記の手順では、Azure Synapse と Azure SQL の REST API を使用します。

- [専用 SQL プールの操作](/rest/api/synapse/sqlpools)
 
- [Azure SQL Database REST API](/rest/api/sql)

Synapse パイプラインを使用すると、一時停止と再開を自動化できますが、任意のツールまたはアプリケーションを使ってこれらのコマンドをオンデマンドで実行できます。

## <a name="prerequisites"></a>前提条件

- 既存の [Azure Synapse ワークスペース](../get-started-create-workspace.md)
- 1 つ以上の[専用 SQL プール](../get-started-analyze-sql-pool.md)
- ワークスペースには、Azure 共同作成者ロールが割り当てられている必要があります。 「[ワークスペースの "Azure 共同作成者" ロールを Synapse 管理者に付与する](../security/how-to-set-up-access-control.md#step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace)」を参照してください。

## <a name="step-1-create-a-pipeline-in-synapse-studio"></a>手順 1: Synapse Studio でパイプラインを作成する。
1. ワークスペースに移動し、Synapse Studio を開きます。 
1. **[統合]** アイコンを選んでから、 **[+]** 記号を選択して新しいパイプラインを作成します。 
1. パイプラインに PauseResume という名前を付けます。

    ![Synapse Studio でパイプラインを作成する](./media/how-to-pause-resume-pipelines/create-pipeline.png) 

## <a name="step-2-create-pipeline-parameters"></a>手順 2: パイプライン パラメーターを作成する 

作成するパイプラインはパラメーター駆動型になります。 パラメーターを使用すると、複数のサブスクリプション、リソース グループ、または専用 SQL プール全体で使用できる汎用パイプラインを作成できます。 パイプライン画面の下部付近にある **[パラメーター]** タブを選択します。 **[+ 新規]** を選択して、次の各パラメーターを作成します。

    
|Name  |Type  |既定値  |説明|
|---------|---------|---------|-----------|
|ResourceGroup    |string        |Synapse          |専用 SQL プールのリソース グループの名前|
|SubscriptionID   |string        |`<SubscriptionID>` |リソース グループのサブスクリプション ID|
|WorkspaceName    |string        |Synapse          |ワークスペースの名前|
|SQLPoolName      |string        |SQLPool1         |専用 SQL プールの名前|
|PauseorResume    |string        |一時停止            |パイプライン実行の終了時に必要な状態|

![Synapse Studio のパイプライン パラメーター。](./media/how-to-pause-resume-pipelines/pipeline-parameters.png)

## <a name="step-3-create-list-of-dedicated-sql-pools"></a>手順 3: 専用 SQL プールの一覧を作成する
 
 **Web** アクティビティを設定します。このアクティビティは、専用 SQL プールを呼び出して専用 SQL プールの一覧を作成するために使用します (サーバーごとの一覧取得 REST API 要求)。 出力は、ワークスペース内の専用 SQL プールの一覧を含む JSON 文字列です。 JSON 文字列は次のアクティビティに渡されます。
1. **[アクティビティ]**  >  **[全般]** で、パイプラインの最初のステージとして **Web** アクティビティをパイプライン キャンバスにドラッグします。  
1. **[全般]** タブで、このステージに GET List という名前を付けます。 
1. **[設定]** タブを選択し、 **[URL]** エントリ領域をクリックして、 **[動的なコンテンツの追加]** を選びます。 以下の @concat 文字列関数を使用してパラメーター化された GET 要求をコピーし、動的コンテンツ ボックスに貼り付けます。 **[完了]** を選択します。
次のコードはシンプルな Get 要求です。

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools?api-version=2019-06-01-preview
    ```
    
    @concat 文字列関数を使用してパラメーター化された GET 要求:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools?api-version=2019-06-01-preview')
    ``` 
1. **[メソッド]** のドロップダウンを選択し、 **[Get]** を選びます。  
1. **[詳細設定]** を選択してコンテンツを展開します。 認証の種類として **[MSI]** を選択します。 [リソース] に、「`https://management.azure.com/`」と入力します 
    > [!IMPORTANT]
    > すべての Web アクティビティおよび REST API Web 呼び出しについて、Synapse パイプラインが専用 SQL プールに対して確実に認証されるようにする必要があります。 これらの REST API 呼び出しを実行するには、[マネージド ID](../../data-factory/control-flow-web-activity.md#managed-identity) が必要です。 
        
        
    ![専用 SQL プールの Web アクティビティ一覧](./media/how-to-pause-resume-pipelines/web-activity-list-sql-pools.png)



## <a name="step-4-filter-dedicated-sql-pools"></a>手順 4: 専用 SQL プールをフィルター処理する
一時停止または再開したくない専用 SQL プールを除外します。 Get list アクティビティから渡された値をフィルター処理するフィルター アクティビティを使用します。  この例では、名前に "prod" が含まれていないレコードを配列から抽出しています。 必要に応じて他の条件を適用します。 たとえば、Synapse ワークスペースの sku または名前をフィルター処理して、有効な専用 SQL プールのみが確実に識別されるようにします。
1. **[Iteration & conditionals]\(繰り返しと条件\)** の下にある **[Filter]** アクティビティを選択して、パイプライン キャンバスにドラッグします。    
![専用 SQL プールをフィルター処理する](./media/how-to-pause-resume-pipelines/filter-sql-pools.png)    
1. Get List Web アクティビティを Filter アクティビティに接続します。 Web アクティビティの緑色のタブを選択し、[フィルター] ボックスにドラッグします。
1. **[項目]** に「`@activity('Get list').output.value`」と入力します。ここで、GET List は前の Web アクティビティの名前です
1. **[条件]** に「`@not(endswith(item().name,'prod'))`」と入力します。 その後、配列内の残りのレコードは次のアクティビティに渡されます。

## <a name="step-5-create-a-foreach-loop"></a>手順 5: ForEach ループを作成する
ForEach アクティビティを作成して、各専用 SQL プールをループします。 
1. **[Iteration & conditionals]\(繰り返しと条件\)** の下にある **[ForEach]** アクティビティを選択して、パイプライン キャンバスにドラッグします。
1. **[全般]** タブで、アクティビティに名前を付けます。ここでは、"ForEach_pool" を使用しています。 
1. [設定] タブで、 **[項目]** 入力を選択し、 **[動的コンテンツの追加]** を選びます。 **[アクティビティの出力]** までスクロールし、フィルター アクティビティからの出力を選択します。 `.value` をアクティビティに追加します。 この値は `@activity('Filter_PROD').output.value` のようになるはずです。 **[完了]** を選択します。    
    ![専用 SQL プールをループする](./media/how-to-pause-resume-pipelines/loop-through-sql-pools.png)
1. **[アクティビティ]** タブを選択し、編集鉛筆を選んで ForEach ループ キャンバスを開きます。 

## <a name="step-5a-check-the-state-of-the-dedicated-sql-pools"></a>手順 5a: 専用 SQL プールの状態を確認する    
専用 SQL プールの状態を確認するには、手順 1 のような Web アクティビティが必要です。 このアクティビティでは、[Azure Synapse の専用 SQL プール状態の確認 REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#check-database-state) を呼び出します。 
1. **[全般]** の下にある **[Web]** アクティビティを選択して、パイプライン キャンバスにドラッグします。    
2. **[全般]** タブで、このステージに CheckState という名前を付けます。 
3. **[Settings]\(設定\)** タブを選択します。     
4. **[URL]** エントリ領域をクリックし、 **[動的コンテンツの追加]** を選択します。 以下の @concat 文字列関数を使用してパラメーター化された GET 要求をコピーして、動的コンテンツ ボックスに貼り付けます。 **[完了]** を選択します。 状態をもう一度確認するには、Get 要求を使用します。その場合、次の呼び出しを使用します。

    ```HTTP
    GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    @concat 文字列関数を使用してパラメーター化された Get 要求:
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',item().name,'?api-version=2019-06-01-preview')
    ```
    
    この場合、item().name を使用します。これは、ForEach ループからこのアクティビティに渡された手順 1 の専用 SQL プールの名前です。 パイプラインを使用して 1 つの専用 SQL プールを制御している場合は、ここに専用 SQL プールの名前を埋め込むか、パイプラインのパラメーターを使用することができます。 たとえば、pipeline().parameters.SQLPoolName を使用できます。

    出力は、(properties.status の) 状態など、専用 SQL プールの詳細を含む JSON 文字列です。 JSON 文字列は次のアクティビティに渡されます。 
1. **[メソッド]** のドロップダウンを選択し、 **[Get]** を選びます。 **[詳細設定]** を選択してコンテンツを展開します。 認証の種類として **[MSI]** を選択します。 [リソース] に、「`https://management.azure.com/`」と入力します 

![専用 SQL プールの状態を確認する](./media/how-to-pause-resume-pipelines/check-sql-pool-state.png)
    

    
## <a name="step-5b-evaluate-the-state-of-the-dedicated-sql-pools"></a>手順 5b: 専用 SQL プールの状態を評価する
目的の状態 (一時停止または再開) および現在の状態 (オンラインまたは一時停止済み) を評価してから、必要に応じて一時停止または再開を開始します。

1. **[Iteration & conditionals]\(繰り返しと条件\)** の下にある **[Switch]** アクティビティを選択して、パイプライン キャンバスにドラッグします。    
1. **[Switch]** アクティビティを **[CheckState]** アクティビティに接続します。 Web アクティビティの緑色のタブを選択し、[スイッチ] ボックスにドラッグします。  
1. **[全般]** タブで、このステージに State-PauseOrResume という名前を付けます。 

    目的の状態と現在の状態に基づき、次の 2 つの組み合わせのみで状態の変更が必要になります: 一時停止済み -> 再開またはオンライン -> 一時停止。 

1. **[アクティビティ]** タブで、以下のコードを **[式]** にコピーします。

    ```HTTP
    @concat(activity('CheckState').output.properties.status,'-',pipeline().parameters.PauseOrResume)
    ```
    
    ここで、Check State は現在の状態を定義する output.properties.status を含む前の Web アクティビティの名前であり、pipeline().parameters.PauseOrResume は目的の状態を示します。
    
    条件の確認では、目的の状態と現在の状態を確認します。 目的の状態が再開で、現在の状態が一時停止済みの場合は、再開アクティビティが Paused-Resume ケース内で呼び出されます。 目的の状態が一時停止で、現在の状態がオンラインの場合は、一時停止アクティビティが Online-Pause ケースで呼び出されます。 目的の状態が一時停止で現在の状態が一時停止済み、あるいは目的の状態が再開で現在の状態がオンラインなどの他のすべてのケースでは、アクションは不要であり、アクティビティがない既定のケースで処理されます。
1. [アクティビティ] タブで、 **[+ ケースの追加]** を選択します。  `Paused-Resume` および `Online-Pause` ケースを追加します。 
    ![専用 SQL プールの状態条件を確認する](./media/how-to-pause-resume-pipelines/check-condition.png)

### <a name="step-5c-pause-or-resume-dedicated-sql-pools"></a>手順 5c: 専用 SQL プールを一時停止または再開する     

一部の要件にのみ関連する最後の手順では、専用 SQL プールの一時停止または再開を開始します。 この手順では Web アクティビティをもう一度使用して、[Azure Synapse のコンピューティングの一時停止または再開 REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md#pause-compute) を呼び出します。 
1. アクティビティの編集鉛筆を選択し、**Web** アクティビティを State-PauseorResume キャンバスに追加します。 
1. **[設定]** タブを選択し、 **[URL]** エントリ領域をクリックして、 **[動的なコンテンツの追加]** を選びます。 以下の @concat 文字列関数を使用してパラメーター化された POST 要求をコピーし、動的コンテンツ ボックスに貼り付けます。 **[完了]** を選択します。 
1. **[メソッド]** のドロップダウンを選択し、 **[POST]** を選びます。
1. [本文] セクションに、「一時停止と再開」と入力します
1. **[詳細設定]** を選択してコンテンツを展開します。 認証の種類として **[MSI]** を選択します。 [リソース] に、「`https://management.azure.com/`」と入力します 
1. 以下のパラメーター化されたコードを使用して、再開機能の 2 つ目のアクティビティを追加します。

    ![専用 SQL プールを再開する](./media/how-to-pause-resume-pipelines/true-condition-resume.png)
    
    
    以下の例では、次の呼び出しを使用して POST 要求を呼び出し、専用 SQL プールを再開します。
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/resume?api-version=2019-06-01-preview HTTP/1.1
    ```
    
    @concat 文字列関数を使用して、上記の POST ステートメントをパラメーター化できます。
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/resume?api-version=2019-06-01-preview')
    ```
    
    この場合、アクティビティ 'Check State'.output.name を使用します。これには、スイッチ条件を通じてこのアクティビティに渡された手順 3a の専用 SQL プールの名前が含まれます。 単一データベースに対して 1 つのアクティビティを使用する場合は、ここに専用 SQL プールの名前を埋め込むか、パイプラインのパラメーターを使用することができます。 たとえば、pipeline().parameters.DatabaseName を使用できます。
    
    専用 SQL プールを一時停止する POST 要求:
    
    ```HTTP
    POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Synapse/workspaces/{workspace-name}/sqlPools/{database-name}/pause?api-version=2019-06-01-preview HTTP/1.1    
    ```    
    
    POST 要求は、次のように @concat 文字列関数を使用してパラメーター化できます。
    
    ```HTTP
    @concat('https://management.azure.com/subscriptions/',pipeline().parameters.SubscriptionID,'/resourceGroups/',pipeline().parameters.ResourceGroup,'/providers/Microsoft.Synapse/workspaces/',pipeline().parameters.WorkspaceName,'/sqlPools/',activity('CheckState').output.name,'/pause?api-version=2019-06-01-preview')
    ```

## <a name="pipeline-run-output"></a>パイプラインの実行の出力

完全なパイプラインが実行されると、以下に一覧表示されている出力が表示されます。 **[デバッグ]** モードを選ぶか、 **[トリガーの追加]** を選択することで、パイプラインを実行できます。 以下のパイプラインの結果の場合、"ResourceGroup" という名前のパイプライン パラメーターが、Synapse ワークスペースが 2 つある単一のリソース グループに設定されていました。 1 つは testprod という名前でフィルター処理され、もう 1 つは test1 という名前でした。 test1 専用 SQL プールが一時停止されたため、ジョブで再開が開始されました。

![パイプラインの実行の出力](./media/how-to-pause-resume-pipelines/pipeline-run-output.png)

## <a name="save-your-pipeline"></a>パイプラインを保存する

パイプラインを保存するには、パイプラインの上にある **[すべて発行]** を選択します。

## <a name="schedule-your-pause-or-resume-pipeline-to-run"></a>一時停止または再開パイプラインを実行するようにスケジュールを設定する

パイプラインをスケジュールするには、パイプラインの上部にある **[トリガーの追加]** を選択します。 画面の指示に従って、指定された時間に実行されるようにパイプラインをスケジュールします。

![パイプラインの実行時間を設定するトリガーを選択する](./media/how-to-pause-resume-pipelines/trigger.png)

## <a name="next-steps"></a>次のステップ

Azure Synapse のマネージド ID と、専用 SQL プールにマネージド ID を追加する方法の詳細については、以下を参照してください。

[Azure Synapse ワークスペース マネージド ID](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics)

[ワークスペースのマネージド ID にアクセス許可を付与する](../security/how-to-grant-workspace-managed-identity-permissions.md)
