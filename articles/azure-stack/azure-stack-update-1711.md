---
title: Azure Stack 1711 更新プログラム | Microsoft Docs
description: Azure Stack 統合システムの 1711 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: f067656f510c14c4a2ac02d3629ae71432c342d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1711-update"></a>Azure Stack 1711 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する既知の問題と、ビルド (インストール後) に関する既知の問題に分けられます。

> [!IMPORTANT]
> この更新プログラム パッケージを適用できるのは Azure Stack 統合システムのみです。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1711 更新プログラムのビルド番号は **171201.3** です。

## <a name="before-you-begin"></a>開始する前に

### <a name="prerequisites"></a>前提条件

- この更新プログラムを適用する前に、Azure Stack [1710 更新プログラム](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710)をインストールする必要があります。

- 更新プログラム 1711 をインストールする前に、アカウント名 **CloudAdmin** の使用状況を確認します。 バージョン 1711 以降、*CloudAdmin* は予約済みアカウント名となっているため、手動でこのアカウント名を指定してはいけません。 バージョン 1711 に更新すると、更新プログラムにより、展開アカウントの既存のインスタンス (通常の名前は AzureStackAdmin) が削除されます。 展開アカウントの名前を *CloudAdmin* にした場合、1711 に更新すると削除されます。 

  *CloudAdmin* は、[*特権エンドポイント*](azure-stack-privileged-endpoint.md) (PEP) に接続するためのビルトイン アカウントです。 CloudAdmin グループのメンバーである別のユーザー アカウントが既にある場合を除き、このアカウントが削除されると、PEP のロックアウトになる可能性があります。 

  CloudAdmin を展開アカウントの名前として使用していた場合は、1711 への更新を開始する前に、使用している PEP に新しい CloudAdmin ユーザーを追加し、Azure Stack からロックアウトされないようにします。 新しい CloudAdmin ユーザーを追加するには、PEP に対して **New-CloudAdminUser** コマンドレットを実行します。

### <a name="new-features-and-fixes"></a>新機能と修正

この更新プログラムには、Azure Stack に対する次の機能強化と修正が含まれています。

#### <a name="new-features"></a>新機能

- 配信ソリューション テンプレートのサポート
- Azure Stack Graph のログとエラー処理
- ホストのオンとオフの切り替え
- ユーザーによる Windows VM の自動アクティブ化
- 保有目的で BitLocker 回復キーを取得する特権エンドポイント PowerShell コマンドレットの追加
- インフラストラクチャ更新時のオフライン イメージ更新のサポート
- バックアップ サービスの有効化によるインフラストラクチャ バックアップの有効化

#### <a name="fixes"></a>修正

- フィールド交換ユニット (FRU) での DNS の競合状態の修正、およびクラスター ログの更新
- フィールド交換ユニット (FRU) での無効ホストの再起動機能の修正
- その他のパフォーマンス、セキュリティ、および安定性に関するさまざまな修正

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 の新機能と修正

