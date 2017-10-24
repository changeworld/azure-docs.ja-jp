---
title: "Azure Stack の診断"
description: "Azure Stack の診断のログ ファイルを収集する方法"
services: azure-stack
author: adshar
manager: byronr
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 10/2/2017
ms.author: adshar
ms.openlocfilehash: 9b1fbbf63ddd8bac2c1a76bbcd5daca69e2513f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack の診断ツール

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*
 
Azure Stack は、相互に連携して作用する複数のコンポーネントによって構成された大規模なコレクションです。 これらすべてのコンポーネントは、独自の一意のログを生成します。 このため、問題を診断するのは困難な作業になります。相互に作用する複数の Azure Stack コンポーネントのエラーについては、特にそう言えます。 

Microsoft の診断ツールは、ログ収集のメカニズムを簡単で効率的なものにする助けとなります。 次の図は、Azure Stack のログ収集ツールのしくみを示しています。

![ログ収集ツール](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>トレース コレクター
 
トレース コレクターは、既定で有効になっています。 継続的にバックグラウンドで実行され、Azure Stack のコンポーネント サービスから Windows イベント トレーシング (ETW) のすべてのログを収集し、共通のローカル共有に格納します。 

トレース コレクターについて知っておくべき重要事項を次に示します。
 
* トレース コレクターは、既定のサイズ制限で継続的に実行されます。 各ファイルに対して許容される既定の最大サイズ (200 MB) は、サイズの上限では**ありません**。 サイズ チェックは定期的に行われ (現在は 2 分ごと)、現在のファイルが 200 MB 以上であればそれは保存され、新しいファイルが生成されます。 また、イベント セッションごとに生成されるファイルの合計サイズには、8 GB (構成可能) の制限が設定されています。 この制限に達した場合、新しいファイルが作成されると最も古いファイルは削除されます。
* ログの保存期間には 5 日間の制限があります。 この制限も構成可能です。 
* 各コンポーネントは、JSON ファイルでトレースの構成プロパティを定義します。 JSON ファイルは `C:\TraceCollector\Configuration` に格納されています。 必要に応じてこれらのファイルを編集し、収集したログの保存期間とサイズの制限を変更できます。 ファイルへの変更を有効にするには、*Microsoft Azure Stack トレース コレクター* サービスを再起動する必要があります。
* 次の例は、XRP VM の FabricRingServices 操作に対する、トレース構成の JSON ファイルです。 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    このパラメーターは、ファイルの保存期間を制御します。 古いログ ファイルは削除されます。
* **MaxSizeInMB**

    このパラメーターは、1 つのファイルのサイズのしきい値を制御します。 サイズが上限に達すると、新しい .etl ファイルが作成されます。
* **TotalSizeInMB**

    このパラメーターは、イベント セッションから生成された .etl ファイルの合計サイズを制御します。 ファイルの合計サイズがこのパラメーターの値よりも大きくなると、古いファイルは削除されます。
  
## <a name="log-collection-tool"></a>ログ収集ツール
 
PowerShell コマンド `Get-AzureStackLog` を使用して、Azure Stack 環境のすべてのコンポーネントからログを収集できます。 これらは、ユーザーの定義した場所に zip ファイルで保存されます。 テクニカル サポート チームが問題を解決するためにお客様のログを必要とする場合は、このツールを実行するようにお願いすることがあります。

> [!CAUTION]
> これらのログ ファイルには個人を特定できる情報 (PII) が含まれている可能性があります。 ログ ファイルを公開する前に、この点を考慮してください。
 
以下に、収集されるログの種類をいくつか例示します。
*   **Azure Stack デプロイ ログ**
*   **Windows イベント ログ**
*   **Panther ログ**

   VM 作成の問題をトラブルシューティングするには、以下を使用します。
*   **クラスター ログ**
*   **Storage 診断ログ**
*   **ETW ログ**

これらのファイルはトレース コレクターによって収集され、`Get-AzureStackLog` で取得された場所から共有に保管されます。
 
**Azure Stack Development Kit (ASDK) システムで Get-AzureStackLog を実行するには**
1.  ホストに AzureStack\AzureStackAdmin でログインします。
2.  管理者として PowerShell ウィンドウを開きます。
3.  `Get-AzureStackLog` を実行します。  

    **例**

    - すべてのロールのすべてのログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - VirtualMachines ロールと BareMetal ロールからログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - ログ ファイルに対する過去 8 時間以内の日付範囲のフィルター処理で、VirtualMachines ロールと BareMetal ロールからログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)`

    - ログ ファイルに対する過去 8 時間から 2 時間以内の日付範囲のフィルター処理で、VirtualMachines ロールと BareMetal ロールからログを収集します。

      `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)`

**Azure Stack 統合システムで Get-AzureStackLog を実行するには**

統合システムでログ収集ツールを実行するには、特権エンド ポイント (PEP) へのアクセス権が必要です。 PEP を使用して統合システムでログを収集するのに実行できるスクリプト例を次に示します。

```
$ip = "<IP OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDREESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- PEP からログを収集する場合は、HLH マシン上の場所となる `OutputPath` パラメーターを指定します。 また、場所が暗号化されていることを確認します。
- `OutputSharePath` および `OutputShareCredential` パラメーターはオプションであり、外部の共有フォルダーにログをアップロードするときに使用されます。 これらのパラメーターは、`OutputPath` に*追加して*使用します。 `OutputPath` が指定されていない場合、ログ収集ツールはストレージの PEP VM のシステム ドライブを使用します。 この場合、ドライブ容量が限られているためにスクリプトが失敗する可能性があります。
- 前の例で示したように、`FromDate` パラメーターおよび `ToDate` パラメーターは、特定の期間についてログを収集するのに使用できます。 これは、統合システムへの更新パッケージ適用後のログ収集などのシナリオに役立ちます。

