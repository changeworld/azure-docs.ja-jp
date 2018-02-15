---
title: "Azure Stack 1710 更新プログラム (ビルド 20171020.1) | Microsoft Docs"
description: "Azure Stack 統合システムの 1710 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 1a482f1d2f3eef8775bb7b64d4f6749f69fa5471
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 更新プログラム (ビルド 20171020.1)

*適用対象: Azure Stack 統合システム*

この記事では、この更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する既知の問題と、ビルド (インストール後) に関する既知の問題に分けられます。

> [!IMPORTANT]
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="improvements-and-fixes"></a>機能強化と修正

この更新プログラムには、Azure Stack に対する次の機能強化と修正が含まれています。
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 の機能強化と修正

- Windows Server 2016 の更新プログラム: 2017 年 10 月 10 日 - KB4041691 (OS ビルド 14393.1770)。 詳しくは、[https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691) をご覧ください。

### <a name="additional-quality-improvements-and-fixes"></a>追加の機能強化と修正

- ネットワーク タイム プロトコル (NTP) サーバーのトラブルシューティングと更新に役立つ特権エンドポイントの PowerShell コマンドレットを追加しました。
- 特権エンドポイント Just Enough Administration (JEA) エンドポイント モジュールとコマンドレットのホワイトリストを更新するサポートを追加しました。 
- 特権エンドポイントのローカル言語のエラーを修正しました。
- ゲートウェイの資格情報をローテーションする機能を追加しました。
- CBLocalAdmin ローカル Administrator アカウントを削除しました。 
- 更新後もハートビート アラートが正しく機能するように、ハートビート アラートのテンプレート コンテンツを修正しました。
- FRU 操作中のタイムアウトに対応するようにファブリックのリソース プロバイダーを修正しました。 
- クラウド開発者が Azure Stack の Azure Resource Manager API プロファイルを使用する機能を追加しました。
- デプロイメント仮想マシン (DVM) 上の Windows Update サービスを無効にしました。 
- ユーザー インターフェイスからノードの電源のオン/オフ アクションを削除しました。
- その他各種パフォーマンスおよび安定性に関する修正を加えました。 
 
## <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

このセクションには、1710 更新プログラムのインストール中に発生する可能性のある既知の問題が含まれています。

> [!IMPORTANT]
> 更新プログラムが失敗した場合、後で再開するときは特権エンドポイントから `Resume-AzureStackUpdate` コマンドレットを使用する必要があります。 管理者ポータルを使用して更新プログラムを再開しないでください。 (これは、このリリースの既知の問題です)。詳細については、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」を参照してください。

