---
title: Azure Stack Edge デバイスのリモート サポート、診断、および修復を有効にする
description: Azure Stack Edge デバイスでリモート サポートを有効にする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 364845dc046664a7af4d9f7ac6fbb965a818430a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092799"
---
# <a name="remote-support-and-diagnostics-for-azure-stack-edge"></a>Azure Stack Edge のリモート サポートと診断 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]


> [!IMPORTANT]
> リモート サポートはパブリック プレビュー段階であり、Azure Stack Edge バージョン 2110 以降に適用されます。

お使いの Azure Stack Edge デバイスでリモート サポートを有効にすると、Microsoft のエンジニアがデバイスにリモートでアクセスして問題を診断および修復できるようになります。 この機能を有効にする場合、アクセスのレベルとアクセスの期間に同意することになります。 

この記事ではリモート サポート機能に関して、機能を使用するタイミング、機能を有効にする方法などを含めて説明するとともに、Microsoft のエンジニアがデバイス上でリモート実行可能になる操作の一覧を示します。


## <a name="about-remote-support"></a>リモート サポートについて

Azure Stack Edge で問題が発生した場合、お客様は通常、サポート パッケージを収集して Microsoft サポートに提出する必要があります。 場合によっては、問題を診断して修復するのに十分な情報がログに含まれていないケースもあります。 その際、Microsoft サポートよりお客様に連絡し、リモート サポートに対してお客様の同意をいただくことになります。

リモート サポートの利点を次に示します。

- Microsoft サポートがお客様と対面での会議を設定する必要がないため、問題をより迅速に解決できます。
- デバイスで実行されたすべての操作を含む、リモート サポート セッションのトランスクリプトを確認できます。
- お客様はデータを完全に制御できるとともに、いつでも同意を取り消すことができます。 セッションを終了し忘れた場合、セッションへのアクセスはアクセスの有効期限が切れると自動的に無効になります。

## <a name="how-remote-support-works"></a>リモート サポートのしくみ

次の図は、リモート サポートのしくみを示しています。

![Azure Stack Edge でのリモート サポートの図](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/remote-support-flow-1.png)

1. Azure portal より、デバイスのリモート サポートに対する同意を提出します。 また、デバイスに対するアクセスのレベルと期間も設定します。
1. リモート サポートの同意と要求は Azure portal から Azure Stack Edge リソース プロバイダー (RP) に送信され、その後デバイスに送信されます。 デバイス エージェントは RP に対して、接続を可能にするハイブリッド接続資格情報を要求します。
1. Azure ハイブリッド接続を確立するために使用される、ハイブリッド接続資格情報が作成されます。 この接続には次の特徴があります。
    
    - デバイス固有です。 各デバイスが独自の接続を持ち、接続は共有されません。
    - Microsoft サポートが安全で監査済みの準拠したチャネルを使用して、デバイスへの Just-In-Time (JIT) アクセスを行えるようにします。  
    - この接続は *https* プロトコルを使用してポート 443 経由で行われ、トラフィックは TLS 1.2 で暗号化されます。
     
1. デバイス エージェントは、このハイブリッド接続上で、Azure サポート センターのブラウザー インターフェイスを介して送信される要求に対するリッスンを開始します。 
1. ブラウザーがハイブリッド接続に対する接続を要求し、その要求はデバイスに送信され、制限付き PowerShell セッションが開きます。 同意がある場合、要求は受け入れられます。 デバイスに同意がない場合、その接続は拒否されます。 

接続が確立されると、このセキュリティで保護された接続を介してすべての通信が行われます。 

