---
title: Azure Stack 1804 更新プログラム | Microsoft Docs
description: Azure Stack 統合システムの 1804 更新プログラムの内容、既知の問題、および更新プログラムをダウンロードする場所について説明します。
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
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 0190298cbf6352feeb71e365f5815e174c9e30cc
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413518"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この 1804 更新プログラム パッケージで機能強化および修正された内容、このリリースの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]        
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス    
Azure Stack 1804 更新プログラムのビルド番号は **20180513.1** です。   

### <a name="new-features"></a>新機能
この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。

- <!-- 15028744 - IS -->  **Visual Studio で、接続されていない Azure Stack を AD FS を利用してデプロイ**。 Visual Studio 内で、サブスクリプションを追加したり、AD FS フェデレーション ユーザー資格情報を利用して認証したりできるようになりました。 
 
- <!-- 1779474, 1779458 - IS --> **Av2 シリーズと F シリーズの仮想マシンを使用**。 Azure Stack で、Av2 シリーズと F シリーズの仮想マシン サイズに基づいて仮想マシンを利用できるようになりました。 詳細については、「[Azure Stack でサポートされている仮想マシンのサイズ](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)」を参照してください。 

- <!-- 1759172 - IS, ASDK --> **新しい管理サブスクリプション**。 1804 では、ポータルで新しいサブスクリプションの種類 2 つを利用できるようになりました。 これらの新しいサブスクリプションの種類は、既定のプロバイダー サブスクリプションに追加され、バージョン 1804 以降の新しい Azure Stack インストールで表示されます。 *このバージョンの Azure Stack ではこれらの新しいサブスクリプションの種類を使用しないでください*。 今後の更新プログラムでこれらのサブスクリプションの種類を使用できるようになった場合はお知らせします。 

  Azure Stack をバージョン 1804 に更新すると、2 種類の新しいサブスクリプションが表示されなくなります。 ただし、Azure Stack 統合システムの新しいデプロイと、Azure Stack Development Kit バージョン 1804 以降のインストールでは、3 種類すべてのサブスクリプションにアクセスできます。  

  これらの新しい種類のサブスクリプションは、Default Provider サブスクリプションを保護し、SQL Hosting サーバーなど、共有リソースのデプロイを簡易化するための大規模な変更の一環です。 Azure Stack の将来の更新でこの大規模な変更をさらに進めるとき、これらの新しい種類のサブスクリプションでデプロイされたリソースが失われることがあります。 

  現在表示されている 3 種類のサブスクリプションは次のとおりです。  
  - Default Provider サブスクリプション: 引き続き、この種類のサブスクリプションを使用してください。 
  - Metering サブスクリプション: *この種類のサブスクリプションは使用しないでください。*
  - Consumption サブスクリプション: *この種類のサブスクリプションは使用しないでください。*

  



## <a name="fixed-issues"></a>修正された問題

- <!-- IS, ASDK --> 管理ポータルで、情報を表示する前に [更新] タイルを更新する必要がなくなりました。
 
- <!-- 2050709  --> 管理ポータルを使用して、Blob service、Table service、および Queue サービスのストレージ メトリックを編集できるようになりました。
 
- <!-- IS, ASDK --> **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定するときに、**[サイト対サイト (IPsec)]** オプションを使用できるようになりました。

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。

