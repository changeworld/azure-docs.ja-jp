---
title: Azure Stack 検証ツールの使用 |Microsoft Docs
description: Azure Stack の診断のログ ファイルを収集する方法。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 63a198b082c7486de2392153291a11be5bcb2f9e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103225"
---
# <a name="validate-azure-stack-system-state"></a>Azure Stack システムの状態を検証する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack オペレーターは、システムの正常性と状態をオンデマンドで把握できることが不可欠です。 Azure Stack の検証ツール (**Test-azurestack**) は PowerShell コマンドレットであり、システム上で一連のテストを実行して、障害があればそれを特定できます。 通常このツールは、Microsoft カスタマー サービス サポート (CSS) にアクセスして問題について問い合わせるときに、[特権エンドポイント (PEP)](azure-stack-privileged-endpoint.md) から実行することが求められます。 手元にシステム全体の正常性および状態情報があれば、CSS は詳細ログを収集して分析し、エラーが発生した領域に焦点を当て、お客様と連携して問題を解決できます。

## <a name="running-the-validation-tool-and-accessing-results"></a>検証ツールを実行して結果にアクセスする

前述のとおり、検証ツールは PEP 経由で実行されます。 各テストは、PowerShell ウィンドウで**合格/不合格**のいずれかの状態を返します。 さらに、詳細な HTML レポートが作成され、後で[ログの収集](azure-stack-diagnostics.md)時にアクセスできます。 以下に示すのは、エンド ツー エンド検証テスト プロセスの概要です。 

1. 特権エンドポイント (PEP) にアクセスします。 PEP セッションを確立するために、次のコマンドを実行します。

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > ASDK ホスト コンピューターで PEP にアクセスするには、-ComputerName に対して azs-ercs01 を使用します。

