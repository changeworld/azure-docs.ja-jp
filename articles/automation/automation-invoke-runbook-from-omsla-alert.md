---
title: Log Analytics アラートから Azure Automation Runbook を呼び出す
description: この記事では、Azure で Log Analytics アラートから Automation Runbook を呼び出す方法の概要を示します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0094362ee083511b05027f22b37ed62d56d68d41
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217135"
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Log Analytics アラートから Azure Automation Runbook を呼び出す

Azure Log Analytics で、結果が条件と一致する場合にアラート レコードを作成するようにアラートを構成することができます。 そのアラートから Azure Automation Runbook を自動的に実行して、問題の自動修復を試すことができます。 

たとえば、アラートは、プロセッサ使用率の長期的な上昇を示している場合があります。 また、ビジネス アプリケーションの機能に不可欠なアプリケーション プロセスで障害が発生している可能性もあります。 その場合、Runbook から、対応するイベントが Windows のイベント ログに書き込まれることがあります。  

アラートの構成で Runbook を呼び出す方法は 2 つあります。

* Webhook を使用する。
   * これは、Log Analytics ワークスペースが Automation アカウントにリンクされていない場合に使用できる唯一のオプションです。
   * Automation アカウントを既に Log Analytics ワークスペースにリンクしている場合も引き続きこのオプションを使用できます。  

* 直接 Runbook を選択する。
   * このオプションは、Log Analytics ワークスペースが Automation アカウントにリンクされている場合のみ使用できます。

## <a name="calling-a-runbook-by-using-a-webhook"></a>webhook を使用して Runbook を呼び出す

Webhook を使用して、単一の HTTP 要求を通して Azure Automation で特定の Runbook を開始することができます。 アラート アクションとして webhook を使用して Runbook を呼び出すように[ログ アラートの webhook アクション](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)を構成する前に、この方法で呼び出される Runbook 用に [webhook を作成する](automation-webhooks.md#creating-a-webhook)必要があります。 アラート ルールの構成時に参照できるように webhook URL を忘れずにメモしておいてください。   

## <a name="calling-a-runbook-directly"></a>直接 Runbook を呼び出す

Log Analytics ワークスペースに Automation & Control サービスをインストールして構成することができます。 アラートの Runbook アクションのオプションを構成するときに、**[Runbook の選択]** ボックスの一覧ですべての Runbook を表示し、そのアラートに対応して実行する特定の Runbook を選択できます。 選択した Runbook は、Azure ワークスペース、または Hybrid Runbook Worker で実行できます。 

Runbook オプションを使用してアラートを作成すると、その Runbook 用に webhook が作成されます。 その webhook は、Automation アカウントに移動し、選択した Runbook の webhook ウィンドウを開くと表示されます。 

アラートを削除しても、webhook は削除されません。 これは問題ではありません。 webhook は孤立した項目になるので、整理された Automation アカウントを維持するには、最終的に手動で削除する必要があります。  

## <a name="characteristics-of-a-runbook"></a>Runbook の特性

Log Analytics アラートから Runbook を呼び出す 2 つの方法には、アラート ルールを構成する前に理解しておくべき特性があります。 

アラート データは、**SearchResult** という単一のプロパティに JSON 形式で格納されています。 この形式は、標準的なペイロードを含んだ Runbook アクションや webhook アクションに使用されます。 (**RequestBody** の **IncludeSearchResults:True** など) カスタム ペイロードを含んだ webhook アクションのプロパティが **SearchResults** になります。

**Object** 型の **WebhookData** という Runbook 入力パラメーターが必要です。 これには必須と任意があります。 アラートは、この入力パラメーターを使用して検索結果を Runbook に渡します。

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
**WebhookData** を PowerShell オブジェクトに変換するためのコードが必要です。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** はオブジェクトの配列です。 各オブジェクトには、1 つの検索結果からの値を含むフィールドが含まれています。


## <a name="example-walkthrough"></a>例のチュートリアル

次のグラフィカルな Runbook の例は、このプロセスの仕組みを示しています。 Windows サービスを開始します。

![Windows サービスを開始するためのグラフィカルな Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Runbook には、**WebhookData** という **Object** 型の 1 つの入力パラメーターがあります。 これには、**SearchResult** を含むアラートから渡された webhook データが含まれます。

![Runbook の入力パラメーター](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

この例では、Log Analytics で **SvcDisplayName_CF** と **SvcState_CF** という 2 つのカスタム フィールドを作成しました。 これらのフィールドは、システム イベント ログに書き込まれたイベントからサービスの表示名とサービスの状態 (つまり、実行中または停止中) を抽出します。 その後、Windows システムで印刷スプーラー サービスが停止したときに検出できるように次の検索クエリを含むアラート ルールを作成します。

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

関心のある任意のサービスを指定できます。 この例では、Windows OS に付属している既存のサービスの 1 つを参照しています。 アラート アクションは、この例で使用されている Runbook を実行し、Hybrid Runbook Worker で動作するように構成されています。これは、ターゲット システムで有効になっています。   

Runbook コード アクティビティ **Get Service Name from LA** は、JSON 形式の文字列をオブジェクトの種類に変換し、項目 **SvcDisplayName_CF** をフィルター処理します。 また、Windows サービスの表示名を抽出し、それを次のアクティビティに渡します。次のアクティビティは、このサービスが停止していることを確認してから、サービスの再開を試みます。 **SvcDisplayName_CF** は、この例を説明するために Log Analytics に作成された[カスタム フィールド](../log-analytics/log-analytics-custom-fields.md)です。

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

サービスが停止すると、Log Analytics のアラート ルールは、一致を検出して Runbook をトリガーし、アラートのコンテキストを Runbook に送信します。 Runbook は、サービスが停止していることを確認します。 停止している場合はサービスの再開を試み、正しく開始されたことを確認し、結果を表示します。     

または、Automation アカウントを Log Analytics ワークスペースにリンクしていない場合は、webhook アクションを使用してアラート ルールを構成できます。 この webhook アクションで Runbook がトリガーされます。 また、JSON 形式の文字列を変換し、前述のガイダンスに従って **SearchResult** に対してフィルターを実行するように Runbook を構成します。    

>[!NOTE]
> ご使用のワークスペースが[新しい Log Analytics クエリ言語](../log-analytics/log-analytics-log-search-upgrade.md)にアップグレードされている場合は、webhook ぺイロードが変わります。 形式の詳細については、「[Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse)」を参照してください。

## <a name="next-steps"></a>次の手順

* ログ検索を使用した Azure アラートの作成について詳しくは、[Azure でのログ アラート](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)に関する記事をご覧ください。

* webhook を使用して Runbook をトリガーする方法を理解するには、「[Azure Automation Webhook](automation-webhooks.md)」を参照してください。
