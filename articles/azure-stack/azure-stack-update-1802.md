---
title: Azure Stack 1802 更新プログラム | Microsoft Docs
description: Azure Stack 統合システムの 1802 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: af65ffc088c2beadf415b72ec284ef77f3e4f6d4
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757259"
---
# <a name="azure-stack-1802-update"></a>Azure Stack 1802 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この 1802 更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]        
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス    
Azure Stack 1802 更新プログラムのビルド番号は **20180302.1** です。  


## <a name="before-you-begin"></a>開始する前に    
> [!IMPORTANT]    
> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。


### <a name="prerequisites"></a>前提条件
- Azure Stack 1802 更新プログラムを適用する前に Azure Stack [1712 更新プログラム](azure-stack-update-1712.md)をインストールします。    

- Azure Stack 1802 更新プログラムを適用する前に、**AzS 修正プログラム - 1.0.180312.1 - ビルド 20180222.2** をインストールします。 この修正プログラムで Windows Defender が更新されます。この修正プログラムは Azure Stack の更新プログラムをダウンロードするときに入手できます。

  この修正プログラムをインストールするには、[Azure Stack](azure-stack-apply-updates.md) の更新プログラムをインストールする場合の通常の手順を実行します。 更新プログラムの名前は **AzS 修正プログラム - 1.0.180312.1** と表示され、次のファイルが含まれています。 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  これらのファイルをストレージ アカウントとコンテナーにアップロードした後に、管理ポータルの [更新] タイルからインストールを実行します。 
  
  Azure Stack の更新プログラムとは異なり、この更新プログラムをインストールしても Azure Stack のバージョンは変更されません。  この更新プログラムがインストールされているかどうかは、**[インストール済み更新プログラム]** の一覧を確認します。
 


