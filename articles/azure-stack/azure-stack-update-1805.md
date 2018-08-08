---
title: Azure Stack 1805 更新プログラム | Microsoft Docs
description: 既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1805 更新プログラムの更新内容について説明します。
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
ms.openlocfilehash: abc96497c95397a6fab72672f1525462301c5cf9
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414635"
---
# <a name="azure-stack-1805-update"></a>Azure Stack 1805 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、この 1805 更新プログラム パッケージで機能強化および修正された内容、このバージョンの既知の問題、および更新プログラムをダウンロードする場所について説明します。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]        
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス    
Azure Stack 1805 更新プログラムのビルド番号は **1.1805.1.47** です。  

> [!TIP]  
> お客様のフィードバックに基づき、Microsoft Azure Stack に使用されているバージョン スキーマが更新されています。  今回の更新プログラム 1805 以降、新しいスキーマは現在のクラウド バージョンをより適切に表します。  
> 
> バージョン スキーマは *Version.YearYearMonthMonth.MinorVersion.BuildNumber* になりました。この 2 番目と 3 番目のセットはバージョンとリリースを示しています。 たとえば、1805.1 は、1805 の*開発完了* (RTM) バージョンを示しています。  


### <a name="new-features"></a>新機能
この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack には、*プレビュー機能*として *Syslog* クライアント**が追加されました。 このクライアントを使用すると、Azure Stack インフラストラクチャに関連する監査ログとセキュリティログを、Azure Stack の外部にある Syslog サーバーまたはセキュリティ情報イベント管理 (SIEM) ソフトウェアに転送できます。 現在、Syslog クライアントは、既定のポート 514 を介した認証されていない UDP 接続のみをサポートしています。 各 Syslog メッセージのペイロードは、共通イベント形式 (CEF) です。 

  Syslog クライアントを構成するには、特権エンドポイントで公開されている **Set-SyslogServer** コマンドレットを使用します。 

  このプレビューでは、次の 3 つのアラートが表示されることがあります。 Azure Stack でこれらのアラートが表示される場合、アラートには*説明*と*修復*のガイダンスが記載されます。 
  - タイトル: コードの整合性のオフ  
  - タイトル: 監査モードのコードの整合性 
  - タイトル: ユーザー アカウントの作成

  この機能はプレビュー段階ですが、運用環境では使用しないでください。   




### <a name="fixed-issues"></a>修正された問題

