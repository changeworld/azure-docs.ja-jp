---
title: Azure Stack 1803 更新プログラム | Microsoft Docs
description: Azure Stack 統合システムの 1803 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。
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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318811"
---
# <a name="azure-stack-1803-update"></a>Azure Stack 1803 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この 1803 更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]        
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス    
Azure Stack 1803 更新プログラムのビルド番号は **20180329.1** です。


## <a name="before-you-begin"></a>開始する前に    
> [!IMPORTANT]    
> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。


### <a name="prerequisites"></a>前提条件
- Azure Stack 1803 更新プログラムを適用する前に Azure Stack [1802 更新プログラム](azure-stack-update-1802.md)をインストールします。   

- Azure Stack 1803 更新プログラムを適用する前に、**AzS 修正プログラム - 1.0.180312.1 - ビルド 20180222.2** をインストールします。 この修正プログラムで Windows Defender が更新されます。この修正プログラムは Azure Stack の更新プログラムをダウンロードするときに入手できます。

  この修正プログラムをインストールするには、[Azure Stack](azure-stack-apply-updates.md) の更新プログラムをインストールする場合の通常の手順を実行します。 更新プログラムの名前は **AzS 修正プログラム - 1.0.180312.1** と表示され、次のファイルが含まれています。 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  これらのファイルをストレージ アカウントとコンテナーにアップロードした後に、管理ポータルの [更新] タイルからインストールを実行します。 
  
  Azure Stack の更新プログラムとは異なり、この更新プログラムをインストールしても Azure Stack のバージョンは変更されません。 この更新プログラムがインストールされているかどうかは、**[インストール済み更新プログラム]** の一覧を確認します。



### <a name="new-features"></a>新機能 
この更新プログラムには、Azure Stack に対する次の機能強化と修正が含まれています。

- **Azure Stack シークレットの更新** - (アカウントおよび証明書)。 シークレットの管理に関する詳細については、「[Azure Stack でシークレットをローテーションする](azure-stack-rotate-secrets.md)」をご覧ください。 

- <!-- 1914853 --> HTTP を使用して管理者ポータルとユーザー ポータルにアクセスする場合の、**HTTPS への自動ダイレクト**。 この機能強化は、Azure Stack の [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) フィードバックに基づいて行われたものです。 