### <a name="post-update-steps"></a>更新後の手順
1802 のインストール後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。 
- Azure Stack 修正プログラム **1.0.180302.4**。 [KB 4131152 - 既存の Virtual Machine Scale Sets を使用できなくなることがある]( https://support.microsoft.com/help/4131152) 

  この修正プログラムは、「[KB 4103348 - Azure Stack 更新プログラムをインストールしようとするとネットワーク コントローラー API サービスがクラッシュする](https://support.microsoft.com/help/4103348)」に詳細が説明されている問題も解決します。


### <a name="new-features-and-fixes"></a>新機能と修正
この更新プログラムには、Azure Stack に対する次の機能強化と修正が含まれています。

- **次の Azure Storage Service API バージョン**のサポートが追加されました。
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    詳細については、「[Azure Stack Storage: 違いと考慮事項](/azure/azure-stack/user/azure-stack-acs-differences)」を参照してください。

- **より大きな[ブロック BLOB](azure-stack-acs-differences.md)** のサポート:
    - 最大許容ブロック サイズは 4 MB から 100 MB に増加しました。
    - 最大 BLOB サイズは 195 GB から 4.75 TB に増加しました。  

- **インフラストラクチャのバックアップ**が [リソース プロバイダー] タイルに表示され、バックアップのアラートが有効になりました。 インフラストラクチャ バックアップ サービスの詳細については、「[インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復](azure-stack-backup-infrastructure-backup.md)」を参照してください。

- ***Test-AzureStack* コマンドレットの更新**。ストレージの診断を改善します。 このコマンドレットの詳細については、[Azure Stack の検証](azure-stack-diagnostic-test.md)に関するページを参照してください。

- **ロールベースのアクセス制御 (RBAC) の改善** - Azure Stack と AD FS がデプロイされている場合、RBAC を使用して、アクセス許可をユニバーサル ユーザー グループに委任できるようになりました。 RBAC の詳細については、[RBAC の管理](azure-stack-manage-permissions.md)に関するページを参照してください。

- **複数の障害ドメインのサポートが追加されました**。  詳細については、[Azure Stack の高可用性](azure-stack-key-features.md#high-availability-for-azure-stack)に関するページを参照してください。

- **物理メモリのアップグレードのサポート** - 初めてのデプロイ後に、Azure Stack に統合されたシステムのメモリ容量を増やすことができます。 詳しくは、「[Azure Stack の物理メモリ容量を管理する](azure-stack-manage-storage-physical-memory-capacity.md)」をご覧ください。

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題    
*更新プログラム 1802 のインストールに関する既知の問題はありません。*


### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)
ビルド **20180302.1** のインストール後について次の既知の問題があります。

#### <a name="portal"></a>ポータル
- <!-- 2332636 - IS -->  When you use AD FS for your Azure Stack identity system and update to this version of Azure Stack, the default owner of the default provider subscription is reset to the built-in **CloudAdmin** user.  
  回避策: この更新プログラムのインストール後にこの問題を解決するには、「[Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)」の手順 3 を使用して、既定のプロバイダー サブスクリプションの所有者をリセットします。   

- 管理者ポータルの[ドロップダウン リストから新しいサポート要求を開く](azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、次のリンクを使用します。     
    - Azure Stack 統合システムの場合は、https://aka.ms/newsupportrequest を使用します。

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- 管理ポータルのダッシュボードの [更新] タイルで、更新プログラムに関する情報を表示できません。 この問題を解決するには、そのタイルをクリックして更新してください。

-   管理ポータルに、Microsoft.Update.Admin コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、修復方法が、次のようにすべて表示されます。  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

    このアラートは無視してかまいません。 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- 管理ポータルとユーザー ポータルの両方で、古い API バージョン (2015-06-15 など) で作成されたストレージ アカウントの [概要] ブレードを選択した場合、[概要] ブレードの読み込みに失敗します。 これには、パッチと更新プログラムの実行時に使用される **updateadminaccount** などのシステム ストレージ アカウントが含まれます。 

  この問題を回避するには、PowerShell を使用して **Start-ResourceSynchronization.ps1** スクリプトを実行し、ストレージ アカウントの詳細へのアクセスを復元します。 [このスクリプトは GitHub から入手可能]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)であり、特権エンドポイントでサービス管理者の資格情報で実行する必要があります。 

- **[サービス正常性]** ブレードの読み込みに失敗します。 管理ポータルまたはユーザー ポータルで [サービス正常性] ブレードを開くと、Azure Stack にエラーが表示され、情報は読み込まれません。 これは正しい動作です。 [サービス正常性] を選択して開けますが、この機能はまだ利用できません。Azure Stack の今後のバージョンに実装されます。


#### <a name="health-and-monitoring"></a>正常性と監視
- <!-- 1264761 - IS ASDK -->  You might see alerts for the *Health controller* component that have the following details:  

  アラート #1:
   - 名前: インフラストラクチャ ロールの異常
   - 重大度: 警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーのハートビート スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。  

  アラート #2:
   - 名前: インフラストラクチャ ロールの異常
   - 重大度: 警告
   - コンポーネント: 正常性コントローラー
   - 説明: 正常性コントローラーの障害スキャナーは使用できません。 これは、正常性レポートとメトリックに影響する可能性があります。

  いずれのアラートも無視してかまいません。 時間が経つと自動的に閉じられます。  


#### <a name="marketplace"></a>マーケットプレース
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。

#### <a name="compute"></a>コンピューティング
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1802. This is due to the change in support for using availability sets with virtual machine scale sets. This support was added with version 1802.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message *Provisioning state failed*. 

  この問題はバージョン 1803 で解決されました。 バージョン 1802 のこの問題を解決するには、Azure Stack 修正プログラム **1.0.180302.4** をインストールしてください。 詳細については、[KB 4131152 (既存の Virtual Machine Scale Sets を使用できなくなることがある)]( https://support.microsoft.com/help/4131152) を参照してください。 

- Azure Stack では、固定タイプ VHD の使用のみがサポートされます。 Azure Stack のマーケットプレースで提供されていた一部のイメージはダイナミック VHD を使用しますが、これらは削除されました。 ダイナミック ディスクがアタッチされている仮想マシン (VM) のサイズを変更すると、VM は失敗した状態のままになります。

  この問題を軽減するには、VM のディスク、つまりストレージ アカウントの VHD BLOB を削除せずに VM を削除します。 次に、VHD をダイナミック ディスクから固定ディスクに変換した後、仮想マシンを再作成します。

- ポータルで **[新規]** > **[コンピューティング]** > **[可用性セット]** に移動して可用性セットを作成した場合、障害ドメインと更新ドメインが 1 の可用性セットのみを作成できます。 回避策として、新しい仮想マシンを作成する場合は、PowerShell、CLI、またはポータル内から可用性セットを作成します。

- Azure Stack ユーザー ポータルで仮想マシンを作成するときに、ポータルでは、D シリーズ VM に接続できるデータ ディスクの数に誤った値が表示されます。 サポートされているすべての D シリーズ VM は、Azure の構成と同数のデータ ディスクに対応できます。

- VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

-  VM のデプロイで拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>ネットワーク
- VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。

- 内部負荷分散 (ILB) では、バックエンド VM の MAC アドレスが正しく処理されないため、バックエンド ネットワークで Linux インスタンスを使用すると ILB が中断します。  ILB は、バックエンド ネットワーク上の Windows インスタンスでは問題なく動作します。

-   IP 転送機能がポータルに表示されますが、IP 転送を有効にしても何も起こりません。 この機能は、まだサポートされていません。

- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

- Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    App Service の回避策: コントローラー インスタンスへのリモート デスクトップ接続が必要な場合は、PowerShell を使用してネットワーク セキュリティ グループ内のセキュリティ規則を変更します。  "*許可*" する方法と、次にこの構成を復元して "*拒否*" する方法の例を次に示します。  
    
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
 - 続行する前に、これらのリリース ノートの冒頭にある「[開始する前に](#before-you-begin)」の重要な注意事項を確認します。
- ユーザーがデータベースを新しい SQL または MySQL の展開で作成するまでに、最大で 1 時間かかる場合があります。

- SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。  

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

> [!NOTE]  
> Azure Stack 1802 に更新した後も、以前に展開した SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。  たとえば、バージョン 1710 を使用する場合は、まずバージョン 1711 を適用してから 1712 を適用し、次に 1802 に更新します。      
>   
> 更新プログラム 1802 をインストールしても、ユーザーが現在 SQL または MySQL リソース プロバイダーを使用している方法に影響しません。
> 使用しているリソース プロバイダーのバージョンに関係なく、データベース内のユーザー データは変更されず、アクセス可能な状態が維持されます。    


#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

- インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード
- PowerShell の *invoke-webrequest* コマンドレットを使用して Github から Azure Stack ツールをダウンロードすると、次のエラーが発生します。     
    -  "*invoke-webrequest : 要求は中止されました: SSL/TLS のセキュリティで保護されたチャネルを作成できませんでした。*"     

  このエラーは、GitHub で、Tlsv1 および Tlsv1.1 の暗号化標準 (PowerShell の既定) のサポートが最近廃止されたために発生します。 詳細については、[脆弱な暗号化標準の削除の通知](https://githubengineering.com/crypto-removal-notice/)に関するページを参照してください。

  この問題を解決するには、スクリプトの先頭に `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` を追加して、GitHub リポジトリからダウンロードするときに TLSv1.2 を使用するよう PowerShell コンソールに強制します。


## <a name="download-the-update"></a>更新プログラムをダウンロードする
Azure Stack 1802 更新プログラム パッケージは、[ここで](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="more-information"></a>詳細情報
Microsoft は、更新プログラム 1710 でインストールされる特権エンドポイント (PEP) を使用して、更新プログラムを監視および再開するための方法を提供しています。

- 「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)」をご覧ください。

## <a name="see-also"></a>関連項目

- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
