---
title: Web ジョブでバックグラウンド タスクを実行する
description: Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する方法を説明します。 さまざまなスクリプト形式から選択し、CRON 式を使用して実行します。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 6/25/2021
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 3ecb31f6d008fda51d03c3e0d2d44b881397b466
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "122651578"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する

[Azure portal](https://portal.azure.com) を使用して、実行可能ファイルまたはスクリプトをアップロードするための WebJobs をデプロイします。 バックグラウンド タスクは、Azure App Service で実行できます。

Azure App Service の代わりに Visual Studio 2019 を使用して WebJobs を開発およびデプロイする場合は、「[Visual Studio を使用して Web ジョブを展開する](webjobs-dotnet-deploy-vs.md)」を参照してください。

## <a name="overview"></a>概要
Web ジョブは、Web アプリ、API アプリ、またはモバイル アプリと同じインスタンスでプログラムやスクリプトを実行できる [Azure App Service](index.yml) の機能です。 Web ジョブの使用に追加コストはかかりません。

Azure WebJobs SDK と WebJobs を使用して、多くのプログラミング タスクを単純化できます。 Web ジョブは App Service on Linux ではまだサポートされていません。 詳細については、「 [Azure Web ジョブ SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。

Azure Functions は、プログラムとスクリプトを実行する別の方法を提供します。 Web ジョブと Functions の比較については、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="webjob-types"></a>Web ジョブの種類

次の表で、*継続的* Web ジョブと *トリガーされる* Web ジョブの違いを説明します。


|継続的  |トリガー  |
|---------|---------|
| Web ジョブが作成されるとすぐに開始します。 ジョブが終了しないようにするため、プログラムまたはスクリプトは、その動作を無限ループ内で実行します。 ジョブが終了した場合でも、再開することができます。 通常、WebJobs SDK と共に使用されます。 | 手動またはスケジュールに従ってトリガーされたときにのみ開始します。 |
| Web アプリが実行されているすべてのインスタンスで実行されます。 必要に応じて、Web ジョブを 1 つのインスタンスに制限できます。 |Azure が負荷分散用に選択した 1 つのインスタンス上で実行されます。|
| リモート デバッグをサポートします。 | リモート デバッグをサポートしません。|
| コードは `\site\wwwroot\app_data\Jobs\Continuous` に配置されます。 | コードは `\site\wwwroot\app_data\Jobs\Triggered` に配置されます。 |

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>スクリプトまたはプログラムでサポートされるファイルの種類

次のファイルの種類がサポートされます。

* .cmd、.bat、.exe (Windows コマンドを使用)
* .ps1 (PowerShell を使用)
* .sh (Bash を使用)
* .php (PHP を使用)
* .py (Python を使用)
* .js (Node.js を使用)
* .jar (Java を使用)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> 継続的 Web ジョブを作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> アプリケーションでソース管理が構成されている場合は、ソース管理の統合の一環として Webjobs をデプロイする必要があります。 アプリケーションのソース管理を構成した後、WebJobs を Azure portal から追加することはできません。

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

1. アプリの **[App Service]** ページの左側のウィンドウで、**WebJobs** を検索して選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

1. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

1. **[Web ジョブの追加]** 設定を、次の表に示されているように入力します。

   ![構成する必要がある [Web ジョブの追加] 設定を示すスクリーンショット。](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myContinuousWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | 継続的 | [Web ジョブの種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **スケール** | 複数のインスタンス | 継続的 Web ジョブでのみ使用できます。 プログラムまたはスクリプトがすべてのインスタンスで実行されるか、1 つのインスタンスだけで実行されるかどうかを決定します。 複数のインスタンスで実行するオプションは、Free または Shared [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)には適用されません。 | 

1. **[OK]** を選択します。 

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。 Web ジョブが追加されたというメッセージが表示されても、表示されない場合は、 **[更新]** を選択します。 

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/list-continuous-webjob.png)

1. 継続的 Web ジョブを停止または再開するには、一覧の Web ジョブを右クリックし、 **[停止]** または **[開始]** を選択します。

    ![継続的 Web ジョブを停止する](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>手動でトリガーされる Web ジョブを作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択します。 

1. 一覧から Web アプリ、API アプリ、またはモバイル アプリを選択します。 

1. アプリの **[App Service]** ページの左側のウィンドウで、**WebJobs** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

1. **[Web ジョブの追加]** 設定を、次の表に示されているように入力します。 

   ![手動でトリガーされる Web ジョブを作成するために指定する必要がある設定を示すスクリーンショット。](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myTriggeredWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。|
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [Web ジョブの種類](#webjob-types)については、この記事で前述のとおりです。 |
   | **トリガー** | マニュアル | |

4. **[OK]** を選択します。

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。 Web ジョブが追加されたというメッセージが表示されても、表示されない場合は、 **[更新]** を選択します。  

   ![Web ジョブトリガーの一覧](./media/web-sites-create-web-jobs/list-triggered-webjob.png)

7. Web ジョブを実行するには、一覧でその名前を右クリックし、 **[実行]** を選択します。
   
    ![Web ジョブの実行](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>スケジュールされた Web ジョブを作成する

スケジュールされた Web ジョブもトリガーされます。 指定するスケジュールでトリガーが自動的に実行されるように、スケジュール設定できます。
 
<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択します。 

1. 一覧から Web アプリ、API アプリ、またはモバイル アプリを選択します。 

1. アプリの **[App Service]** ページの左側のウィンドウで、**WebJobs** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

1. **[Web ジョブ]** ページで、 **[追加]** を選択します。

   ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[Web ジョブの追加]** 設定を、次の表に示されているように入力します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | 設定      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **名前** | myScheduledWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [Web ジョブの種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **トリガー** | スケジュール | スケジュールを確実に動作させるために、Always On 機能を有効にします。 Always On 機能は、Basic、Standard、および Premium の価格レベルでのみ利用できます。|
   | **CRON 式** | 0 0/20 * * * * | [CRON 式](#ncrontab-expressions)については、次のセクションで説明します。 |

4. **[OK]** を選択します。

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。 Web ジョブが追加されたというメッセージが表示されても、表示されない場合は、 **[更新]** を選択します。  

   ![Web ジョブの一覧 - スケジュール済み](./media/web-sites-create-web-jobs/list-scheduled-webjob.png)

## <a name="ncrontab-expressions"></a>NCRONTAB 式

[NCRONTAB 式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)は、ポータルで入力するか、`settings.job` ファイルを Web ジョブの *.zip* ファイルのルートに含めることができます。例を以下に示します。

```json
{
    "schedule": "0 */15 * * * *"
}
```

詳細については、「[Scheduling a triggered WebJob (トリガーされる Web ジョブのスケジュール設定)](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)」を参照してください。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="manage-webjobs"></a>Web ジョブの管理

[Azure portal](https://portal.azure.com) で、サイトで実行されている個々の Web ジョブの実行状態を管理できます。 **[設定]**  >  **[WebJobs]** にアクセスし、[Web ジョブ] を選択するだけで、Web ジョブを開始および停止することができます。 また、Web ジョブを実行する Webhook のパスワードを表示および変更することもできます。  

また、`1` という値を持つ `WEBJOB_STOPPED` という名前の[アプリケーション設定を追加](configure-common.md#configure-app-settings)して、サイトで実行されているすべての Web ジョブを停止することもできます。 これは、ステージング スロットと運用スロットの両方で競合する Web ジョブが実行されないようにするのに便利な方法です。 同様に、`1` の値を `WEBJOBS_DISABLE_SCHEDULE` 設定に使用して、サイトまたはステージング スロットでトリガーされた Web ジョブを無効にすることもできます。 スロットの場合は、設定自体がスワップされないように、 **[デプロイ スロットの設定]** オプションを必ず有効にしてください。    

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>ジョブ履歴を表示する

1. Web ジョブを選択し、履歴を表示するには、 **[ログ]** を選択します。
   
   ![[ログ] ボタン](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. **[WebJob Details]\(Web ジョブの詳細\)** ページで、詳細を確認する実行時間を選択します。
   
   ![Web ジョブの詳細](./media/web-sites-create-web-jobs/webjobdetails.png)

3. **[WebJob Run Details]\(Web ジョブの実行の詳細\)** ページで、 **[出力の切り替え]** を選択すると、ログの内容のテキストが表示されます。
   
    ![Web ジョブ実行の詳細](./media/web-sites-create-web-jobs/webjobrundetails.png)

   出力されるテキストを別のブラウザー ウィンドウに表示するには、 **[ダウンロード]** を選択します。 テキスト自体をダウンロードするには、 **[ダウンロード]** を右クリックし、ブラウザーのオプションを使用してファイルの内容を保存します。
   
5. Web ジョブの一覧に移動するには、ページの上部にある **[Web ジョブ]** 階層リンクを選択します。

    ![[Web ジョブ] 階層リンク](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![履歴ダッシュボードに表示された Web ジョブの一覧](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> 次のステップ

Azure WebJobs SDK と Web ジョブを使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure Web ジョブ SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。