## <a name="additional-releases-timed-with-this-update"></a>この更新に合わせた追加のリリース  
次が利用できるようになりましたが、Azure Stack 更新プログラム 1804 は必要ありません。
- **Microsoft Azure Stack System Center Operations Manager Monitoring Pack の更新プログラム**。 Microsoft System Center Operations Manager Monitoring Pack for Azure Stack の新しいバージョン (1.0.3.0) が[ダウンロード](https://www.microsoft.com/download/details.aspx?id=55184)できるようになりました。 このバージョンでは、接続済みの Azure Stack のデプロイを追加するときに、サービス プリンシパルを使用できます。 このバージョンでは、Operations Manager 内から直接修復アクションを実行できるようにする Update Management エクスペリエンスの機能もあります。 リソース プロバイダー、スケール単位、スケール ユニットのノードを表示する新しいダッシュボードもあります。

- **新しい Azure Stack 管理の PowerShell バージョン 1.3.0**。  Azure Stack PowerShell 1.3.0 がインストールに使用できるようになりました。 このバージョンでは、Azure Stack を管理するためにすべての管理リソース プロバイダーにコマンドを提供します。  このリリースにより、Azure Stack ツールの GitHub [リポジトリ](https://github.com/Azure/AzureStack-Tools)から一部のコンテンツが廃止される予定です。 

   インストールの詳細については、Azure Stack Module 1.3.0 の[指示](azure-stack-powershell-install.md)または[ヘルプ](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) コンテンツに従ってください。 

- **Azure Stack API Rest リファレンスの初回リリース**。 [Azure Stack のすべての管理リソース プロバイダーの API リファレンス](https://docs.microsoft.com/rest/api/azure-stack/)が発行されました。 


## <a name="before-you-begin"></a>開始する前に    

### <a name="prerequisites"></a>前提条件
- Azure Stack 1804 更新プログラムを適用する前に Azure Stack [1803 更新プログラム](azure-stack-update-1803.md)をインストールします。  
  
- 最新の入手できる[バージョン 1803 の更新プログラムまたは修正プログラム](azure-stack-update-1803.md#post-update-steps)をインストールします。 


### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題   
- 1804 更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 1804 への更新が完了すると、これらのアラートは自動的に閉じられます。   
 
- <!-- TBD - IS --> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。


### <a name="post-update-steps"></a>更新後の手順
1804 のインストール後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。  
 - [KB 4344114 - Azure Stack 修正プログラム 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)
ビルド **20180513.1** のインストール後について次の既知の問題があります。

#### <a name="portal"></a>ポータル
- <!-- TBD - IS ASDK --> このバージョンの Azure Stack では、OEM Extension パッケージを使用してドライバーの更新プログラムを適用することはできません。  この問題の回避策はありません。

- <!-- 1272111 - IS --> このバージョンの Azure Stack をインストールまたは更新すると、管理者ポータルで Azure Stack スケール ユニットを表示できなくなることがあります。  
  回避策: PowerShell を使用し、スケール ユニットに関する情報を表示します。 詳細については、Azure Stack Module 1.3.0 の[ヘルプ](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) コンテンツをご覧ください。 

- <!-- 2332636 - IS --> Azure Stack ID システムに AD FS を使用し、このバージョンの Azure Stack に更新すると、既定のプロバイダー サブスクリプションの既定の所有者は、組み込みの **CloudAdmin** ユーザーにリセットされます。  
  回避策: この更新プログラムのインストール後にこの問題を解決するには、「[Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)」の手順 3 を使用して、既定のプロバイダー サブスクリプションの所有者をリセットします。   

- <!-- TBD - IS ASDK --> 一部の管理サブスクリプションの種類は利用できません。  Azure Stack をこのバージョンにアップグレードすると、[バージョン 1804 で導入された](#new-features) 2 つのサブスクリプションの種類はコンソールに表示されません。 これは予期されることです。 利用不可のサブスクリプションの種類は、*Metering サブスクリプション*と *Consumption サブスクリプション*です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 *既定のプロバイダー* サブスクリプションの種類を引き続き使用する必要があります。  


- <!-- TBD -  IS ASDK -->管理者ポータルの[ドロップダウン リストから新しいサポート要求を開く](azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、次のリンクを使用します。     
    - Azure Stack 統合システムの場合は、https://aka.ms/newsupportrequest を使用します。

- <!-- 2403291 - IS ASDK --> 管理ポータルとユーザー ポータルの下部に水平スクロールバーが表示されない可能性があります。 水平スクロールバーにアクセスできない場合は、ポータルの左上にある階層リンク リストから表示するブレードの名前を選択して、階層リンクを使用してポータル内の前のブレードに移動します。
  ![階層リンク](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

- <!-- TBD - IS --> ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

- <!-- TBD - IS ASDK --> ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- <!-- TBD - IS ASDK --> Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- <!-- TBD - IS ASDK --> 管理ポータルに、*Microsoft.Update.Admin* コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、解決策が、次のようにすべて表示されます。  
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
 

#### <a name="compute"></a>コンピューティング
- <!-- TBD - IS --> 仮想マシンの展開用に仮想マシンのサイズを選択すると、VM の作成するときに F シリーズの VM のサイズはサイズ セレクターの一部として表示されません。 セレクターに *F8s_v2*、*F16s_v2*、*F32s_v2*、および *F64s_v2* の VM サイズが表示されません。  
  この問題を回避するには、次のいずれかの方法を使用して VM をデプロイします。 どの方法でも、使用する VM のサイズを指定する必要があります。

  - **Azure Resource Manager テンプレート:** テンプレートを使用する際に、テンプレートの *vmSize* を必要な VM サイズと同じに設定します。 たとえば、*F32s_v2* サイズを使用する VM をデプロイするには、次を使用します。  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) コマンドを使用して、`--size "Standard_F32s_v2"` と同様に VM サイズをパラメーターとして指定できます。

  - **PowerShell:** Powershell では、`-VMSize "Standard_F32s_v2"` と同様に VM サイズを指定するパラメーターとともに [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) を使用することができます。


- <!-- TBD - IS ASDK --> 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

- <!-- TBD - IS --> ポータルで **[新規]** > **[コンピューティング]** > **[可用性セット]** に移動して可用性セットを作成した場合、障害ドメインと更新ドメインが 1 の可用性セットのみを作成できます。 回避策として、新しい仮想マシンを作成する場合は、PowerShell、CLI、またはポータル内から可用性セットを作成します。

- <!-- TBD - IS ASDK --> Azure Stack ユーザー ポータルで仮想マシンを作成するときに、ポータルでは、D シリーズ VM に接続できるデータ ディスクの数に誤った値が表示されます。 サポートされているすべての D シリーズ VM は、Azure の構成と同数のデータ ディスクに対応できます。

- <!-- TBD - IS ASDK --> VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

- <!-- TBD - IS ASDK --> VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 IS ASDK --> Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  


#### <a name="networking"></a>ネットワーク
- <!-- 1766332 - IS ASDK --> **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

- <!-- 2388980 - IS ASDK --> VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。

- <!-- 2292271 - IS ASDK --> テナントのサブスクリプションに関連付けられているオファーとプランの一部であるネットワーク リソースのクォータ制限を引き上げた場合、新しい制限がそのサブスクリプションに適用されません。 ただし、クォータを増加した後に作成した新しいサブスクリプションには新しい制限が適用されます。 

  この問題を回避するには、プランがサブスクリプションに既に関連付けられている場合は、アドオン プランを使用して、ネットワーク クォータを増やします。 詳細については、[アドオン プランを利用できるようにする方法](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)を参照してください。

- <!-- 2304134 IS ASDK --> DNS ゾーン リソースまたはそれに関連付けられたルート テーブル リソースがあるサブスクリプションを削除することができません。 サブスクリプションを正常に削除するには、まずテナント サブスクリプションから DNS ゾーンとルート テーブルのリソースを削除する必要があります。 
  

- <!-- 1902460 - IS ASDK --> Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- <!-- 16309153 - IS ASDK --> "*自動*" の DNS サーバー設定を使用して作成した Virtual Network で、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。

- <!-- TBD - IS ASDK --> Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。

- <!-- 2096388 IS --> 管理ポータルを使用してネットワーク セキュリティ グループのルールを更新することができません。 

    App Service の回避策: コントローラー インスタンスへのリモート デスクトップ接続が必要な場合は、PowerShell を使用してネットワーク セキュリティ グループ内のセキュリティ規則を変更します。  "*許可*" する方法と、次にこの構成を復元して "*拒否*" する方法の例を次に示します。  
    
    - "*許可:*"
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

- <!-- TBD - IS --> SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。  

- <!-- IS, ASDK --> SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** または **[層]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。


> [!NOTE]  
> <!-- TBD - IS --> Azure Stack 1804 に更新した後も、以前にデプロイした SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。  たとえば、バージョン 1802 を使用している場合、最初にバージョン 1803 を適用してから 1804 に更新します。      
>   
> 更新プログラム 1804 をインストールしても、ユーザーによる現在の SQL または MySQL リソース プロバイダーの使用には影響しません。
> 使用しているリソース プロバイダーのバージョンに関係なく、データベース内のユーザー データは変更されず、アクセス可能な状態が維持されます。    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

- <!-- TBD - IS ASDK --> インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。

- <!-- TBD - IS ASDK --> 現在、App Service は、**既定のプロバイダー サブスクリプション**にのみデプロイできます。  今後の更新プログラムで、App Service は Azure Stack 1804 で導入された新しい Metering サブスクリプションにデプロイされ、既存のデプロイもすべてこの新しいサブスクリプションに統合されます。

#### <a name="usage"></a>使用法  
- <!-- TBD - IS ASDK --> パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>更新プログラムをダウンロードする
Azure Stack 1804 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="see-also"></a>関連項目
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
