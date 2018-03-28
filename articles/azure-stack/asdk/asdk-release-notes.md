---
title: Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs
description: Azure Stack Development Kit の機能強化、修正、既知の問題。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 176b850120958a5ca5fdaece4831e2ed27ac0a04
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack Development Kit のリリース ノート
以下のリリース ノートでは、Azure Stack Development Kit の機能強化、修正、既知の問題に関する情報を提供します。 実行しているバージョンが不明な場合は、[ポータルを使用して確認](.\.\azure-stack-updates.md#determine-the-current-version)できます。

> [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [フィード](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)をサブスクライブして、ASDK の新着情報を常に把握するようにしてください。

## <a name="build-201803021"></a>ビルド 20180302.1

### <a name="new-features-and-fixes"></a>新機能と修正
Azure Stack 統合システムの Azure Stack 1802 更新プログラム リリース ノートの「[新機能と修正](.\.\azure-stack-update-1802.md#new-features-and-fixes)」セクションを参照してください。

> [!IMPORTANT]    
> 「**新機能と修正**」セクションに記載されている項目の一部は、Azure Stack 統合システムにのみ関連します。


### <a name="known-issues"></a>既知の問題
 
#### <a name="portal"></a>ポータル
- 管理者ポータルの[ドロップダウン リストから新しいサポート要求を開く](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、次のリンクを使用します。     
    - Azure Stack Development Kit の場合は、https://aka.ms/azurestackforum を使います。    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- リソースまたはリソース グループのプロパティを表示する際に、**[移動]** が無効になっています。 これは正しい動作です。 リソース グループまたはサブスクリプション間のリソースまたはリソース グループの移動は現在サポートされていません。
 
- Azure Stack Development Kit を登録するように勧める警告アラート **アクティブ化が必要**が表示されます。 これは正しい動作です。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- 管理ポータルのダッシュボードの [更新] タイルで、更新プログラムに関する情報を表示できません。 この問題を解決するには、そのタイルをクリックして更新してください。

-   管理ポータルに、Microsoft.Update.Admin コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、修復方法が、次のようにすべて表示されます。  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

    このアラートは無視してかまいません。 

#### <a name="health-and-monitoring"></a>正常性と監視
Azure Stack 管理ポータルに、**外部証明書の有効期限の保留中**という重大なアラートが表示される場合があります。  このアラートは無視してかまいませんが、Azure Stack Development Kit の操作に影響を与えます。 


#### <a name="marketplace"></a>マーケットプレース
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。
 
#### <a name="compute"></a>コンピューティング
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

- Azure Stack でサポートされるのは、固定サイズの VHD のみです。 Azure Stack のマーケットプレースで提供されていた一部のイメージは動的 VHD を使用しますが、これらは削除されました。 ダイナミック ディスクがアタッチされている仮想マシン (VM) のサイズを変更すると、VM は失敗した状態のままになります。

  この問題を軽減するには、VM のディスク、つまりストレージ アカウントの VHD BLOB を削除せずに VM を削除します。 次に、VHD をダイナミック ディスクから固定ディスクに変換した後、仮想マシンを再作成します。

- Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクに対応できます。

- VM イメージの作成に失敗した場合に、エラーが発生したが削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、エラーが発生した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成して 15 分たつと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試すことができます。

-  VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>ネットワーク
- **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定した場合は、可能な接続の種類として **[VNet 対 VNet]** が一覧に表示されます。 このオプションを選択しないでください。 現時点でサポートされているのは、**[サイト対サイト (IPsec)]** オプションのみです。

- VM を作成してパブリック IP アドレスに関連付けた後に、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。

- 内部負荷分散 (ILB) は、バックエンド VM の MAC アドレスを正しく処理しないため、バックエンド ネットワークで Linux インスタンスを使用すると ILB が中断します。  ILB は、バックエンド ネットワークの Windows インスタンスでは問題なく動作します。

-   IP 転送機能がポータルに表示されますが、IP 転送を有効にしても何も起こりません。 この機能は、まだサポートされていません。

- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" をサポートしています。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- "*自動*" の DNS サーバー設定を使用して作成された仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。
 
- Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    App Service の回避策: コントローラー インスタンスへのリモート デスクトップが必要な場合は、PowerShell を使用してネットワーク セキュリティ グループ内のセキュリティ規則を変更します。  "*許可*" してから、"*拒否*" する構成に復元する方法の例を次に示します。 
    
    - "*許可:*"
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - "*拒否:*"

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL および MySQL 
- ユーザーがデータベースを新しい SQL または MySQL SKU で作成するまでに、最大で 1 時間かかる場合があります。

- データベースをホストするサーバーは、リソース プロバイダーとユーザーのワークロード専用にする必要があります。 他のコンシューマー (App Services など) が使用中のインスタンスは使用できません。

#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

- インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。
 
#### <a name="usage-and-billing"></a>使用量と課金
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード
- *invoke-webrequest* PowerShell コマンドレットを使用して Github から Azure Stack ツールをダウンロードする場合、次のエラーが発生します。     
    -  "*invoke-webrequest : 要求は中止されました: SSL/TLS のセキュリティで保護されているチャネルを作成できませんでした。*"     

  このエラーは、GitHub で、Tlsv1 および Tlsv1.1 の暗号化標準 (PowerShell の既定) のサポートが最近廃止されたために発生します。 詳細については、[脆弱な暗号化標準の削除の通知](https://githubengineering.com/crypto-removal-notice/)に関するページを参照してください。

  この問題を解決するには、スクリプトの先頭に `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` を追加して、GitHub リポジトリからダウンロードするときに TLSv1.2 を使用するように PowerShell コンソールに強制します。


## <a name="build-201801032"></a>ビルド 20180103.2

### <a name="new-features-and-fixes"></a>新機能と修正

- Azure Stack 統合システムの Azure Stack 1712 更新プログラム リリース ノートの「[新機能と修正](.\.\azure-stack-update-1712.md#new-features-and-fixes)」セクションを参照してください。

    > [!IMPORTANT]
    > 「**新機能と修正**」セクションに記載されている項目の一部は、Azure Stack 統合システムにのみ関連します。

### <a name="known-issues"></a>既知の問題
 
#### <a name="deployment"></a>デプロイ
- デプロイ時に、IP アドレスでタイム サーバーを指定する必要があります。

#### <a name="infrastructure-management"></a>インフラストラクチャの管理
- **[Infrastructure backup]\(インフラストラクチャのバックアップ\)** ブレードでインフラストラクチャのバックアップは有効にしないでください。
- ベースボード管理コントローラー (BMC) の IP アドレスとモデルは、スケール ユニット ノードの重要な情報には表示されません。 Azure Stack Development Kit では、この動作は予期されるものです。

#### <a name="portal"></a>ポータル
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。
- リソース グループのプロパティを表示する際に、**[移動]** ボタンが無効になっています。 これは正しい動作です。 サブスクリプション間のリソース グループの移動は現在サポートされていません。
-  ドロップダウン リストからサブスクリプション、リソース グループ、または場所を選択するワークフローで、次の問題のいずれかが発生する場合があります。

   - 一覧の最上部に空の行が表示されることがある。 アイテムは問題なく選択できます。
   - ドロップダウン リストの項目の一覧が短い場合、アイテム名を表示できないことがある。
   - ユーザー サブスクリプションが複数ある場合、リソース グループのドロップダウン リストが空になることがある。 

   最後の 2 つの問題を回避するには、サブスクリプションまたはリソース グループの名前を入力するか (把握している場合)、代わりに PowerShell を使用します。

- Azure Stack Development Kit を登録するように勧める警告アラート "**アクティブ化が必要**" が表示されます。 これは正しい動作です。
- **コンポーネント**のリンクが**インフラストラクチャ ロール**のアラートからクリックされた場合、結果の **[概要]** ブレードはロードを試みて失敗します。 さらに、**[概要]** ブレードはタイムアウトになりません。
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 回避策として、PowerShell を使用してアクセス許可を確認できます。
 
#### <a name="marketplace"></a>マーケットプレース
- 一部のマーケットプレース項目は、互換性の問題のために、このリリースで削除されています。 これらは、さらに検証された後、再度有効にされます。
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。
 
#### <a name="compute"></a>コンピューティング
- ユーザーは geo 冗長ストレージを持つ仮想マシンを作成するオプションを利用できます。 この構成の場合、仮想マシンの作成が失敗します。 
- 仮想マシン可用性セットは、1 つの障害ドメインと 1 つの更新ドメインでのみ構成可能です。
- 仮想マシン スケール セットを作成するマーケットプレースのエクスペリエンスはありません。 スケール セットはテンプレートを使用して作成できます。
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

#### <a name="networking"></a>ネットワーク
- ポータルを使用して、パブリック IP アドレスを持つロード バランサーを作成することはできません。 この問題を回避するには、PowerShell を使用してロード バランサーを作成します。
- ネットワーク ロード バランサーの作成時に、ネットワーク アドレス変換 (NAT) 規則を作成する必要があります。 作成しない場合、ロード バランサーを作成した後に NAT 規則を追加しようとするときにエラーが表示されます。
- **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定した場合は、可能な接続の種類として **[VNet 対 VNet]** が一覧に表示されます。 このオプションを選択しないでください。 現時点でサポートされているのは、**[サイト対サイト (IPsec)]** オプションのみです。
- 仮想マシン (VM) を作成して IP アドレスに関連付けた後で、VM からパブリック IP アドレスの関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。 この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。 現時点では、新しい VM の作成には新しいパブリック IP アドレスのみを使用する必要があります。
- Azure Stack オペレーターが、VNET または ネットワーク セキュリティ グループのデプロイ、削除、変更を行えない場合があります。 この問題は、主に同じパッケージの更新を続けて試行する際に発生します。 これは、更新プログラムのパッケージ化の問題によって引き起こされますが、問題については現在調査中です。
- 内部負荷分散 (ILB) は、バックエンド VM の MAC アドレスを正しく処理せず、Linux インスタンスを中断します。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。 
- リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。

#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。
 
#### <a name="usage-and-billing"></a>使用量と課金
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。

#### <a name="identity"></a>ID

Azure Active Directory フェデレーション サービス (AD FS) が配置された環境では、**azurestack\azurestackadmin** アカウントは既定プロバイダー サブスクリプションの所有者ではなくなりました。 **Admin ポータル/adminmanagement エンドポイント**に **azurestack\azurestackadmin** でログインする代わりに **azurestack\cloudadmin** アカウントを使用できます。こうすることで、既定プロバイダー サブスクリプションを管理および使用できます。

> [!IMPORTANT]
> ADFS が配置された環境で **azurestack\cloudadmin** アカウントが既定プロバイダー サブスクリプションの所有者であっても、ホストに対する RDP の権限はありません。 ホストのログイン、アクセス、および管理については、必要に応じて **azurestack\azurestackadmin** アカウントまたはローカル管理者アカウントを引き続き使用します。


## <a name="build-201711221"></a>ビルド 20171122.1

### <a name="new-features-and-fixes"></a>新機能と修正

- Azure Stack 統合システムの Azure Stack 1711 更新プログラム リリース ノートの「[新機能と修正](.\.\azure-stack-update-1711.md#new-features-and-fixes)」セクションを参照してください。

    > [!IMPORTANT]
    > 「**新機能と修正**」セクションに記載されている項目の一部は、Azure Stack 統合システムにのみ関連します。

### <a name="known-issues"></a>既知の問題
 
#### <a name="deployment"></a>デプロイ
- デプロイ時に、IP アドレスでタイム サーバーを指定する必要があります。

#### <a name="infrastructure-management"></a>インフラストラクチャの管理
- **[Infrastructure backup]\(インフラストラクチャのバックアップ\)** ブレードでインフラストラクチャのバックアップは有効にしないでください。
- ベースボード管理コントローラー (BMC) の IP アドレスとモデルは、スケール ユニット ノードの重要な情報には表示されません。 Azure Stack Development Kit では、この動作は予期されるものです。

#### <a name="portal"></a>ポータル
- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。
- リソース グループのプロパティを表示する際に、**[移動]** ボタンが無効になっています。 これは正しい動作です。 サブスクリプション間のリソース グループの移動は現在サポートされていません。
-  ドロップダウン リストからサブスクリプション、リソース グループ、または場所を選択するワークフローで、次の問題のいずれかが発生する場合があります。

   - 一覧の最上部に空の行が表示されることがある。 アイテムは問題なく選択できます。
   - ドロップダウン リストの項目の一覧が短い場合、アイテム名を表示できないことがある。
   - ユーザー サブスクリプションが複数ある場合、リソース グループのドロップダウン リストが空になることがある。 

   最後の 2 つの問題を回避するには、サブスクリプションまたはリソース グループの名前を入力するか (把握している場合)、代わりに PowerShell を使用します。

- Azure Stack Development Kit を登録するように勧める警告アラート "**アクティブ化が必要**" が表示されます。 これは正しい動作です。
- **コンポーネント**のリンクが**インフラストラクチャ ロール**のアラートからクリックされた場合、結果の **[概要]** ブレードはロードを試みて失敗します。 さらに、**[概要]** ブレードはタイムアウトになりません。
- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。
- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 回避策として、PowerShell を使用してアクセス許可を確認できます。
 
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
- **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定した場合は、可能な接続の種類として **[VNet 対 VNet]** が一覧に表示されます。 このオプションを選択しないでください。 現時点でサポートされているのは、**[サイト対サイト (IPsec)]** オプションのみです。
- 仮想マシン (VM) を作成して IP アドレスに関連付けた後で、VM からパブリック IP アドレスの関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。 この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。 現時点では、新しい VM の作成には新しいパブリック IP アドレスのみを使用する必要があります。
- Azure Stack オペレーターが、VNET または ネットワーク セキュリティ グループのデプロイ、削除、変更を行えない場合があります。 この問題は、主に同じパッケージの更新を続けて試行する際に発生します。 これは、更新プログラムのパッケージ化の問題によって引き起こされますが、問題については現在調査中です。
- 内部負荷分散 (ILB) は、バックエンド VM の MAC アドレスを正しく処理せず、Linux インスタンスを中断します。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。 
- リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。

    > [!NOTE]
    > バージョンの互換性に関するガイダンスについては、[SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) と [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) の個々のセットアップ記事を参照してください。

#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。
 
#### <a name="usage-and-billing"></a>使用量と課金
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。

#### <a name="identity"></a>ID

Azure Active Directory フェデレーション サービス (AD FS) が配置された環境では、**azurestack\azurestackadmin** アカウントは既定プロバイダー サブスクリプションの所有者ではなくなりました。 **Admin ポータル/adminmanagement エンドポイント**に **azurestack\azurestackadmin** でログインする代わりに **azurestack\cloudadmin** アカウントを使用できます。こうすることで、既定プロバイダー サブスクリプションを管理および使用できます。

> [!IMPORTANT]
> ADFS が配置された環境で **azurestack\cloudadmin** アカウントが既定プロバイダー サブスクリプションの所有者であっても、ホストに対する RDP の権限はありません。 ホストのログイン、アクセス、および管理については、必要に応じて **azurestack\azurestackadmin** アカウントまたはローカル管理者アカウントを引き続き使用します。