2. PEP から、次のコマンドを実行します。 

   ```powershell
   Test-AzureStack
   ```

   詳細については、「[パラメーターに関する考慮事項](azure-stack-diagnostic-test.md#parameter-considerations)」と「[ユース ケースの例](azure-stack-diagnostic-test.md#use-case-examples)」を参照してください。

3. いずれかのテストで **FAIL** が報告された場合は、次を実行します。

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   このコマンドレットは、Test-azurestack によって生成されたログを収集します。 診断ログの詳細については、「[Azure Stack diagnostics tools (Azure Stack 診断ツール)](azure-stack-diagnostics.md)」を参照してください。 テストで **WARN** が報告される場合は、ログを収集したり CSS に問い合わせたりしないでください。

4. CSS により検証ツールを実行するように指示された場合、CSS の担当者は、問題のトラブルシューティングを続行するために、収集したログの提出を要求します。

## <a name="tests-available"></a>利用可能なテスト

検証ツールでは、現在の状態への洞察を提供してシステム内の問題を確認できる、一連のシステム レベルのテストや基本的なクラウド シナリオを実行できます。

### <a name="cloud-infrastructure-tests"></a>クラウド インフラストラクチャのテスト

これらの影響度が低いテストは、インフラストラクチャ レベルで動作し、さまざまなシステム コンポーネントや機能に関する情報を提供します。 現時点では、テストは、次のカテゴリにグループ化されます。

| テスト カテゴリ                                        | -Include および -Ignore の引数 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack のアラートの概要                            | AzsAlertSummary                   |
| Azure Stack バックアップ共有のアクセシビリティの概要       | AzsBackupShareAccessibility       |
| Azure Stack のコントロール プレーンの概要                    | AzsControlPlane                   |
| Azure Stack Defender の概要                         | AzsDefenderSummary                |
| Azure Stack のホスティング インフラストラクチャ ファームウェアの概要  | AzsHostingInfraFWSummary          |
| Azure Stack クラウド ホスティング インフラストラクチャの概要     | AzsHostingInfraSummary            |
| Azure Stack クラウド ホスティング インフラストラクチャの利用 | AzsHostingInfraUtilization        |
| Azure Stack インフラストラクチャの容量                  | AzsInfraCapacity                  |
| Azure Stack インフラストラクチャのパフォーマンス               | AzsInfraPerformance               |
| Azure Stack インフラストラクチャ ロールの概要              | AzsInfraRoleSummary               |
| Azure Stack 更新プログラムの概要                           | AzsInfraUpdateSummary             |
| Azure Stack ポータルおよび API の概要                   | AzsPortalAPISummary               |
| Azure Stack スケール ユニットの VM イベント                     | AzsScaleUnitEvents                |
| Azure Stack スケール ユニットの VM リソース                  | AzsScaleUnitResources             |
| Azure Stack SDN 検証の概要                   | AzsSDNValidation                  |
| Azure Stack サービス ファブリック ロールの概要              | AzsSFRoleSummary                  |
| Azure Stack の BMC の概要                              | AzsStampBMCSummary                |
| Azure Stack ストレージ サービスの概要                 | AzsStorageSvcsSummary             |
| Azure Stack SQL ストアの概要                        | AzsStoreSummary                   |
| Azure Stack の VM 配置の概要                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>クラウド シナリオのテスト

上記のインフラストラクチャ テストだけでなく、インフラストラクチャ コンポーネント全体で機能をチェックするための、クラウド シナリオのテストを実行する機能もあります。 リソースのデプロイが関係するため、これらのテストを実行するは、クラウド管理者の資格情報が必要です。 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

検証ツールでは、次のクラウドのシナリオがテストされます。
- リソース グループの作成   
- プランの作成              
- オファーの作成            
- ストレージ アカウントの作成   
- 仮想マシンの作成 
- Blob ストレージの操作   
- キュー ストレージの操作  
- テーブル ストレージの操作  

## <a name="parameter-considerations"></a>パラメーターに関する考慮事項

- パラメーター **List** は、利用可能なすべてのテスト カテゴリを表示するために使用できます。

- パラメーター **Include** と **Ignore** は、テスト カテゴリを含めたり除外したりするために使用できます。 これらの引数で使用する短縮形の詳細については、「[利用可能なテスト](azure-stack-diagnostic-test.md#tests-available)」セクションを参照してください。

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- テナント VM はクラウド シナリオ テストの一部としてデプロイされます。 これを向こうにするには **DoNotDeployTenantVm** を使用できます。 

- クラウド シナリオ テストを実行するには、**ServiceAdminCredential** パラメーターを指定する必要があります。これについては「[ユース ケースの例](azure-stack-diagnostic-test.md#use-case-examples)」のセクションで説明しています。

- **BackupSharePath** と **BackupShareCredential** は、インフラストラクチャ バックアップ設定をテストするときに使用されます。これについては「[ユース ケースの例](azure-stack-diagnostic-test.md#use-case-examples)」セクションで説明しています。

- 検証ツールは、Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable、および OutVariable などの一般的な PowerShell パラメーターもサポートします。 詳細については、「[About Common Parameters (一般的なパラメーターについて)](https://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。  

## <a name="use-case-examples"></a>ユース ケースの例

### <a name="run-validation-without-cloud-scenarios"></a>クラウド シナリオがない検証の実行

クラウド シナリオ テストの実行をスキップするには、**ServiceAdminCredential** パラメーターを指定せずに検証ツールを実行します。 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>クラウド シナリオがある検証の実行

検証ツールに **ServiceAdminCredentials** パラメーターを指定すると、既定でクラウド シナリオ テストが実行されます。 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

残りのテストを実行せずにクラウド シナリオのみを実行したい場合は、**Include** パラメーターを使用してそのようにすることができます。 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

クラウド管理者のユーザー名は UPN 形式serviceadmin@contoso.onmicrosoft.com (Azure AD) で入力する必要があります。 入力を求められたら、クラウド管理者のアカウントのパスワードを入力します。

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>更新プログラムまたは修正プログラムをインストールする前に、検証ツールを実行してシステムの対応状態をテストします。

更新または修正プログラムのインストールを開始する前に、検証ツールを実行して Azure Stack の状態を確認する必要があります。

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>インフラストラクチャのバックアップ設定をテストするには、検証ツールを実行します。

インフラストラクチャのバックアップを構成する*前に*、**AzsBackupShareAccessibility** テストを使用して、バックアップ共有パスと資格情報をテストできます。 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

バックアップを構成した*後*、PEP セッションの実行から **AzsBackupShareAccessibility** を実行して、ERCS から共有にアクセスできることを検証できます。

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

構成済みのバックアップ共有で新しい資格情報をテストするには、以下を実行します。 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>次の手順

Azure Stack 診断ツールと問題のログ記録の詳細については、「[Azure Stack の診断ツール](azure-stack-diagnostics.md)」を参照してください。

トラブルシューティングの詳細については、「[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)」を参照してください。