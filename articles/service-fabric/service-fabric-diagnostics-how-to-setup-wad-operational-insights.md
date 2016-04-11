<properties
   pageTitle="Azure 診断と Azure Operational Insights を使用してログを収集する方法 | Microsoft Azure"
   description="この記事では、Azure で実行されている Service Fabric クラスターのログを収集するように Azure 診断と Azure Operational Insights を設定する方法について説明します"
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="toddabel"/>


# Azure 診断と Operational Insights を使用して Service Fabric クラスターのログを収集する

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。1 か所でログを収集すると、クラスターと、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決が簡単になります。ログのアップロードと収集には、ログを Azure Storage にアップロードする Azure 診断拡張機能を使用する方法があります。

Azure [Operational Insights](https://azure.microsoft.com/services/operational-insights/) (Microsoft Operations Management Suite の一部) は、ログの分析と検索が簡単になる SaaS ソリューションです。以下の手順では、クラスター内の VM で Azure 診断拡張機能を設定してログを中央のストアにアップロードし、ログを取得するように Operational Insights を構成して、Operational Insights ポータルで確認する方法について説明します。

Operational Insights は、格納されているストレージ テーブルの名前によって、Service Fabric クラスターからアップロードされたさまざまな種類のログを識別します。つまり、Operational Insights が検索する名前と同じ名前のストレージ テーブルにログをアップロードするように、Azure 診断拡張機能を構成する必要があります。このドキュメントの構成設定例では、ストレージ テーブルの名前の付け方を示します。


## 前提条件
これらのツールは、このドキュメントの操作の一部を実行するために使用されます。

* [Azure PowerShell](../powershell-install-configure.md)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)


## クラスター ログの表示と検索のために Operational Insights を設定する
「[Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)」では、Azure ストレージ アカウントに送信されるログを有効にする詳細な手順が説明されています。クラスターに診断が設定され、ログがストレージ アカウントにアップロードされたら、次の手順は、Operational Insights ポータルですべてのクラスター ログの表示、検索、およびクエリを行うように Operational Insights を設定することです。

### Operational Insights ワークスペースを作成する
Operational Insights ワークスペースの作成手順については、以下の記事を参照してください。この記事では、ワークスペースを作成する 2 つの方法について説明しています。Azure ポータルとサブスクリプションベースのアプローチを選択します。このドキュメントの以降のセクションでは、Operational Insights ワークスペース名が必要です。ストレージ アカウントなど、すべてのクラスター リソースの作成に使用したものと同じサブスクリプションを使用して、Operational Insights ワークスペースを作成します。

[Operational Insights の運用](https://technet.microsoft.com/library/mt484118.aspx)

### クラスター ログを表示するように Operational Insights ワークスペースを構成する
前述のように Operational Insights ワークスペースを作成したら、次に、診断拡張機能によってクラスターからアップロードされる Azure Storage テーブルのログを取得するようにワークスペースを構成します。現在、この構成は、Operational Insights ポータルでは使用できません。PowerShell コマンドを使用する必要があります。次の PowerShell スクリプトを実行します。

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

ストレージ アカウントで Azure テーブルから読み取る Operational Insights ワークスペースを構成したら、ポータルにログインし、Operational Insights リソースの **[Storage]** タブを開きます。次のような画面が表示されます。![Azure ポータルの Operational Insights ストレージの構成](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Operational Insights のログの検索と表示
指定したストレージ アカウントからログを読み取るように Operational Insights ワークスペースを構成してから、Operational Insights UI にログが表示されるまでに、最大で 10 分かかることがあります。新しいログが生成されていることを確認するには、Service Fabric プラットフォームから運用イベントが生成されるように、Service Fabric アプリケーションをクラスターにデプロイすることをお勧めします。

1. ログを表示するには、Operational Insights ポータルのメイン ページで、**[ログの検索]** を選択します。![Operational Insights の [ログの検索] オプション](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. ログの検索ページで、**Type=ServiceFabricOperationalEvent** というクエリを使用します。次のようにクラスターの運用ログが表示されます。Actor プログラミング モデルのすべてのログを表示するには、**Type=ServiceFabricReliableActorEvent** というクエリを実行します。Reliable Services プログラミング モデルのすべてのログを表示するには、**Type=ServiceFabricReliableServiceEvent** と入力します。![Operational Insights のログのクエリと表示](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 問題解決に役立つサンプル クエリ
ここでは、いくつかのシナリオと、そのトラブルシューティングに使用できるクエリの例を紹介します。

1. **Service Fabric から特定のサービスについて RunAsync が呼び出されたかどうかを確認するには:** サービスの起動時にクラッシュが発生した場合を除外する必要があるときに、この手順を実行します。この手順を実行するには、以下のようなクエリを使用し (サービス名とアプリケーション名は、デプロイした実際の名前に合わせて変更します)、結果が返されるかどうかを確認します。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **ステートフル サービスを実行中で、そのサービスから Service Fabric によるエラーとマークされた例外がスローされたかどうかを確認するには:** 次のようなクエリを使用してイベントを検索します。

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **すべてのデプロイされたアプリケーションとサービスで Actor メソッドからスローされた例外に対応するイベントを検索するには:** 次のようなクエリを使用できます。

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 新しい EventSource チャネルのログを収集およびアップロードするように診断を更新する
デプロイする予定の新しいアプリケーションを示す新しい EventSource チャネルのログを収集するように診断を更新するには、既存のクラスターに対する診断の設定について説明した[前述のセクション](#deploywadarm)と同じ手順を実行する必要があります。

WadConfigUpdate.json の EtwEventSourceProviderConfiguration セクションを更新して、新しい EventSource のエントリを追加してから、リソース マネージャー コマンドで構成の更新を適用する必要があります。アップロードのテーブルは、アプリケーションの ETW イベントを読み取るように Operational Insights 用に構成されたテーブルなので、同じ (ETWEventTable) です。


## 次のステップ
問題を解決する際に確認する必要があるイベントの詳細については、[Reliable Actors](service-fabric-reliable-actors-diagnostics.md) と [Reliable Services](service-fabric-reliable-services-diagnostics.md) で生成される診断イベントを参照してください。

<!---HONumber=AcomDC_0330_2016-->