- <!-- 2202621  --> **Marketplace へのアクセス** – Azure portal の場合と同じ方法で、管理者ポータルとユーザー ポータル内から [[+新規]](https://ms.portal.azure.com/#create/hub) オプションを使用して、Azure Stack Marketplace を開けるようになりました。
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack では、[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) が管理者ポータルとユーザー ポータルに追加されます。 これには、メトリックおよびアクティビティ ログ用の新しいエクスプローラーが含まれます。 外部ネットワークからこの Azure Monitor にアクセスするには、ファイアウォール構成でポート **13012** を開く必要があります。 Azure Stack で必要なポートの詳細については、「[Azure Stack とデータセンターの統合 - エンドポイントの公開](azure-stack-integrate-endpoints.md)」を参照してください。

   この変更の一環として、**[More services]\(その他のサービス\)** 下では、*[監査ログ]* が *[アクティビティ ログ]* と表示されるようになります。 Azure Portal との機能の一貫性が確保できました。 

- <!-- 1664791 --> **スパース ファイル** - Azure Stack に新しいイメージを追加する場合や、マーケットプレース シンジケーションを介してイメージを追加する場合は、イメージがスパース ファイルに変換されます。 Azure Stack バージョン 1803 を使用する前に追加されたイメージを変換することはできません。 この機能を利用するには、代わりにマーケットプレース シンジケーションを使用して、それらのイメージを再送信する必要があります。 
 
   スパース ファイルは、記憶域スペースの使用を減らして I/O を改善するために使用される、効率的なファイル形式です。  詳細については、Windows Server の「[Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse)」 (fsutil sparse) をご覧ください。 

### <a name="fixed-issues"></a>修正された問題

- <!-- 1739988 --> 内部負荷分散 (ILB) でバックエンド VM の MAC アドレスが正しく処理されるようになりました。これにより、バックエンド ネットワークで Linux インスタンスを使用した場合は、ILB でバックエンド ネットワークへのパケットが破棄されるようになります。 ILB は、バックエンド ネットワーク上の Windows インスタンスでは問題なく動作します。 

- <!-- 1805496 --> Azure Stack では、Azure とは異なる IKE ポリシーの設定を使用するため、Azure Stack 間の VPN 接続が切断されるという問題。 SALifetime (時間) と SALiftetime (バイト) の値が Azure と互換性がなかったため、Azure の設定と一致するように 1803 で変更されました。 1803 より前の SALifetime (秒) の値は 14,400 でしたが、1803 では 27,000 に変更されました。 1803 より前の SALifetime (バイト) の値は 819,200 でしたが、1803 では 33,553,408 に変更されました。

- <!-- 2209262 --> ポータルに以前は VPN 接続が表示されていたが、IP 転送を有効にしたり、切り替えたりすることができないという IP の問題。 この機能は既定で有効でになりますが、これを変更する機能はまだサポートされていません。  コントロールはポータルから削除されました。 

- <!-- 1766332 --> Azure Stack では、ポータルにオプションが表示される場合でも、ポリシー ベースの VPN ゲートウェイはサポートされません。  オプションはポータルから削除されました。 

- <!-- 1868283 --> Azure Stack では、ダイナミック ディスクで作成される仮想マシンのサイズ変更が行われないようになりました。 

- <!-- 1756324 --> 仮想マシンの利用状況データが、1 時間間隔で分割されるようになりました。 これは Azure と一致しています。 

- <!--  2253274 --> 管理者ポータルとユーザー ポータルで、vNet サブネットの [設定] ブレードを読み込めない問題。 回避策として、PowerShell と [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) コマンドレットを使用して、この情報を表示および管理します。

- 仮想マシンを作成する際、VM サイズ用のサイズを選択したときのメッセージ "*価格を表示できません*" は表示されなくなりました。

- さまざまな修正 - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。


### <a name="changes"></a>変更点
- 新しく作成されたオファーの状態を *[プライベート]* から *[パブリック]* または *[使用停止]* に切り替える方法が変更されました。 詳細については、[オファ―の作成](azure-stack-create-offer.md)に関するページをご覧ください。


### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題    
<!-- 2328416 --> 1803 更新プログラムのインストール時に、Blob service と Blob service を使用する内部サービスのダウンタイムが発生する場合があります。 これには、一部の仮想マシンの操作が含まれます。 このダウン タイムにより、テナントの操作が失敗したり、データにアクセスできないサービスからのアラートが表示されたりする可能性があります。 この問題は、更新プログラムのインストールの完了時に自動的に解決します。 



### <a name="post-update-steps"></a>更新後の手順
- 1803 のインストール後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。

  - [KB 4341390 - Azure Stack 修正プログラム 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390)。

- この更新プログラムをインストールしたら、ファイアウォールの設定で[必要なポート](azure-stack-integrate-endpoints.md)が開いていることを確認します。 たとえば、この更新プログラムには、監査ログをアクティビティ ログに変更することを含む *Azure Monitor* が入っています。 この変更によりポート 13012 が使用されるようになったため、開いている必要があります。  


### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)
ビルド **20180323.2** のインストール後について次の既知の問題があります。

#### <a name="portal"></a>ポータル
- <!-- 2332636 - IS --> Azure Stack ID システムに AD FS を使用し、このバージョンの Azure Stack に更新すると、既定のプロバイダー サブスクリプションの既定の所有者は、組み込みの **CloudAdmin** ユーザーにリセットされます。  
  回避策: この更新プログラムのインストール後にこの問題を解決するには、「[Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)」の手順 3 を使用して、既定のプロバイダー サブスクリプションの所有者をリセットします。   

- 管理者ポータルの[ドロップダウン リストから新しいサポート要求を開く](azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、次のリンクを使用します。     
    - Azure Stack 統合システムの場合は、https://aka.ms/newsupportrequest を使用します。

- <!-- 2050709 --> 管理ポータルで、Blob service、Table service、および Queue サービスのストレージ メトリックを編集できません。 [ストレージ] に移動し、Blob、Table、または Queue サービスのタイルを選択すると、新しいブレードが開き、そのサービスのメトリック グラフが表示されます。 メトリック グラフ タイルの上部にある [編集] を選択すると、[グラフの編集] ブレードが開きますが、メトリックを編集するオプションは表示されません。

- 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

- ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- 管理ポータルのダッシュボードの [更新] タイルで、更新プログラムに関する情報を表示できません。 この問題を解決するには、そのタイルをクリックして更新してください。

- 管理ポータルに、*Microsoft.Update.Admin* コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、解決策が、次のようにすべて表示されます。  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

  このアラートは無視してかまいません。 


#### <a name="health-and-monitoring"></a>正常性と監視
- <!-- 1264761 - IS ASDK --> 以下の詳細情報の*正常性コントローラー* コンポーネントのアラートが表示されることがあります:  

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

- ポータルで **[新規]** > **[コンピューティング]** > **[可用性セット]** に移動して可用性セットを作成した場合、障害ドメインと更新ドメインが 1 の可用性セットのみを作成できます。 回避策として、新しい仮想マシンを作成する場合は、PowerShell、CLI、またはポータル内から可用性セットを作成します。

- Azure Stack ユーザー ポータルで仮想マシンを作成するときに、ポータルでは、D シリーズ VM に接続できるデータ ディスクの数に誤った値が表示されます。 サポートされているすべての D シリーズ VM は、Azure の構成と同数のデータ ディスクに対応できます。

- VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

-  VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 --> Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  


#### <a name="networking"></a>ネットワーク
- VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。



- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

- Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。

- <!-- 2096388 --> 管理ポータルを使用してネットワーク セキュリティ グループのルールを更新することができません。 

    App Service の回避策: コントローラー インスタンスへのリモート デスクトップ接続が必要な場合は、PowerShell を使用してネットワーク セキュリティ グループ内のセキュリティ規則を変更します。  "*許可*" する方法と、次にこの構成を復元して "*拒否*" する方法の例を次に示します。  
    
    - "*許可:*"
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- IS, ASDK --> SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。

> [!NOTE]  
> Azure Stack 1803 に更新した後も、以前にデプロイした SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。  たとえば、バージョン 1711 を使用している場合は、最初にバージョン 1712、次に 1802 を適用してから、1803 に更新します。      
>   
> 更新プログラム 1803 をインストールしても、ユーザーによる現在の SQL または MySQL リソース プロバイダーの使用には影響しません。
> 使用しているリソース プロバイダーのバージョンに関係なく、データベース内のユーザー データは変更されず、アクセス可能な状態が維持されます。    



#### <a name="app-service"></a>App Service
- ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

- インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。


#### <a name="usage"></a>使用法  
- パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード
- PowerShell の *invoke-webrequest* コマンドレットを使用して Github から Azure Stack ツールをダウンロードすると、次のエラーが発生します。     
    -  "*invoke-webrequest : 要求は中止されました: SSL/TLS のセキュリティで保護されたチャネルを作成できませんでした。*"     

  このエラーは、GitHub で、Tlsv1 および Tlsv1.1 の暗号化標準 (PowerShell の既定) のサポートが最近廃止されたために発生します。 詳細については、[脆弱な暗号化標準の削除の通知](https://githubengineering.com/crypto-removal-notice/)に関するページを参照してください。

  この問題を解決するには、スクリプトの先頭に `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` を追加して、GitHub リポジトリからダウンロードするときに TLSv1.2 を使用するよう PowerShell コンソールに強制します。


## <a name="download-the-update"></a>更新プログラムをダウンロードする
Azure Stack 1803 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="see-also"></a>関連項目
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
