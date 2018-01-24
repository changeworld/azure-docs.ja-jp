---
title: "Azure Stack 1712 更新プログラム | Microsoft Docs"
description: "Azure Stack 統合システムの 1712 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: 0456a202990d383370051d99112f829533b1b101
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="azure-stack-1712-update"></a>Azure Stack 1712 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する既知の問題と、ビルド (インストール後) に関する既知の問題に分けられます。

> [!IMPORTANT]
> この更新プログラム パッケージを適用できるのは Azure Stack 統合システムのみです。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス

Azure Stack 1712 更新プログラムのビルド番号は **180106.1** です。 顧客が以前に **180103.2** をデプロイしている場合、**180106.1** を適用する必要はありません。

## <a name="before-you-begin"></a>開始する前に

> [!IMPORTANT]
> 1712 更新プログラムのインストール プロセス中に仮想マシンを作成しようとしないでください。 詳細については、「[Azure Stack での更新プログラムの管理概要](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates)」を参照してください。

### <a name="prerequisites"></a>前提条件

この更新プログラムを適用する前に、まず Azure Stack [1711 更新プログラム](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711)をインストールする必要があります。

### <a name="post-update-steps"></a>更新後の手順

この更新プログラムでは、1712 Azure Stack 更新プログラムのインストールを完了した後、OEM パートナーからのファームウェアの更新プログラムをインストールすることも必要です。

> [!NOTE]
> それらの更新プログラムをダウンロードするには、OEM パートナーの Web サイトを参照してください。

### <a name="new-features-and-fixes"></a>新機能と修正

この更新プログラムには、Azure Stack に対する次の機能強化と修正が含まれています。

#### <a name="new-features"></a>新機能

- 特権エンドポイント経由で使用可能な Azure Stack クラウドを検証するための Test-AzureStack コマンドレット
- Azure Stack の切断されたデプロイを登録する機能
- 証明書とユーザー アカウントの有効期限に関する監視アラート
- BMC パスワードのローテーションのために PEP に追加された Set-BmcPassword コマンドレット
- オンデマンド ログ記録をサポートするためのネットワーク ログ記録の更新
- 仮想マシン スケール セット (VMSS) の再イメージ化操作のサポート
- CloudAdmin ログインのための ERCS VM でのキオスク モードの有効化
- テナントは Windows VM を自動的にアクティブ化可能

#### <a name="fixes"></a>修正

- 修復の実行中にメンテナンスでノード動作状態を表示するための修正
- パブリック IP 使用レコードの日時スタンプを修正するための修正
- その他のパフォーマンス、安定性、およびセキュリティに関するさまざまな修正
- TimeSource および Defender 特権エンドポイント モジュールのバグ修正

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 の新機能と修正

- [1 月 3 日 - 2018—KB4056890 (OS ビルド 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - この更新プログラムには、[MSRC セキュリティ勧告 ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) で説明されている業界全体のセキュリティの問題に関するソフトウェア修正プログラムが含まれています。

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

このセクションには、1712 更新プログラムのインストール中に発生する可能性のある既知の問題が含まれています。

1. **現象:** 更新プロセス中に Azure Stack オペレーターに次のエラーが表示される場合があります。*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for -ACS01 produced the following errors.\nThere was an error getting VM information from hosts.Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request.The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c."*
    1. **原因:** この問題は、Windows Server の問題によって引き起こされています。これは今後の Window Server 更新プログラムで修正される予定です。
    2. **解決方法:** マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。
<br><br>
2. **現象:** 更新プロセス中に Azure Stack オペレーターに次のエラーが表示される場合があります。*"Enabling the seed ring VM failed on node Host-Node03 with an error: [Host-Node03] Connecting to remote server Host-Node03 failed with the following error message : The WinRM client received an HTTP server error status (500), but the remote service did not include any other information about the cause of the failure."*
    1. **原因:** この問題は、Windows Server の問題によって引き起こされています。これは今後の Window Server 更新プログラムで修正される予定です。 
    2. **解決方法:** マイクロソフト カスタマー サービスおよびサポート (CSS) に問い合わせてサポートを依頼してください。
<br><br>

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

このセクションには、ビルド **180106.1** でのインストール後の既知の問題が含まれています。

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

#### <a name="health-and-monitoring"></a>正常性と監視

- インフラストラクチャのロール インスタンスを再起動すると、再起動が失敗したことを示すメッセージを受け取る場合があります。 ただし、再起動は実際には成功しています。

#### <a name="marketplace"></a>Marketplace
- 一部のマーケットプレース項目は、互換性の問題のために、このリリースで削除されています。 これらは、さらに検証された後、再度有効にされます。
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。

#### <a name="compute"></a>計算
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

    > [!NOTE]
    > Azure Stack 統合システムを 1712 バージョンに更新するときは、既存の SQL または MySQL リソース プロバイダー ユーザーに影響を与えないようにしてください。 新しい Azure Stack 更新プログラムが入手可能になるまでは、現在の SQL または MySQL リソース プロバイダー ビルドを引き続き使用できます。

#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

#### <a name="identity"></a>ID

Azure Active Directory フェデレーション サービス (AD FS) が配置された環境では、**azurestack\azurestackadmin** アカウントは既定プロバイダー サブスクリプションの所有者ではなくなりました。 **Admin ポータル/adminmanagement エンドポイント**に **azurestack\azurestackadmin** でログインする代わりに **azurestack\cloudadmin** アカウントを使用できます。こうすることで、既定プロバイダー サブスクリプションを管理および使用できます。

> [!IMPORTANT]
> ADFS が配置された環境で **azurestack\cloudadmin** アカウントが既定プロバイダー サブスクリプションの所有者であっても、ホストに対する RDP の権限はありません。 ホストのログイン、アクセス、および管理については、必要に応じて **azurestack\azurestackadmin** アカウントまたはローカル管理者アカウントを引き続き使用します。

## <a name="download-the-update"></a>更新プログラムをダウンロードする

Azure Stack 1712 更新プログラム パッケージは、[ここで](https://aka.ms/azurestackupdatedownload)ダウンロードできます。

## <a name="more-information"></a>詳細情報

Microsoft は、更新プログラム 1712 でインストールされる特権エンドポイント (PEP) を使用して、更新プログラムを監視および再開するための方法を提供しています。

- 「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)」をご覧ください。 

## <a name="see-also"></a>関連項目

- Azure Stack での更新プログラムの管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」をご覧ください。
- Azure Stack に更新プログラムを適用する方法について詳しくは、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」をご覧ください。