| 症状  | 原因  | 解決策 |
|---------|---------|---------|
|更新プログラムを実行するときに、<br>更新プログラム アクション プランの「Storage Hosts Restart Storage Node (ストレージ ホストでの記憶域ノードの再起動)」の手順の実行中に<br> 次のようなエラーが発生する可能性があります。<br><br>**{"name":"Restart Storage Hosts","description":"Restart<br> Storage Hosts.","errorMessage":"Type 'Restart' of Role<br> 'BareMetal' raised an exception:\n\n The computer<br> HostName-05 is skipped.Fail to retrieve its LastBootUpTime<br> via the WMI service with the following error message:<br> The RPC server is unavailable.<br>(Exception from HRESULT: 0x800706BA).\nat Restart-Host** | この問題は、一部の構成に潜在的な障害が存在するドライバーによって引き起こされます。 | 1.ベースボード管理コントローラー (BMC) Web インターフェイスにサインインし、エラー メッセージで特定されたホストを再起動します。<br><br>2.特権エンドポイントを使用して、更新プログラムを再開します。 |
| 更新プログラムを実行するときに、<br> 更新プログラム アクション プランの手順「Step: Running step 2.4 - Install update (手順: 手順 2.4 を実行する - 更新プログラムのインストール)」の後に<br> 更新プロセスが停止して先に進まないように見えます。<br><br>この手順の後は、.nupkg ファイルの内部インフラストラクチャの<br> ファイル共有への一連のコピー プロセスが続きます。 例: <br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>または、次のメッセージが表示されます。<br><br>**WarningMessage:Task: Invocation of interface 'LiveUpdate'<br> of role 'Cloud\Fabric\VirtualMachines' failed:<br> Type 'LiveUpdate' of Role 'VirtualMachines' raised an<br> exception: There is not enough space on the disk.**  | この問題は、ログ ファイルによりインフラストラクチャの仮想マシンのディスクがいっぱいになることと、Windows Server スケールアウト ファイル サーバー (SOFS) の問題が原因で発生し、次回の更新プログラムで修正されます。 | マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。 | 
| 更新プログラムを実行するときに、<br> 更新プログラム アクション プランの「Step: Running step 2.13.2 - Update<br> *VM_Name* (手順: 手順 2.13.2 を実行する - VM_Name の更新)」の手順の実行中に次のようなエラーが発生することがあります。 (仮想マシン名は<br> 変わることがあります。)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: Invocation of interface 'LiveUpdate' of<br> role 'Cloud\Fabric\WAS'failed: Type 'LiveUpdate' of Role<br> 'WAS' raised an exception: ERROR during storage<br> initialization: An error occurred while trying to make an API<br> call to Microsoft Storage service: {"Message": "A timeout<br> occurred while communicating with Service Fabric.<br>Exception Type: TimeoutException.<br>Exception message: Operation timed out."}**  | この問題は、Windows Server の I/O のタイムアウトが原因で発生し、次回の更新プログラムで修正されます。 | マイクロソフトの CSS に問い合わせてサポートを依頼してください。
| 更新プログラムを実行するときに、<br> 手順「Step 21 Restart SQL server VMs (手順 21 SQL Server VM を再起動する)」の実行中に次のようなエラーが発生することがあります。<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an<br> exception: VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM<br> VerboseMessage:[VirtualMachines:LiveUpdateRestart] at<br>MS.Internal.ServerClusters.ExceptionHelp.Build at<br>MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(Boolean force) at Microsoft.FailoverClusters.PowerShell.<br>StopClusterResourceCommand.BeginTimedOperation() at <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() at  Microsoft.FailoverClusters.PowerShell.<br>FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM Warning<br>Message: Task: Invocation of interface 'LiveUpdateRestart' of<br> role 'Cloud\Fabric\VirtualMachines' failed:** | この問題は、仮想マシンを再起動できなかった場合に発生することがあります。 | マイクロソフトの CSS に問い合わせてサポートを依頼してください。
| 更新プログラムを実行するときに、次のようなエラーが発生する場合があります。<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL'<br> raised an exception: An error occurred pausing node<br> 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 at<br> Shutdown,C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>Applications \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: line 50 at <ScriptBlock&#62;,<br> <No file>: line 18 at <ScriptBlock&#62;, <No file&#62;: line 16** | この問題は、ロールを排出するために仮想マシンを中断状態にできない場合に発生することがあります。 | マイクロソフトの CSS に問い合わせてサポートを依頼してください。
| 更新プログラムを実行するときに、次のいずれかのエラーが発生する場合があります。<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error checking ADFS<br> probe endpoint *endpoint_URI*: Exception calling<br> "GetResponse" with "0" argument(s): "The remote server<br> returned an error: (503) Server Unavailable." at Invoke-<br>ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error fetching<br> ADFS properties: Could not connect to <br>net.tcp://localhost:1500/policy.The connection attempt lasted<br> for a time span of 00:00:02.0498923.TCP error code<br> 10061: No connection could be made because the target<br> machine actively refused it 127.0.0.1:1500.<br> at Invoke-ADFSGraphValidation** | 更新プログラム アクション プランでは Active Directory フェデレーション サービス (AD FS) の正常性を検証できません。 | マイクロソフトの CSS に問い合わせてサポートを依頼してください。

## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このセクションには、ビルド 20171020.1 のインストール後の既知の問題が含まれています。

### <a name="portal"></a>ポータル

- 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 これは、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたことを示します。 問題が発生した場合は、マイクロソフトの CSS に問い合わせてサポートを依頼してください。
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。
- リソース グループのプロパティを表示する際に、**[移動]** ボタンが無効になっています。 これは正しい動作です。 サブスクリプション間のリソース グループの移動は現在サポートされていません。
- ドロップダウン リストからサブスクリプション、リソース グループ、または場所を選択するワークフローで、次の問題のいずれかが発生する場合があります。

   - 一覧の最上部に空の行が表示されることがある。 アイテムは問題なく選択できます。
   - ドロップダウン リストの項目の一覧が短い場合、アイテム名を表示できないことがある。
   - ユーザー サブスクリプションが複数ある場合、リソース グループのドロップダウン リストが空になることがある。 

   最後の 2 つの問題を回避するには、サブスクリプションまたはリソース グループの名前を入力するか (把握している場合)、代わりに PowerShell を使用します。
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 回避策として、PowerShell を使用してアクセス許可を確認できます。
  
### <a name="backup"></a>Backup

- **[Infrastructure backup]\(インフラストラクチャのバックアップ\)** ブレードでインフラストラクチャのバックアップは有効にしないでください。

### <a name="health-and-monitoring"></a>正常性と監視

- インフラストラクチャのロール インスタンスを再起動すると、再起動が失敗したことを示すメッセージを受け取る場合があります。 ただし、再起動は実際には成功しています。

### <a name="marketplace"></a>マーケットプレース
- **[Add from Azure]\(Azure から追加\)** を使用してアイテムを Azure Stack マーケットプレースに追加しようとすると、すべてのアイテムがダウンロード対象として表示されない場合があります。
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。

### <a name="compute"></a>計算
- ユーザーは geo 冗長ストレージを持つ仮想マシンを作成するオプションを利用できます。 この構成の場合、仮想マシンの作成が失敗します。
- 仮想マシン可用性セットは、1 つの障害ドメインと 1 つの更新ドメインでのみ構成可能です。
- 仮想マシン スケール セットを作成するマーケットプレースのエクスペリエンスはありません。 スケール セットはテンプレートを使用して作成できます。
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。
 
### <a name="networking"></a>ネットワーク
- ポータルを使用して、パブリック IP アドレスを持つロード バランサーを作成することはできません。 この問題を回避するには、PowerShell を使用してロード バランサーを作成します。
- ネットワーク ロード バランサーの作成時に、ネットワーク アドレス変換 (NAT) 規則を作成する必要があります。 作成しない場合、ロード バランサーを作成した後に NAT 規則を追加しようとするときにエラーが表示されます。
- 仮想マシン (VM) を作成して IP アドレスに関連付けた後で、VM からパブリック IP アドレスの関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。 この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。 現時点では、新しい VM の作成には新しいパブリック IP アドレスのみを使用する必要があります。
 
### <a name="sqlmysql"></a>SQL/MySQL
- テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。 
- リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。
 
### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。
 
### <a name="field-replaceable-unit-fru-procedures"></a>現場交換可能ユニット (FRU) のプロシージャ

- 更新プログラムの実行中、オフラインのイメージにパッチは適用されません。 スケール ユニット ノードを置換する必要がある場合、OEM ハードウェアのベンダーと協力して、置換されたノードのパッチ レベルが最新であることを確認します。

## <a name="download-the-update"></a>更新プログラムをダウンロードする

1710 更新プログラム パッケージは、[こちら](https://aka.ms/azurestackupdatedownload)からダウンロードできます。

## <a name="next-steps"></a>次の手順

- Azure Stack の更新プログラムの管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」をご覧ください。
- 更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」をご覧ください。