**ASDK および統合システムの両方に関するパラメーターの考慮事項:**

- `FromDate` および `ToDate` パラメーターが指定されない場合、既定では過去 4 時間以内のログが収集されます。
- `TimeOutInMinutes` パラメーターを使用してログ収集のタイムアウトを設定できます。 既定では 150 (2.5 時間) に設定されています。

- 現時点では `FilterByRole` パラメーターを使用して、次のロールごとにログ収集をフィルター処理できます。

   |   |   |   |
   | - | - | - |
   | `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
   | `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
   | `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
   | `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
   | `BRP`                     | `CA`                     | `CPI`                |
   | `CRP`                     | `DeploymentMachine`      | `DHCP`               |
   |`Domain`                   | `ECE`                    | `ECESeedRing`        |        
   | `FabricRing`              | `FabricRingServices`     | `FRP`                |
   |` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
   | `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
   | `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
   | `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
   | `SeedRingServices`        | `SLB`                    | `SQL`                |     
   | `SRP`                     | `Storage`                | `StorageController`  |
   | `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
   | `WAS`                     | `WASPUBLIC`              | `WDS`                |


その他の注意事項

* このコマンドは、どのロールに基づいてログが収集されるかによって、実行にいくらかの時間がかかります。 また、関連する要素には、ログ収集に指定した期間と、Azure Stack 環境のノード数が含まれます。
* ログの収集が完了したら、コマンドで指定した `-OutputPath` パラメーターに作成された新しいフォルダーを確認します。
* ロールごとに、個別の zip ファイル内にログがあります。 収集されたログのサイズによっては、1 つのロールのログが複数の zip ファイルに分割されることがあります。 そのようなロールでは、すべてのログ ファイルを単一のフォルダーに解凍したい場合は、一括で解凍するツール (7zip など) を使用します。 そのロールのすべての圧縮済みファイルを選択し、**[ここに展開]** を選択します。 これで、そのロールのすべてのログ ファイルが 1 つのマージされたフォルダーに解凍されます。
* また、`Get-AzureStackLog_Output.log` と呼ばれるファイルが、圧縮済みログ ファイルを含むフォルダーに作成されます。 このファイルはコマンド出力のログで、ログ収集中の問題のトラブルシューティングに使用できます。
* 特定のエラーを調べるには、複数のコンポーネントのログが必要な場合があります。
    -   インフラストラクチャのすべての VM のシステム ログとイベント ログは、*VirtualMachines* ロールで収集されます。
    -   すべてのホストのシステム ログとイベント ログは、*BareMetal* ロールで収集されます。
    -   フェールオーバー クラスターおよび Hyper-V のイベント ログは、*Storage* ロールで収集されます。
    -   ACS ログは、*Storage* ロールと *ACS* ロールで収集されます。

> [!NOTE]
> 記憶域スペースを効率的に使用し、ログで占領されないようにすることは極めて重要であるため、収集されるログにはサイズと有効期間の制限が強制されます。 ただし、問題を診断する場合に、こうした制限のためにもう存在していない可能性のあるログが必要となることがあります。 このため、外部の記憶域スペース (Azure のストレージ アカウント、オンプレミスの追加の記憶装置など) に 8 ～ 12 時間ごとにログをオフロードし、要件に応じて 1 ～ 3 か月間そこに保存しておくことを**強くお勧めします**。 また、この記憶域の場所が暗号化されていることを確認します。

## <a name="next-steps"></a>次のステップ
[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)
