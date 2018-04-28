---
title: Azure Stack で検証テストを実行する | Microsoft Docs
description: Azure Stack の診断のログ ファイルを収集する方法
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: mabrigg
ms.openlocfilehash: c28216ced2a7cd2995c55a9faacb93cf27e60c65
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Azure Stack の検証テストを実行する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*
 
Azure Stack の状態を検証できます。 問題が発生している場合は、Microsoft カスタマー サービス サポートに連絡してください。 サポートは、ユーザーに管理ノードから Test-AzureStack を実行するよう依頼します。 この検証テストによって障害が分離されます。 それによりサポートは詳細なログを分析し、エラーが発生した領域に焦点を絞って、問題の解決でユーザーと協力することができます。

## <a name="run-test-azurestack"></a>Test-AzureStack を実行する

問題が発生している場合は、Microsoft カスタマー サービス サポートに連絡してから、**Run Test-AzureStack** を実行します。

1. 問題が発生しています。
2. Microsoft カスタマー サービス サポートに連絡してください。
3. 特権エンドポイントから **Test-AzureStack** を実行します。
    1. 特権エンドポイントにアクセスします。 手順については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。 
    2. ASDK で、**AzureStack\CloudAdmin** として管理ホストにログインします。  
    統合システムでは、OEM ハードウェア ベンダーから提供された管理の特権エンド ポイントの IP アドレスを使用する必要があります。
    3. PowerShell を管理者として開きます。
    4. 次のコマンドを実行します: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. 次のコマンドを実行します: `Test-AzureStack`
4. どのテストも失敗を報告する場合は、`Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` を実行します。このコマンドレットは、Test-AzureStack からログを収集します。 診断ログの詳細については、「[Azure Stack diagnostics tools (Azure Stack 診断ツール)](azure-stack-diagnostics.md)」を参照してください。
5. **SeedRing** ログを Microsoft カスタマー サービス サポートに送信します。 Microsoft カスタマー サービス サポートは、ユーザーと協力して問題を解決します。

## <a name="reference-for-test-azurestack"></a>Test-AzureStack のリファレンス

このセクションには、Test-AzureStack コマンドレットの概要と検証レポートの要約が含まれています。

### <a name="test-azurestack"></a>Test-AzureStack

Azure Stack の状態を検証します。 このコマンドレットは、Azure Stack ハードウェアおよびソフトウェアの状態を報告します。 サポート スタッフは、このレポートを使用して、Azure Stack サポート ケースを解決するための時間を短縮できます。

> [!Note]  
> Test-AzureStack は、クラウドの停止を発生させない障害 (1 台の故障したディスクや 1 つの物理ホスト ノードの障害など) を検出する可能性があります。

#### <a name="syntax"></a>構文

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parameters

| パラメーター               | 値           | 必須 | 既定値 |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | いいえ        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | いいえ        | FALSE   |
| AdminCredential         | PSCredential    | いいえ        | 該当なし      |
<!-- | StorageConnectionString | String          | いいえ        | 該当なし      | 1802 ではサポートされていません -->
| 一覧表示                    | SwitchParameter | いいえ        | FALSE   |
| 無視                  | String          | いいえ        | 該当なし      |
| Include (含める)                 | String          | いいえ        | 該当なし      |

Test-AzureStack コマンドレットは、Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable、および OutVariable の一般的なパラメーターをサポートしています。 詳細については、「[About Common Parameters (一般的なパラメーターについて)](http://go.microsoft.com/fwlink/?LinkID=113216)」を参照してください。 

### <a name="examples-of-test-azurestack"></a>Test-AzureStack の例

次の例では、**CloudAdmin** としてサインインし、特権エンドポイント (PEP) にアクセスしていることを前提にしています。 手順については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>クラウド シナリオなしで Test-AzureStack を対話的に実行する

PEP セッションで、次を実行します。

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>クラウド シナリオを使用して Test-AzureStack を実行する

Test-AzureStack を使用して、Azure Stack に対してクラウド シナリオを実行できます。 これらのシナリオは、次のとおりです。

 - リソース グループの作成
 - プランの作成
 - オファーの作成
 - ストレージ アカウントの作成
 - 仮想マシンの作成
 - テスト シナリオで作成されたストレージ アカウントを使用して BLOB 操作を実行する
 - テスト シナリオで作成されたストレージ アカウントを使用してキュー操作を実行する
 - テスト シナリオで作成されたストレージ アカウントを使用してテーブル操作を実行する

クラウド シナリオには、クラウド管理者の資格情報が必要です。 
> [!Note]  
> Active Directory フェデレーション サービス (ADFS) の資格情報を使用してクラウド シナリオを実行することはできません。 **Test-AzureStack** コマンドレットは、PEP 経由でのみアクセス可能です。 ただし、PEP は ADFS の資格情報をサポートしていません。

クラウド管理者のユーザー名を UPN 形式 serviceadmin@contoso.onmicrosoft.com (AAD) で入力します。 入力を求められたら、クラウド管理者のアカウントのパスワードを入力します。

PEP セッションで、次を実行します。

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>クラウド シナリオなしで Test-AzureStack を実行する

PEP セッションで、次を実行します。

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>使用可能なテスト シナリオを一覧表示する

PEP セッションで、次を実行します。

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>指定されたテストを実行する

PEP セッションで、次を実行します。

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

特定のテストを除外するには:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>検証テスト

次の表は、Test-AzureStack による検証テストの実行をまとめたものです。

| Name                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure Stack クラウド ホスティング インフラストラクチャの概要                                                                                  |
| Azure Stack ストレージ サービスの概要                                                                                              |
| Azure Stack インフラストラクチャ ロール インスタンスの概要                                                                                  |
| Azure Stack クラウド ホスティング インフラストラクチャの利用                                                                              |
| Azure Stack インフラストラクチャの容量                                                                                               |
| Azure Stack ポータルおよび API の概要                                                                                                |
| Azure Stack Azure Resource Manager 証明書の概要                                                                                               |
| インフラストラクチャ管理コントローラー、ネットワーク コントローラー、ストレージ サービス、および特権エンドポイントのインフラストラクチャ ロール          |
| インフラストラクチャ管理コントローラー、ネットワーク コントローラー、ストレージ サービス、および特権エンドポイントのインフラストラクチャ ロール インスタンス |
| Azure Stack インフラストラクチャ ロールの概要                                                                                           |
| Azure Stack クラウド サービス ファブリック サービス                                                                                         |
| Azure Stack インフラストラクチャ ロール インスタンスのパフォーマンス                                                                              |
| Azure Stack クラウド ホストのパフォーマンスの概要                                                                                        |
| Azure Stack サービス リソース消費の概要                                                                                  |
| Azure Stack スケール ユニットの重大なイベント (過去 8 時間)                                                                             |
| Azure Stack ストレージ サービス物理ディスクの概要                                                                               |

## <a name="next-steps"></a>次の手順

 - Azure Stack 診断ツールと問題のログ記録の詳細については、「[Azure Stack diagnostics tools (Azure Stack 診断ツール)](azure-stack-diagnostics.md)」を参照してください。
 - トラブルシューティングの詳細については、「[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)」を参照してください。