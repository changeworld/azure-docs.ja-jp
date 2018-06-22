---
title: Azure Stack の診断
description: Azure Stack の診断のログ ファイルを収集する方法
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: b966ed4f1a9a8e659fbce185a807573d5321b251
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801655"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack の診断ツール

Azure Stack は、相互に連携して作用する複数のコンポーネントによって構成された大規模なコレクションです。 これらすべてのコンポーネントは、独自の一意のログを生成します。 このため、問題を診断するのは困難な作業になります。相互に作用する複数の Azure Stack コンポーネントのエラーについては、特にそう言えます。 

Microsoft の診断ツールは、ログ収集のメカニズムを簡単で効率的なものにする助けとなります。 次の図は、Azure Stack のログ収集ツールのしくみを示しています。

![Azure Stack の診断ツール](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>トレース コレクター
 
トレース コレクターは既定で有効になり、Azure Stack コンポーネント サービスからすべての Windows イベント トレーシング (ETW) ログを収集するためにバック グラウンドで継続的に実行されます。 ETW ログは、一般的なローカル共有に 5 日間を上限として格納されます。 この制限に達した場合、新しいファイルが作成されると最も古いファイルは削除されます。 各ファイルで許容される既定の最大サイズは 200 MB です。 サイズ チェックは 2 分間隔で行われ、現在のファイルが 200 MB 以上であれば保存され、新しいファイルが生成されます。 また、イベント セッションごとに生成されるファイルの合計サイズには、8 GB の制限が設定されています。 

## <a name="log-collection-tool"></a>ログ収集ツール
 
PowerShell コマンドレット **Get-AzureStackLog** を使用して、Azure Stack 環境のすべてのコンポーネントからログを収集できます。 これらは、ユーザーの定義した場所に zip ファイルで保存されます。 Azure Stack テクニカル サポート チームが問題を解決するためにお客様のログを必要とする場合は、このツールを実行するようにお願いすることがあります。

> [!CAUTION]
> これらのログ ファイルには個人を特定できる情報 (PII) が含まれている可能性があります。 ログ ファイルを公開する前に、この点を考慮してください。
 
以下に、収集されるログの種類をいくつか例示します。
*   **Azure Stack デプロイ ログ**
*   **Windows イベント ログ**
*   **Panther ログ**
*   **クラスター ログ**
*   **Storage 診断ログ**
*   **ETW ログ**

これらのファイルは、トレース コレクターによって収集され、共有に保存されます。 必要に応じて、**Get-AzureStackLog** PowerShell コマンドレットを使用して、それらを収集できます。
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Azure Stack Development Kit (ASDK) システムで Get-AzureStackLog を実行するには
1. ホストに **AzureStack\CloudAdmin** でログインします。
2. 管理者として PowerShell ウィンドウを開きます。
3. **Get-AzureStackLog** PowerShell コマンドレットを実行します。

**例:**

  すべてのロールのすべてのログを収集します。

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  VirtualMachines ロールと BareMetal ロールからログを収集します。

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  ログ ファイルに対する過去 8 時間以内の日付範囲のフィルター処理で、VirtualMachines ロールと BareMetal ロールからログを収集します。
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  ログ ファイルに対する過去 8 時間から 2 時間以内の日付範囲のフィルター処理で、VirtualMachines ロールと BareMetal ロールからログを収集します。

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1804-and-later"></a>Azure Stack 統合システムバージョン 1804 以降で Get-AzureStackLog を実行するには

統合システムでログ収集ツールを実行するには、特権エンド ポイント (PEP) へのアクセス権が必要です。 PEP を使用して統合システムでログを収集するのに実行できるスクリプト例を次に示します。

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- **OutputSharePath** パラメーターと **OutputShareCredential** パラメーターは、外部の共有フォルダーにログをアップロードするときに使用されます。
- 前の例で示したように、**FromDate** パラメーターと **ToDate** パラメーターを使用して、特定の期間のログを収集できます。 これは、統合システムへの更新パッケージ適用後のログ収集などのシナリオに役立ちます。


### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems-version-1803-and-earlier"></a>Azure Stack 統合システムバージョン 1803 以前で Get-AzureStackLog を実行するには

統合システムでログ収集ツールを実行するには、特権エンド ポイント (PEP) へのアクセス権が必要です。 PEP を使用して統合システムでログを収集するのに実行できるスクリプト例を次に示します。

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- PEP からログを収集する場合は、ハードウェア ライフサイクル ホスト (HLH) マシン上の保存先になる **OutputPath** パラメーターを指定します。 また、場所が暗号化されていることを確認します。
- **OutputSharePath** パラメーターと **OutputShareCredential** パラメーターはオプションであり、外部の共有フォルダーにログをアップロードするときに使用されます。 これらのパラメーターは、**OutputPath** に "*追加して*" 使用します。 **OutputPath** が指定されていない場合、ログ収集ツールはストレージの PEP VM のシステム ドライブを使用します。 この場合、ドライブ容量が限られているためにスクリプトが失敗する可能性があります。
- 前の例で示したように、**FromDate** パラメーターと **ToDate** パラメーターを使用して、特定の期間のログを収集できます。 これは、統合システムへの更新パッケージ適用後のログ収集などのシナリオに役立ちます。


### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK および統合システムの両方に関するパラメーターの考慮事項

- **FromDate** パラメーターと **ToDate** パラメーターが指定されない場合は、既定で過去 4 時間分のログが収集されます。
- **TimeOutInMinutes** パラメーターを使用して、ログ収集のタイムアウトを設定できます。 既定では 150 (2.5 時間) に設定されています。
- バージョン 1805 以降では、ダンプ ファイルのログ収集は既定で無効になります。 これを有効にするには、**IncludeDumpFile** スイッチ パラメーターを使用します。 
- 現時点では **FilterByRole** パラメーターを使用して、次のロールごとにログ収集をフィルター処理できます。

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | ネットワーク                    |
   | ACSFabric              | ドメイン                           | NonPrivilegedAppGateway    |
   | ACSFabric            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSFabric         | [ギャラリー]                          | SlbVips                    |
   | ACSWac                 | ゲートウェイ                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | Storage                    |   
   | NCAzureBridge          | IBC                              | ストレージ アカウント            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | iDns                             | テナント                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | インフラストラクチャ                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | クラウド                  | KeyVaultControlPlane             | VirtualMachines            |
   | クラスター                | KeyVaultDataPlane                | WAS                        |
   | コンピューティング                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>グラフィカル ユーザー インターフェイスを使用してログを収集する
Get-AzureStackLog コマンドレットのパラメーターを指定して Azure Stack ログを取得する代わりに、主要な Azure Stack ツールの GitHub ツール リポジトリ (http://aka.ms/AzureStackTools) にあるオープン ソースの Azure Stack ツールを使用することもできます。

**ERCS_AzureStackLogs.ps1** PowerShell スクリプトは GitHub ツール リポジトリに格納され、定期的に更新されています。 最新バージョンを入手するために、http://aka.ms/ERCS から直接ダウンロードすることをお勧めします。 管理 PowerShell セッションから開始されるこのスクリプトは、特権エンドポイントに接続し、指定されたパラメーターで Get-AzureStackLog を実行します。 パラメーターが指定されていない場合、スクリプトは、既定でグラフィカル ユーザー インターフェイスを使用して、パラメーターを入力するように求めます。

ERCS_AzureStackLogs.ps1 PowerShell スクリプトの詳細については、[短いビデオ](https://www.youtube.com/watch?v=Utt7pLsXEBc)を視聴するか、Azure Stack ツールの GitHub リポジトリにある [Readme ファイル](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) を参照します。 

### <a name="additional-considerations"></a>追加の考慮事項

* このコマンドは、どのロールに基づいてログが収集されるかによって、実行にいくらかの時間がかかります。 また、関連する要素には、ログ収集に指定した期間と、Azure Stack 環境のノード数が含まれます。
* ログの収集と同時に、コマンドで指定した **OutputSharePath** パラメーターに作成された新しいフォルダーを確認します。
* ロールごとに、個別の zip ファイル内にログがあります。 収集されたログのサイズによっては、1 つのロールのログが複数の zip ファイルに分割されることがあります。 そのようなロールでは、すべてのログ ファイルを単一のフォルダーに解凍したい場合は、一括で解凍するツール (7zip など) を使用します。 そのロールのすべての圧縮済みファイルを選択し、**[ここに展開]** を選択します。 これで、そのロールのすべてのログ ファイルが 1 つのマージされたフォルダーに解凍されます。
* また、**Get-AzureStackLog_Output.log** と呼ばれるファイルが、圧縮済みログ ファイルを含むフォルダーに作成されます。 このファイルはコマンド出力のログで、ログ収集中の問題のトラブルシューティングに使用できます。 ログ収集の実行後に予期したログ ファイルがない場合を除き、無視してかまわない `PS>TerminatingError` エントリがログ ファイルに含まれることがあります。
* 特定のエラーを調べるには、複数のコンポーネントのログが必要な場合があります。
    -   インフラストラクチャのすべての VM のシステム ログとイベント ログは、*VirtualMachines* ロールで収集されます。
    -   すべてのホストのシステム ログとイベント ログは、*BareMetal* ロールで収集されます。
    -   フェールオーバー クラスターおよび Hyper-V のイベント ログは、*Storage* ロールで収集されます。
    -   ACS ログは、*Storage* ロールと *ACS* ロールで収集されます。

> [!NOTE]
> 記憶域スペースを効率的に使用し、ログで占領されないようにすることは極めて重要であるため、収集されるログにはサイズと有効期間の制限が強制されます。 ただし、問題を診断する場合に、こうした制限のためにもう存在していない可能性のあるログが必要となることがあります。 このため、外部の記憶域スペース (Azure のストレージ アカウント、オンプレミスの追加の記憶装置など) に 8 ～ 12 時間ごとにログをオフロードし、要件に応じて 1 ～ 3 か月間そこに保存しておくことを**強くお勧めします**。 また、この記憶域の場所が暗号化されていることを確認します。

## <a name="next-steps"></a>次の手順
[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)