<!-- # - applicability -->
- 管理ポータル内の[ドロップダウンから新しいサポート リクエストを開く](azure-stack-manage-portals.md#quick-access-to-help-and-support)ことができない問題を修正しました。 このオプションは意図したとおりに機能するようになりました。 

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>開始する前に    

### <a name="prerequisites"></a>前提条件
- Azure Stack 1805 更新プログラムを適用する前に Azure Stack [1804 更新プログラム](azure-stack-update-1804.md)をインストールします。  
- 最新の入手できる[バージョン 1804 の更新プログラムまたは修正プログラム](azure-stack-update-1804.md#post-update-steps)をインストールします。   
- 更新プログラム 1805 のインストールを開始する前に、[Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかった操作上の問題を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。 

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題   
- 1805 更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 1805 への更新が完了すると、これらのアラートは自動的に閉じられます。   

- <!-- 2489559 - IS --> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。


### <a name="post-update-steps"></a>更新後の手順
1805 のインストール後、適用可能な修正プログラムがあればインストールします。 詳細については、以下のサポート技術情報と[サービス ポリシー](azure-stack-servicing-policy.md)に関するページを参照してください。  
 - [KB 4344102 - Azure Stack 修正プログラム 1.1805.7.57](https://support.microsoft.com/help/4344102)。


## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)
このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル  
- <!-- TBD - IS ASDK --> このバージョンの Azure Stack では、OEM Extension パッケージを使用してドライバーの更新プログラムを適用することはできません。  この問題の回避策はありません。

- <!-- 2551834 - IS, ASDK --> 管理ポータルまたはユーザー ポータルでストレージ アカウントの **[概要]** を選択すると、*[基本]* ウィンドウの情報が表示されません。  [基本] ウィンドウには、*リソース グループ*、*リージョン*、*サブスクリプション ID* などのアカウントに関する情報が表示されます。  [概要] のその他のオプションにアクセスできます。たとえば、*[サービス]*、*[監視]*、*[Explorer で開く]*、*[ストレージ アカウントの削除]* のオプションです。 

  利用不可の情報を表示するには、[Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell コマンドレットを使用します。 

- <!-- 2551834 - IS, ASDK --> 管理ポータルまたはユーザー ポータルでストレージ アカウントに **[タグ]** を選択すると、情報は読み込まれず、表示されません。  

  利用不可の情報を表示するには、[Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell コマンドレットを使用します。


- <!-- 2332636 - IS --> Azure Stack ID システムに AD FS を使用し、このバージョンの Azure Stack に更新すると、既定のプロバイダー サブスクリプションの既定の所有者は、組み込みの **CloudAdmin** ユーザーにリセットされます。  
  回避策: この更新プログラムのインストール後にこの問題を解決するには、「[Azure Stack で自動化をトリガーしてクレーム プロバイダー信頼を構成する](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)」の手順 3 を使用して、既定のプロバイダー サブスクリプションの所有者をリセットします。   

- <!-- TBD - IS ASDK --> 一部の管理サブスクリプションの種類は利用できません。  Azure Stack をこのバージョンにアップグレードすると、[バージョン 1804 で導入された](azure-stack-update-1804.md#new-features) 2 つのサブスクリプションの種類はコンソールに表示されません。 これは予期されることです。 利用不可のサブスクリプションの種類は、*Metering サブスクリプション*と *Consumption サブスクリプション*です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 *既定のプロバイダー* サブスクリプションの種類を引き続き使用する必要があります。  

- <!-- 2403291 - IS ASDK --> 管理ポータルとユーザー ポータルの下部に水平スクロールバーが表示されない可能性があります。 水平スクロールバーにアクセスできない場合は、ポータルの左上にある階層リンク リストから表示するブレードの名前を選択して、階層リンクを使用してポータル内の前のブレードに移動します。
  ![階層リンク](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

- <!-- TBD - IS --> ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

- <!-- TBD - IS ASDK --> ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- <!-- TBD - IS ASDK --> Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。


### <a name="health-and-monitoring"></a>正常性と監視
- <!-- 1264761 - IS ASDK --> 以下の詳細情報の*正常性コントローラー* コンポーネントのアラートが表示されることがあります:  
- 
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

  アラート #1 と #2 は、どちらも無視しても問題ありません。時間が経過すると、自動的に閉じられます。 

  *容量*に関する次のアラートも表示されることがあります。 このアラートでは、説明の中に示されている使用可能なメモリの割合が変化する可能性があります。  

  アラート #3:
   - 名前: メモリ容量不足
   - 重大度: 緊急
   - コンポーネント: 容量
   - 説明: このリージョンは、使用可能なメモリの 80.00% を超えるメモリを消費しています。 大量のメモリを使用する仮想マシンを作成すると、エラーが発生する可能性があります。  

  このバージョンの Azure Stack では、このアラートが間違って発行される可能性があります。 テナントの仮想マシンが引き続き正常にデプロイされる場合は、このアラートを無視しても問題はありません。 
  
  アラート #3 は、自動的に閉じることはありません。 このアラートを閉じた場合、Azure Stack は 15 分以内に同じアラートを作成します。  

- <!-- 2368581 - IS. ASDK --> Azure Stack オペレーターで、メモリ不足のアラートを受信し、テナント仮想マシンが*ファブリック VM の作成エラー*でデプロイできなかった場合、Azure Stack スタンプに使用できるメモリが不足している可能性があります。 ワークロードに使用できる容量の詳細については、[Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) に関するページを参照してください。 


### <a name="compute"></a>コンピューティング
- <!-- TBD - IS, ASDK --> 仮想マシンの展開用に仮想マシンのサイズを選択すると、VM の作成するときに F シリーズの VM のサイズはサイズ セレクターの一部として表示されません。 セレクターに *F8s_v2*、*F16s_v2*、*F32s_v2*、および *F64s_v2* の VM サイズが表示されません。  
  この問題を回避するには、次のいずれかの方法を使用して VM をデプロイします。 どの方法でも、使用する VM のサイズを指定する必要があります。

  - **Azure Resource Manager テンプレート:** テンプレートを使用する際に、テンプレートの *vmSize* を使用する VM サイズと同じに設定します。 たとえば、*F32s_v2* サイズを使用する VM をデプロイするには、次のように入力します。  

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

- <!-- TBD - IS ASDK --> Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

- <!-- TBD - IS ASDK --> VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

- <!-- TBD - IS ASDK --> VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 IS ASDK --> Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  


### <a name="networking"></a>ネットワーク
- <!-- TBD - IS ASDK --> 管理ポータルまたはユーザー ポータルで、ユーザー定義のルートを作成できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell) を使用します。

- <!-- 1766332 - IS ASDK --> **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

- <!-- 2388980 - IS ASDK --> VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元の VM に接続する結果になります。

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


### <a name="sql-and-mysql"></a>SQL および MySQL

- <!-- TBD - IS --> SQL または MySQL をホストするサーバー上に項目を作成できるのは、リソース プロバイダーのみです。 リソース プロバイダー以外がホスト サーバー上に項目を作成すると、不一致状態になる可能性があります。  

- <!-- IS, ASDK --> SQL と MySQL リソース プロバイダーの SKU を作成する場合、**[ファミリ]** または **[層]** 名では、スペースやピリオドなどの特殊文字はサポートされていません。


> [!NOTE]  
> <!-- TBD - IS --> Azure Stack 1805 に更新した後も、以前にデプロイした SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。 たとえば、バージョン 1803 を使用している場合、最初にバージョン 1804 を適用してから 1805 に更新します。      
>   
> 更新プログラム 1805 をインストールしても、ユーザーによる現在の SQL または MySQL リソース プロバイダーの使用には影響しません。
> 使用しているリソース プロバイダーのバージョンに関係なく、データベース内のユーザー データは変更されず、アクセス可能な状態が維持されます。    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> ユーザーは、サブスクリプションに最初の Azure 関数を作成する前に、ストレージ リソース プロバイダーを登録する必要があります。

- <!-- 2489178 - IS ASDK --> インフラストラクチャ (worker、管理、フロントエンド ロール) をスケールアウトするには、コンピューティングのリリース ノートの説明に従って PowerShell を使用する必要があります。

- <!-- TBD - IS ASDK --> 現在、App Service は、*既定のプロバイダー サブスクリプション*にのみデプロイできます。 今後の更新プログラムでは、Azure Stack 1804 で導入された新しい *Metering サブスクリプション*に App Service がデプロイされます。 Metering の使用がサポートされている場合、既存のすべてのデプロイがこの新しいサブスクリプションの種類に移行されます。


### <a name="usage"></a>使用法  
- <!-- TBD - IS ASDK --> パブリック IP アドレス使用量メーターのデータは、レコードが作成された日時を示す *TimeDate* スタンプではなく、各レコードに対して同じ *EventDateTime* 値を示します。 現在、このデータを使用して、パブリック IP アドレスの使用を正確に算出することはできません。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>更新プログラムをダウンロードする
Azure Stack 1805 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="see-also"></a>関連項目
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