この接続を介して Microsoft サポートが実行できる操作は、[Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview) (JEA) を使用して付与されたアクセス レベルに基づいて *"制限"* されます。 リモート サポート セッション中に Microsoft サポートが実行できるコマンドレットの詳細については、この記事内の[許可される Microsoft サポートの操作の一覧](#operations-allowed-in-remote-support)に関するセクションをご覧ください。


## <a name="enable-remote-support"></a>リモート サポートを有効にする

Azure Stack Edge デバイスのリモート サポートを設定するには、以下の手順に従います。

1. Azure portal でデバイスの Azure Stack Edge リソースに移動した後、 **[診断]** に移動します。
1. 既定では、Microsoft はデバイスへのリモート アクセス権を持っておらず、リモート サポートの状態は **[無効]** と表示されます。 この機能を有効にするには、 **[Configure remote support]\(リモート サポートの構成\)** を選択します。

    ![[Configure remote support]\(リモート サポートの構成\) が強調表示されている、Azure Stack Edge でリモート サポートを有効にしているスクリーンショット](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-1.png)

1. **[アクセス レベル]** に **[診断]** を選択して、Microsoft サポートに診断情報をリモートで収集するための JIT アクセスを提供します。 サポート チームは必要に応じて、リモートでの修復アクションを許可するために **[Diagnostics and repair]\(診断と修復\)** を選択することをお勧めします。 

    各アクセス レベルに応じて、デバイス上で異なるリモート コマンドのセットが有効になります。そのため、利用できる操作のセットも異なります。 

    - 診断では主に読み取り操作が可能になるため、ほとんどの `Get` コマンドレットを使用できます。
    - 診断と修復では読み取り/書き込みアクセスが可能になるため、`Get` コマンドレットに加えて、`Set`、`Add`、`Start`、`Restart`、`Stop`、`Invoke`、`Remove` コマンドレットも使用できます。
    
    詳細については、この記事内の[各アクセス レベルで許可されるサポートの操作の一覧](#operations-allowed-in-remote-support)に関するセクションをご覧ください。

1. リモート アクセスを提供する期間を選択します。 期間は 7 日、15 日、21 日、30 日のいずれかに設定できます。 この期間が終了すると、デバイスへのリモート アクセスは自動的に無効になります。 

1. 任意の時点でアクセスを取り消すには、 **[Do not allow access]\(アクセスを許可しない\)** に設定します。 この操作により、既存のセッションがある場合には終了し、新しいセッションを確立できなくなります。

1. リモート サポートを設定したら、 **[適用]** を選択して設定を有効にします。 **[適用]** を選択すると、制御された安全な環境から診断情報をリモートで収集することに対する同意が Microsoft に提出されます。

    ![アクセス レベルと期間が強調表示されている Azure Stack Edge のリモート サポート設定のスクリーンショット](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/enable-remote-support-2.png)    

## <a name="remote-support-examples"></a>リモート サポートの例

以下のシナリオ例では、デバイスでリモート サポートが有効になっている場合に、さまざまな操作を実行する方法を示します。 

リモート サポート操作を実行するには、まず、デバイスの [PowerShell インターフェイスに接続](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)し、次にコマンドレットを実行する必要があります。

### <a name="list-existing-remote-sessions"></a>既存のリモート セッションを一覧表示する

`Get-HcsRemoteSupportSession` コマンドレットを使用して、指定した日数内にデバイスに対して行われたすべてのリモート セッションを一覧表示します。

```powershell
Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays <Number of days>
```
過去 10 日間に構成されたすべてのリモート サポート セッションの出力例を次に示します。 

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -IncludeTranscriptContents $false -NumberOfDays 10

SessionId                : 3c135cba-f479-4fef-8dbb-a2b52b744504
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 10:41:03 PM +00:00
SessionEndTime           : 8/19/2021 10:44:31 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.Lp+Myohb.20210
                           819154101.txt
SessionTranscriptContent :

SessionId                : c0f2d002-66a0-4d54-87e4-4b1b949ad686
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/19/2021 7:41:19 PM +00:00
SessionEndTime           : 8/19/2021 7:58:20 PM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.j0lCd5Tm.20210
                           819124117.txt
SessionTranscriptContent :

SessionId                : ca038e58-5344-4377-ab9c-c857a27c8b73
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/19/2021 10:49:39 PM +00:00
SessionEndTime           : 8/20/2021 12:49:40 AM +00:00
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.YHmes94q.
                           20210819154937.txt
SessionTranscriptContent :

[10.126.76.20]: PS>
```


### <a name="get-details-on-a-specific-remote-session"></a>特定のリモート セッションの詳細を取得する

コマンドレット `Get-HcsRemoteSupportSession` を使用して、ID *SessionID* を持つリモート セッションの詳細を取得します。

```powershell
Get-HcsRemoteSupportSession -SessionId <SessionId> -IncludeSessionTranscript $true
```

**[診断]** オプションを使用してリモート サポートが設定された特定のセッションの出力例を次に示します。 この場合の `AccessLevel` は `ReadOnly` です。

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId 360706a2-c530-419f-932b-55403e19502e -IncludeTranscriptContents $true

SessionId                : 360706a2-c530-419f-932b-55403e19502e
RemoteApplication        : Powershell
AccessLevel              : ReadOnly
ControlSession           : False
SessionStartTime         : 8/26/2021 2:35:37 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteDiagnostics\PowerShell_transcript.HW4J1T2.u7qF2J2d.
                           20210826073536.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826073536
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteDiagnostics
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 10976
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}



                           PS>CommandInvocation(Get-Command): "Get-Command"
                           CommandInvocation(Get-HcsApplianceInfo): "Get-HcsApplianceInfo"

[10.126.76.20]: PS>
```
リモート サポートに **[Diagnostics and repair]\(診断と修復\)** オプションが設定された場合の出力例を次に示します。 リモート サポート セッションの `AccessLevel` は `ReadWrite` です。

```output
[10.126.76.20]: PS>Get-HcsRemoteSupportSession -SessionId add360db-4593-4026-93d5-6d6d05d39046 -IncludeTranscriptContents $true

SessionId                : add360db-4593-4026-93d5-6d6d05d39046
RemoteApplication        : Powershell
AccessLevel              : ReadWrite
ControlSession           : False
SessionStartTime         : 8/26/2021 2:57:08 PM +00:00
SessionEndTime           :
SessionTranscriptPath    : C:\ProgramData\JEAConfiguration\Transcripts\RemoteRepair\PowerShell_transcript.HW4J1T2.ZroHb8Un.20210
                           826075705.txt
SessionTranscriptContent : **********************
                           Windows PowerShell transcript start
                           Start time: 20210826075705
                           Username: WORKGROUP\HW4J1T2$
                           RunAs User: WORKGROUP\SYSTEM
                           Configuration Name: RemoteRepair
                           Machine: HW4J1T2 (Microsoft Windows NT 10.0.17763.0)
                           Host Application: C:\Windows\system32\wsmprovhost.exe -Embedding
                           Process ID: 21832
                           PSVersion: 5.1.17763.10520
                           PSEdition: Desktop
                           PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.17763.10520
                           BuildVersion: 10.0.17763.10520
                           CLRVersion: 4.0.30319.42000
                           WSManStackVersion: 3.0
                           PSRemotingProtocolVersion: 2.3
                           SerializationVersion: 1.1.0.1
                           **********************
                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> ParameterBinding(Get-Command): name="ErrorAction"; value="SilentlyContinue"
                           >> CommandInvocation(Measure-Object): "Measure-Object"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Count"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Out-Default"
                           >> ParameterBinding(Measure-Object): name="InputObject"; value="Exit-PSSession"
                           PS>ParameterBinding(Select-Object): name="InputObject";
                           value="Microsoft.PowerShell.Commands.GenericMeasureInfo"

                           Count
                           -----
                               2

                           PS>CommandInvocation(Get-Command): "Get-Command"
                           >> ParameterBinding(Get-Command): name="Name"; value="Out-Default, Exit-PSSession"
                           >> ParameterBinding(Get-Command): name="CommandType"; value="Alias, Function, Filter, Cmdlet,
                           Configuration"
                           >> ParameterBinding(Get-Command): name="Module"; value=""
                           >> ParameterBinding(Get-Command): name="ListImported"; value="True"
                           >> CommandInvocation(Select-Object): "Select-Object"
                           >> ParameterBinding(Select-Object): name="Property"; value="Name, Namespace, HelpUri, CommandType,
                           ResolvedCommandName, OutputType, Parameters"
                           >> ParameterBinding(Select-Object): name="InputObject"; value="Out-Default"

                           Name                : Out-Default
                           Namespace           : Microsoft.PowerShell.Core
                           HelpUri             : https://go.microsoft.com/fwlink/?LinkID=113362
                           CommandType         : Cmdlet
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {[Transcript, System.Management.Automation.ParameterMetadata], [InputObject,
                                                 System.Management.Automation.ParameterMetadata], [Verbose,
                                                 System.Management.Automation.ParameterMetadata], [Debug,
                                                 System.Management.Automation.ParameterMetadata]...}

                           >> ParameterBinding(Select-Object): name="InputObject"; value="Exit-PSSession"
                           Name                : Exit-PSSession
                           Namespace           :
                           HelpUri             :
                           CommandType         : Function
                           ResolvedCommandName :
                           OutputType          : {}
                           Parameters          : {}

                           PS>CommandInvocation(Get-Command): "Get-Command"
[10.126.76.20]: PS>
```
 
### <a name="collect-remote-session-transcripts"></a>リモート セッションのトランスクリプトを収集する

監査の要件によっては、トランスクリプトの確認が必要な場合があります。 リモート セッションのトランスクリプトを収集するには、以下の手順に従います。

1. ローカル UI で、 **[トラブルシューティング] > [サポート]** の順に移動します。 サポート パッケージを収集します。
1. サポート パッケージを収集したら、サポート パッケージをダウンロードします。 zip 圧縮されたフォルダーを展開します。 トランスクリプトは、サポート パッケージの **SupportTranscripts** フォルダーにあります。 

    ![サポート パッケージで SupportTranscripts フォルダーが強調表示されているスクリーンショット](media/azure-stack-edge-gpu-remote-support-diagnostics-repair/transcript-folder-support-package-1.png)


 
## <a name="operations-allowed-in-remote-support"></a>リモート サポートで許可される操作

以下のセクションでは、Microsoft サポートがリモート サポート セッション中に実行できる許可されたコマンドレットの一覧を示します。 コマンドレットが利用可能かどうかは、アクセス レベルの設定 ( **[診断]** または **[Diagnostics and repair]\(診断と修復\)** ) 別に分類して記載されています。

#### <a name="azure-stack-edge-cmdlets"></a>Azure Stack Edge コマンドレット

| コマンドレット                      | 診断 | 診断と修復 | 説明 |
|-----------------------------------------------|-------------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Add-HcsExternalVirtualSwitch                  |             | Y                   | デバイス上で Kubernetes を構成する新しい外部仮想スイッチを作成します。  |
| Add-HcsVirtualNetwork                         |             | Y                   | 指定されたネットワーク インターフェイス上で仮想スイッチを作成します。|
| Get-AcsHealthStatus                           | Y           | Y                   | Azure Consistent Service Providers の正常性状態を取得します。 |
| Get-AzureDataBoxEdgeRole                      | Y           | Y                   | デバイスでコンピューティング ロールが設定されている場合に、PowerShell インターフェイスを介してコンピューティング ログを取得します。|
| Get-HcsApplianceInfo                          | Y           | Y                   | デバイスの情報 (ID、フレンドリ名、ソフトウェアのバージョン、システム状態など) を取得します。|
| Get-HcsApplianceSupportPackage                | Y           | Y                   | デバイスのサポート パッケージを収集します。     |
| Get-HcsArpResponse                            | Y           | Y                   |   |
| Get-HcsControllerSetupInformation             | Y           | Y                   | Microsoft.Hcs.Setup.ControllerInfo オブジェクトを取得します。 |
| Get-HcsDataBoxAccount                         | Y           | Y                   ||
| Get-HcsExternalVirtualSwitch                  | Y           | Y                   | Kubernetes の構成で使用すべきスイッチを取得します。   |
| Get-HcsGpuNvidiaSmi                           | Y           | Y                   | デバイスの GPU ドライバー情報を取得します。  |
| Get-HcsIPAddress                              | Y           | Y                   | データストアまたはシステムからネットワーク アダプターの構成をフェッチします。   |
| Get-HcsIPAddressPool                          |             | Y                   | |
| Get-HcsKubeClusterInfo                        | Y           | Y                   | Kubernetes クラスターの構成情報を取得します。|
| Get-HcsKubeClusterNetworkInfo                 | Y           | Y                   | Kubernetes サービスおよびポッド サブネットを取得します。                                                                                                                                                   |
| Get-HcsKubernetesAzureMonitorConfiguration    | Y           | Y                   | デバイス上の Kubernetes クラスターで実行されている Azure Monitor に関する情報を取得します。                                                                                                        |
| Get-HcsKubernetesContainerRegistryInfo        | Y           | Y                   | デバイス上のエッジ コンテナー レジストリの詳細を取得します。                                                                                                                               |
| Get-HcsKubernetesDashboardToken               | Y           | Y                   | ダッシュボードを表示するための Kubernetes ダッシュボード トークンを取得します (ローカル UI 経由でも同様の操作ができます)。                                                                                               |
| Get-HcsKubernetesNamespaces                   | Y           | Y                   | ユーザーによって構成された Kubernetes 名前空間を取得します。  |
| Get-HcsKubernetesUserConfig                   | Y           | Y                   | ユーザーによって構成された特定の名前空間に対応する `kubeconfig` を取得します。  |
| Get-HcsLocalWebUICertificateHash              | Y           | Y                   | 構成されたローカル Web UI 証明書のサムプリントを取得します。 |
| Get-HcsMacAddressPool                         |             | Y                   | Kubernetes VM Mac アドレス プールを取得します。     |
| Get-HcsNetBmcInterface                        | Y           | Y                   | ベースボード管理コントローラー (BMC) のネットワーク構成プロパティ (IPv4 アドレス、IPv4 ゲートウェイ、IPv4 サブネット マスク、デバイスで DHCP が有効になっているかどうかなど) を取得します。 |
| Get-HcsNetInterface                           | Y           | Y                   | デバイスの望ましいネットワーク構成をフェッチします。    |
| Get-HcsNetRoute                               | Y           | Y                   | デバイスに追加したすべてのカスタム ルート構成を確認します。 これらのルートには、すべてのシステム ルート、およびデバイス上に既に存在する既定のルートは含まれません。            |
| Get-HcsNodeSecureEraseLogs                    | Y           | Y                   | 削除予定、Ernie に確認 - 以前行われた工場出荷時の既定値へのリセット時に、デバイス内のドライブが安全に消去されたことを確認するログを取得します。     |
| Get-HcsNodeSupportPackage                     | Y           | Y                   | デバイスからログを収集し、指定されたネットワークまたはローカル共有にサポート パッケージの形でログをコピーします。   |
| Get-HcsRemoteSupportConsent                   | Y           | Y                   | デバイスでのリモート サポートを有効にするために、お客様から同意を取得します。   |
| Get-HcsRestEndPoint                           | Y           | Y                   |  |
| Get-HcsSetupDesiredStateResult                | Y           | Y                   | デバイスに対する一部の Desired State Configuration (DSC) 実行の DSC 結果オブジェクトを取得します。 |
| Get-HcsSmbConfiguration                       | Y           | Y                   | <!-- to be removed--> |
| Get-HcsSupportedVpnRegions                    | Y           | Y                   |   |
| Get-HcsSupportPackageUploadJob                | Y           | Y                   | 実行中のサポート パッケージ アップロード ジョブの状態を取得します。  |
| Get-HcsUpdateConfiguration                    | Y           | Y                   | Azure Stack Edge デバイスに設定された更新サーバー設定 (サーバーの種類、サーバーへの URI パスなど) を取得します。  |
| Get-HcsUpdateJob                              | Y           | Y                   | デバイスで実行されているすべての更新ジョブ、またはジョブ ID が渡された場合には特定の更新ジョブの状態をフェッチします。 |
| Get-HcsVirtualNetwork                         |             | Y                   | デバイス上で作成されたスイッチに関連付けられている仮想ネットワークとサブネットを識別します。   |
| Get-HcsVirtualSwitch                          | Y           | Y                   | 指定したネットワーク インターフェイスに関連付けられている仮想スイッチを取得します。  |
| Get-VMInGuestLogs                             |             | Y                   | デバイス上の障害が発生した VM のゲスト内ログを収集します。  |
| Invoke-AzureDataBoxEdgeRoleReconcile          |             | Y                   | Kubernetes クラスター構成を最新の状態にする場合に使用します。 |
| Invoke-AzureDataBoxEdgeRoleReconfigure        |             | Y                   | Kubernetes クラスターの構成を変更するために使用されます。  |
| Remove-HcsIPAddressPool                       |             |                     | Kubernetes VM Mac アドレス プールを削除します。 |
| Remove-HcsKubeClusterNetworkInfo              |             | Y                   | Kubernetes のサービス サブネットまたはポッドを変更します。|
| Remove-HcsKubernetesAzureArcAgent             |             | Y                   | デバイス上の Kubernetes クラスターから Azure Arc エージェントを削除します。  |
| Remove-HcsKubernetesAzureMonitorConfiguration |             | Y                   | デバイス上の Kubernetes クラスターから Azure Monitor を削除し、Kubernetes クラスターからコンテナー ログとプロセッサ メトリックを収集できるようにします。  |
| Remove-HcsKubernetesContainerRegistry         |             | Y                   | デバイスからエッジ コンテナー レジストリを削除します。   |
| Remove-HcsKubernetesNamespace                 |             | Y                   | 指定した Kubernetes 名前空間を削除します。 |
| Remove-HcsMacAddressPool                      |             | Y                   | Kubernetes VM Mac アドレス プールを削除します。|
| Remove-HcsNetRoute                            |             | Y                   | ユーザーがデバイスに追加したルート構成を削除します。 |
| Remove-HcsVirtualNetwork                      |             | Y                   | デバイス上で作成されたスイッチに関連付けられている仮想ネットワークとサブネットを削除します。 |
| Remove-HcsVirtualSwitch                       |             | Y                   | デバイス上のポートに関連付けられている仮想スイッチを削除します。  |
| Restart-HcsNode                               |             | Y                   | デバイス上のノードを再起動します。 シングル ノード デバイスの場合、この操作によりデバイスが再起動され、ダウンタイムが発生します。 |
| Set-AzureDataBoxEdgeRoleCompute               |             | Y                   | Azure portal を通じてコンピューティングを構成し、Kubernetes クラスターを作成して設定するために使用されます。   |
| Set-HcsCertificate                            |             | Y                   | 独自の証明書を持ち込めるようにします。 |
| Set-HcsClusterLevelSecurity                   |             | Y                   | クラスター トラフィックのセキュリティ レベルを設定します。 ノード間のクラスター トラフィックと、クラスターの共有ボリューム (CSV) からのトラフィックが含まれます。    |
| Set-HcsClusterWitness                         |             | Y                   | Windows クラスター監視を作成または構成します。 クラスター監視は、2 ノード デバイスのうちの 1 つのノードがダウンした場合にクォーラムを確立するのに役立ちます。     |
| Set-HcsEastWestCsvEncryption                  |             | Y                   | <!--can be removed, confirmed with Vibha/Deepan. Talk to Phani. -->  |
| Set-HcsExternalVirtualSwitch                  |             | Y                   | ユーザーがデバイス上でコンピューティングを有効にしたポートで外部仮想スイッチを構成します。  |
| Set-HcsIPAddress                              |             | Y                   | ネットワーク インターフェイスのプロパティを更新します。                 |
| Set-HcsIPAddressPool                          |             |                     | Kubernetes VM Mac アドレス プールを設定します。       |
| Set-HcsKubeClusterNetworkInfo                 |             | Y                   | デバイス上の Azure portal からコンピューティングを構成する前に、Kubernetes ポッドとサービス サブネットを変更します。                                                                                  |
| Set-HcsKubernetesAzureArcAgent                |             | Y                   | デバイス上に Arc エージェントをインストールして、Kubernetes クラスターで Azure Arc を有効にします。 |
| Set-HcsKubernetesAzureArcDataController       |             | Y                   | デバイス上の Kubernetes クラスターにデータ コントローラーを作成します。 |
| Set-HcsKubernetesAzureMonitorConfiguration    |             | Y                   | デバイス上の Kubernetes クラスターで Azure Monitor を有効にし、Kubernetes クラスターからコンテナー ログとプロセッサ メトリックを収集できるようにします。|
| Set-HcsKubernetesContainerRegistry            |             | Y                   | デバイスのアドオンとしてエッジ コンテナー レジストリを有効にします。                                                                                                                                  |
| Set-HcsMacAddressPool                         |             | Y                   | Kubernetes VM Mac アドレス プールを設定します。 |
| Set-HcsNetBmcInterface                        |             | Y                   | BMC の DHCP 構成を有効または無効にします。 |
| Set-HcsNetInterface                           |             | Y                   | Azure Stack Edge デバイス上のネットワーク インターフェイスの IP アドレス、サブネット マスク、ゲートウェイを構成します。|
| Set-HcsSmbServerEncryptionConfiguration       |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsSmbSigningConfiguration                |             | Y                   | <!--to be removed, Ernie confirmed--> |
| Set-HcsUpdateConfiguration                    |             | Y                   | デバイスの更新サーバー設定 (サーバーの種類とサーバーへの URI パスを含む) を設定します。 更新プログラムは、Microsoft Update サーバーから、または Windows Server Update Services (WSUS) サーバーからインストールできます。   |
| Set-HcsVirtualNetwork                         |             | Y                   |デバイス上で作成されたスイッチに関連付けられている仮想ネットワークとサブネットを作成します。 |
| Set-HcsVpnS2SInterface                        |             | Y                   | <!--VPN should be P2S-->  |
| Start-HcsGpuMPS                               |             | Y                   | デバイスでマルチプロセッサ サービス (MPS) を有効にします。|
| Start-HcsSupportPackageUploadJob              | Y           | Y                   | すべてのログを含むサポート パッケージを収集してパッケージをアップロードすることで、Microsoft がそれをデバイスの問題のデバッグに使用できるようにします。|
| Start-HcsUpdateJob                            |             | Y                   | 更新ジョブを開始します。|
| Stop-HcsGpuMPS                                |             | Y                   | デバイスでマルチプロセッサ サービス (MPS) を無効にします。 |
| Test-HcsKubernetesStatus                      | Y           | Y                   | Kubernetes 診断コンテナーを実行します。 |

#### <a name="generic-network-cmdlets"></a>汎用ネットワーク コマンドレット

| コマンドレット   | 診断 | 診断と修復 | 説明                                                                                                                            |
|---------------------------|-------------|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Find-NetRoute             | Y           | Y                    | リモート アドレスに到達するのに最適なローカル IP アドレスと最適なルートを検索します。                                                          |
| Get-NetAdapter            | Y           | Y                    | 基本的なネットワーク アダプターのプロパティを取得します。                                                                                             |
| Get-NetIPAddress          | Y           | Y                    | コンピューターの IP アドレス構成全体を取得します。                                                                             |
| Get-NetNat                | Y           | Y                    | コンピューターで構成されているネットワーク アドレス変換 (NAT) オブジェクトを取得します。                                                               |
| Get-NetNatExternalAddress | Y           | Y                    | ネットワーク アドレス変換 (NAT) インスタンスで構成された外部アドレスの一覧を取得します。                                          |
| Get-NetRoute              | Y           | Y                    | IP ルーティング テーブルから IP ルート情報 (宛先ネットワーク プレフィックス、ネクスト ホップ IP アドレス、ルート メトリックなど) を取得します。 |
| Get-NetCompartment        | Y           | Y                    | プロトコル スタックのネットワーク コンパートメントを取得します                                                                                        |
| Get-NetNeighbor           | Y           | Y                    | 近隣キャッシュ エントリを取得します。                                                                                                           |
| Get-NetAdapterSriov       | Y           | Y                    | ネットワーク アダプターの SR-IOV プロパティを取得します。                                                                                     |
| Resolve-DnsName           |             | Y                    | 指定した名前に対して DNS 名のクエリ解決を実行します。                                                                           |


####  <a name="multi-access-edge-computing-mec-cmdlets"></a>マルチアクセス エッジ コンピューティング (MEC) コマンドレット

| コマンドレット | 診断 | 診断と修復 | 説明                                                                                                       |
|-------------------------------------------|-------------|----------------------|-------------------------------------------------------------------------------------------------------------------|
| Get-MecVnf                                | Y           | Y                    | Azure Stack Edge 上にデプロイされている、すべてのマルチアクセス エッジ コンピューティング仮想ネットワーク機能の仮想マシンの一覧を取得します。       |
| Get-MecVirtualMachine                     | Y           | Y                    | マルチアクセス エッジ コンピューティング仮想ネットワーク機能によって作成された仮想マシンの一覧を取得します。 関数。                    |
| Get-MecServiceConfig                      | Y           | Y                    | 仮想ネットワーク機能に影響を与えるマルチアクセス エッジ コンピューティング サービスの設定を取得します。 ライフサイクル ワークフロー。 |
| Get-MecInformation                        | Y           | Y                    | マルチアクセス エッジ コンピューティングの情報を取得します。 たとえば、Azure Stack Edge デバイスが Azure Network Function Manager に登録されているかどうかなどです。

####  <a name="general-purpose-os-cmdlets"></a>汎用 OS コマンドレット

| コマンドレット | 診断 | 診断と修復 | 説明                                                                                                                  |
|----------------------------|-------------|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Select-String              | Y           | Y                    | 文字列とファイル内のテキストを検索します。                                                                                             |
| Write-Progress             | Y           | Y                    | PowerShell コマンド ウィンドウに進行状況バーを表示します。                                                                  |
| Get-Command                | Y           | Y                    | セッションで実行できるコマンドの一覧を取得します。                                                                        |
| Measure-Object             | Y           | Y                    | オブジェクトの数値プロパティと、テキストのファイルなど文字列オブジェクト内の文字、単語、および行を計算します。 |
| Select-Object              | Y           | Y                    | オブジェクトまたはオブジェクトのプロパティを選択します。                                                                                        |
| Out-Default                |             | Y                    | 既定のフォーマッタおよび既定の出力コマンドレットに出力を送信します。                                                  |
| Get-WinEvent               | Y           | Y                    | イベント ログとイベント トレース ログ ファイルからイベントを取得します。                                                                     |
| Get-Counter                | Y           | Y                    | パフォーマンス カウンター データを取得します。                                                                                               |
| Get-Volume                 | Y           | Y                    | 指定した Volume オブジェクトを取得します。フィルターが設定されていない場合はすべての Volume オブジェクトを取得します。                                            |
| Get-Service                | Y           | Y                    | サービスを表すオブジェクトを取得します。                                                                                    |

#### <a name="cluster-cmdlets"></a>クラスター コマンドレット 

| コマンドレット       | 診断 | 診断と修復 | 説明                                                                            |
|-----------------------|-------------|----------------------|----------------------------------------------------------------------------------------|
| Get-ClusterResource   | Y           | Y                    | フェールオーバー クラスターの 1 つ以上のリソースに関する情報を取得します。                    |
| Get-Cluster           | Y           | Y                    | フェールオーバー クラスターに関する情報を取得します。                                               |
| Get-ClusterNode       | Y           | Y                    | フェールオーバー クラスターの 1 つ以上のノード (またはサーバー) に関する情報を取得します。           |
| Start-Cluster         |             | Y                    | まだクラスター サービスが開始されていないクラスターのすべてのノードで、クラスター サービスを開始します。 |
| Start-ClusterResource |             | Y                    | フェールオーバー クラスター内のリソースをオンラインにします。                                        |
| Stop-ClusterResource  |             | Y                    | フェールオーバー クラスター内のリソースをオフラインにします。                                        |


#### <a name="hyper-v-cmdlets"></a>Hyper-V コマンドレット

| コマンドレット      | 診断 | 診断と修復 | 説明                                                                                                                                             |
|----------------------|-------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| Get-Vm               | Y           | Y                    | コンピューター上の仮想マシンを取得します。                                                                                                              |
| Get-VMNetworkAdapter | Y           | Y                    | 仮想マシン、スナップショット、管理 OS、または仮想マシンと管理 OS の仮想ネットワーク アダプターを取得します。 |
| Get-VMHardDiskDrive  | Y           | Y                    | 1 つ以上の仮想マシンにアタッチされている仮想ハード ディスク ドライブを取得します。                                                                             |
| Get-VMSwitch         | Y           | Y                    |  仮想スイッチの一覧を取得します。                                                                                                                     |

## <a name="next-steps"></a>次の手順

[Microsoft サポートにお問い合わせください](azure-stack-edge-contact-microsoft-support.md)。