- [2017 年 11 月 14 日—KB4048953 (OS ビルド 14393.1884) ](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

このセクションには、1711 更新プログラムのインストール中に発生する可能性のある既知の問題が含まれています。


1. **現象:** 更新プロセスで Azure Stack オペレーターに次のエラーが表示される場合があります。*"name:Install Update.", "description": "Install Update on Hosts and Infra VMs.", "errorMessage": "Type 'LiveUpdate' of Role 'VirtualMachines' raised an exception:\n\nThere is not enough space on the disk.\n\nat <ScriptBlock>, <No file>: line22", "status": "Error", "startTimeUtc": "2017-11-10T16:46:59.123Z", "endTimeUtc": "2017-11-10T19:20:29.669Z", "steps": [ ]"*
    2. **原因:** この問題は、Azure Stack インフラストラクチャに含まれる 1 つ以上の仮想マシンの空きディスク領域が不足しているために発生します。
    3. **解決方法:** マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。
<br><br>
2. **現象:** 更新プロセスで Azure Stack オペレーターに次のエラーが表示される場合があります。*Exception calling "ExtractToFile" with "3" argument(s):"The process cannot access the file '<\\<machineName>-ERCS01\C$\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll>'*
    1. **原因:** この問題は、以前に特権エンドポイント (PEP) を使用して再開されたポータルから更新を再開するときに発生します。
    2. **解決方法:** マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。
<br><br>
3. **現象:** 更新プロセス中に Azure Stack オペレーターに次のエラーが表示される場合があります。*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for <machineName>-ACS01 produced the following errors.\nThere was an error getting VM information from hosts.Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request.The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c".*
    1. **原因:** この問題は、Windows Server の問題によって引き起こされています。これは今後の Window Server 更新プログラムで修正される予定です。
    2. **解決方法:** マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。
<br><br>
4. **現象:** 更新プロセスで Azure Stack オペレーターに次のエラーが表示される場合があります。*"Type 'DefenderUpdate' of Role 'URP' raised an exception: Failed getting version from \\SU1FileServer\SU1_Public\DefenderUpdates\x64\{file name}.exe after 60 attempts at Copy-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: line 262"*
    1. **原因:** この問題は、Windows Defender 定義ファイルの更新をバックグラウンドでダウンロードしようとして失敗したか完了していないために発生します。
    2. **解決方法:** 最初に更新を試行してから最大 8 時間後に更新の再開を試行してください。

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このセクションには、ビルド **20171201.3** のインストール後の既知の問題が含まれています。

#### <a name="portal"></a>ポータル

- 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 これは、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたことを示します。 問題が発生した場合は、マイクロソフトの CSS に問い合わせてサポートを依頼してください。
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。
- リソース グループのプロパティを表示する際に、**[移動]** ボタンが無効になっています。 これは正しい動作です。 サブスクリプション間のリソース グループの移動は現在サポートされていません。
- ドロップダウン リストからサブスクリプション、リソース グループ、または場所を選択するワークフローで、次の問題のいずれかが発生する場合があります。

   - 一覧の最上部に空の行が表示されることがある。 アイテムは問題なく選択できます。
   - ドロップダウン リストの項目の一覧が短い場合、アイテム名を表示できないことがある。
   - ユーザー サブスクリプションが複数ある場合、リソース グループのドロップダウン リストが空になることがある。

        > [!NOTE]
        > 最後の 2 つの問題を回避するには、サブスクリプションまたはリソース グループの名前を入力するか (把握している場合)、代わりに PowerShell を使用します。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 回避策として、PowerShell を使用してアクセス許可を確認できます。
- **[サービス正常性]** ブレードの読み込みに失敗します。 管理ポータルまたはユーザー ポータルで [サービス正常性] ブレードを開くと、Azure Stack にエラーが表示され、情報は読み込まれません。 これは正しい動作です。 [サービス正常性] を選択して開けますが、この機能はまだ利用できません。Azure Stack の今後のバージョンに実装されます。

#### <a name="health-and-monitoring"></a>正常性と監視

- インフラストラクチャのロール インスタンスを再起動すると、再起動が失敗したことを示すメッセージを受け取る場合があります。 ただし、再起動は実際には成功しています。

#### <a name="marketplace"></a>マーケットプレース
- **[Add from Azure]\(Azure から追加\)** を使用してアイテムを Azure Stack マーケットプレースに追加しようとすると、すべてのアイテムがダウンロード対象として表示されない場合があります。
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。

#### <a name="compute"></a>コンピューティング
- ユーザーは geo 冗長ストレージを持つ仮想マシンを作成するオプションを利用できます。 この構成の場合、仮想マシンの作成が失敗します。
- 仮想マシン可用性セットは、1 つの障害ドメインと 1 つの更新ドメインでのみ構成可能です。
- 仮想マシン スケール セットを作成するマーケットプレースのエクスペリエンスはありません。 スケール セットはテンプレートを使用して作成できます。
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

#### <a name="networking"></a>ネットワーク
- ポータルを使用して、パブリック IP アドレスを持つロード バランサーを作成することはできません。 この問題を回避するには、PowerShell を使用してロード バランサーを作成します。
- ネットワーク ロード バランサーの作成時に、ネットワーク アドレス変換 (NAT) 規則を作成する必要があります。 作成しない場合、ロード バランサーを作成した後に NAT 規則を追加しようとするときにエラーが表示されます。
- 仮想マシン (VM) を作成して IP アドレスに関連付けた後で、VM からパブリック IP アドレスの関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。 この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。 現時点では、新しい VM の作成には新しいパブリック IP アドレスのみを使用する必要があります。
- Azure Stack オペレーターが、VNET または ネットワーク セキュリティ グループのデプロイ、削除、変更を行えない場合があります。 この問題は、主に同じパッケージの更新を続けて試行する際に発生します。 これは、更新プログラムのパッケージ化の問題によって引き起こされますが、問題については現在調査中です。
- 内部負荷分散 (ILB) は、バックエンド VM の MAC アドレスを正しく処理せず、Linux インスタンスを中断します。

#### <a name="sqlmysql"></a>SQL/MySQL
- テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。
- リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。

#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

#### <a name="identity"></a>ID

Azure Active Directory フェデレーション サービス (AD FS) が配置された環境では、**azurestack\azurestackadmin** アカウントは既定プロバイダー サブスクリプションの所有者ではなくなりました。 **Admin ポータル/adminmanagement エンドポイント**に **azurestack\azurestackadmin** でログインする代わりに **azurestack\cloudadmin** アカウントを使用できます。こうすることで、既定プロバイダー サブスクリプションを管理および使用できます。

> [!IMPORTANT]
> ADFS が配置された環境で **azurestack\cloudadmin** アカウントが既定プロバイダー サブスクリプションの所有者であっても、ホストに対する RDP の権限はありません。 ホストのログイン、アクセス、および管理については、必要に応じて **azurestack\azurestackadmin** アカウントまたはローカル管理者アカウントを引き続き使用します。

#### <a name="infrastructure-backup-sevice"></a>インフラストラクチャ バックアップ サービス
<!-- 1974890-->

- **1711 よりも前のバックアップはクラウドの回復ではサポートされません。**  
  1711 よりも前のバックアップはクラウドの回復と互換性がありません。 まず 1711 に更新してからバックアップを有効化する必要があります。 既にバックアップを有効化している場合は、1711 に更新した後でバックアップを取得する必要があります。 1711 よりも前のバックアップは削除する必要があります。

- **ASDK でのインフラストラクチャ バックアップの有効化はテスト専用です。**  
  インフラストラクチャ バックアップを使用してマルチノード ソリューションを復元できます。 ASDK でインフラストラクチャ バックアップを有効化できますが、回復をテストする方法はありません。

詳しくは、「[インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復](azure-stack-backup-infrastructure-backup.md)」をご覧ください。

## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1711 更新プログラム パッケージは[ここ](https://aka.ms/azurestackupdatedownload)でダウンロードできます。

## <a name="more-information"></a>詳細情報

更新プログラム 1711 でインストールされる特権エンドポイント (PEP) を使用すると、更新プログラムのモニターや再開を行う手段が提供されます。

- 「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)」をご覧ください。 

## <a name="see-also"></a>関連項目

- Azure Stack での更新プログラムの管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」をご覧ください。
- Azure Stack に更新プログラムを適用する方法について詳しくは、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」をご覧ください。
