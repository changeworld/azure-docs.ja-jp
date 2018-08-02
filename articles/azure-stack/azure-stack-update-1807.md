---
title: Azure Stack 1807 更新プログラム | Microsoft Docs
description: 既知の問題、更新プログラムをダウンロードする場所など、Azure Stack 統合システムの 1807 更新プログラムの更新内容について説明します。
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
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227675"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 更新プログラム

*適用対象: Azure Stack 統合システム*

この記事では、1807 更新プログラム パッケージの内容について説明します。 このバージョンの Azure Stack の既知の問題、機能強化、修正された内容のほか、更新プログラムをダウンロードする場所について取り上げます。 既知の問題は、更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]        
> 更新プログラム パッケージは、Azure Stack 統合システム専用です。 Azure Stack Development Kit にこの更新プログラム パッケージは適用しないでください。

## <a name="build-reference"></a>ビルドのリファレンス    
Azure Stack 1807 更新プログラムのビルド番号は **1.1807.XX.X** です。  


### <a name="new-features"></a>新機能
この更新プログラムには、Azure Stack に対する次の機能強化が含まれています。

- <!-- 1658937 | ASDK, IS --> **定義済みのスケジュールでバックアップを開始** - アプライアンスとしての Azure Stack に、インフラストラクチャ バックアップを自動で定期的にトリガーする機能が追加され、人の介入が不要になりました。 また、定義されている保有期間を超えたバックアップについては、外部共有が自動的にクリーンアップされます。 

- <!-- 2496385 | ASDK, IS --> **合計バックアップ時間にデータ転送時間が追加されました。**

-   <!-- 1702130 | ASDK, IS --> **外部バックアップ容量に、外部共有の正確な容量が表示されるようになりました。** (従来は 10 GB にハードコーディングされていました。)

- <!-- 2508488 |  IS   -->   容量を拡張するには、[新たにスケール ユニット ノードを追加](azure-stack-add-scale-node.md)します。



### <a name="fixed-issues"></a>修正された問題

- <!-- 448955 | IS ASDK --> UTC+N タイム ゾーンでデプロイされているシステムをアクティビティ ログから検索するクエリが問題なく実行できるようになりました。    

- <!-- 2319627 |  ASDK, IS --> バックアップ構成パラメーター (パス/ユーザー名/パスワード/暗号化キー) の事前確認で、バックアップ構成に間違った設定が適用される問題を修正しました。 (以前は、バックアップに間違った設定が適用され、トリガーされた時点でバックアップが失敗していました。)

- <!-- 2215948 |  ASDK, IS --> 外部共有から手動でバックアップを削除したときにバックアップ リストが最新の情報に更新されるようになりました。

- <!-- 2332636 - IS -->  このバージョンに更新すると、AD FS を使ってデプロイするときに、既定のプロバイダー サブスクリプションの既定の所有者が、ビルトインの CloudAdmin ユーザーにリセットされていましたが、この問題は修正されました。

- <!-- 2360715 |  ASDK, IS -->  データセンターの統合を設定する際、今後は、共有場所の AD FS メタデータ ファイルにはアクセスしません。 詳細については、「[フェデレーション メタデータ ファイルを指定して AD FS の統合を設定する](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)」を参照してください。 

- <!-- 2388980 | ASDK, IS --> ネットワーク インターフェイスまたはロード バランサーに割り当てられている既存のパブリック IP アドレスを新しいネットワーク インターフェイスまたはロード バランサーに割り当てることができない問題を修正しました。  

-   <!--2292271 | ASDK, IS --> 変更したクォータ制限が既存のサブスクリプションに適用されない問題を修正しました。  今後は、テナントのサブスクリプションに関連付けられているオファーとプランについて、そこに含まれるネットワーク リソースのクォータ制限を引き上げると、新しいサブスクリプションだけでなく既存のサブスクリプションにも新しい制限が適用されます。

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>開始する前に    

### <a name="prerequisites"></a>前提条件
- Azure Stack 1807 更新プログラムを適用する前に Azure Stack [1805 更新プログラム](azure-stack-update-1805.md)をインストールします。  更新プログラム 1806 は存在しません。  

- 更新プログラム 1807 のインストールを開始する前に、[Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかった操作上の問題を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題   
- <!-- 2468613 - IS --> この更新プログラムのインストール中に、"*エラー – FaultType UserAccounts.New のテンプレートが見つかりません*" というタイトルのアラートが表示される場合があります。  これらのアラートは無視してかまいません。 この更新プログラムのインストール後、これらのアラートは自動的に閉じられます。   

- <!-- 2489559 - IS --> この更新プログラムのインストール中に仮想マシンを作成しようとしないでください。 更新プログラムの管理方法については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md#plan-for-updates)」を参照してください。


### <a name="post-update-steps"></a>更新後の手順
*更新プログラム 1807 には更新後の手順はありません。*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>既知の問題 (インストール後)
このビルド バージョンのインストール後について次の既知の問題があります。

### <a name="portal"></a>ポータル  
- <!-- 2551834 - IS, ASDK --> 管理ポータルまたはユーザー ポータルでストレージ アカウントの **[概要]** を選択すると、*[基本]* ウィンドウの情報が表示されません。  [基本] ウィンドウには、*リソース グループ*、*リージョン*、*サブスクリプション ID* などのアカウントに関する情報が表示されます。  [概要] のその他のオプションにアクセスできます。たとえば、*[サービス]*、*[監視]*、*[Explorer で開く]*、*[ストレージ アカウントの削除]* のオプションです。

  利用不可の情報を表示するには、[Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell コマンドレットを使用します。

- <!-- 2551834 - IS, ASDK --> 管理ポータルまたはユーザー ポータルでストレージ アカウントに **[タグ]** を選択すると、情報は読み込まれず、表示されません。  

  利用不可の情報を表示するには、[Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell コマンドレットを使用します。 

- <!-- TBD - IS ASDK --> 一部の管理サブスクリプションの種類は利用できません。  Azure Stack をこのバージョンにアップグレードすると、[バージョン 1804 で導入された](azure-stack-update-1804.md#new-features) 2 つのサブスクリプションの種類はコンソールに表示されません。 これは予期されることです。 利用不可のサブスクリプションの種類は、*Metering サブスクリプション*と *Consumption サブスクリプション*です。 これらのサブスクリプションの種類は、バージョン 1804 以降の新しい Azure Stack 環境では表示されますが、まだ使用できる状態ではありません。 *既定のプロバイダー* サブスクリプションの種類を引き続き使用する必要があります。  

- <!-- 2403291 - IS ASDK --> 管理ポータルとユーザー ポータルの下部に水平スクロールバーが表示されない可能性があります。 水平スクロールバーにアクセスできない場合は、ポータルの左上にある階層リンク リストから表示するブレードの名前を選択して、階層リンクを使用してポータル内の前のブレードに移動します。
  ![階層リンク](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 管理ポータルでコンピューティング リソースやストレージ リソースを表示できない場合があります。 この問題の原因は、更新プログラムのインストール中にエラーが発生し、更新が正常に行われたことが誤って報告されたためです。 この問題が発生した場合は、Microsoft カスタマー サポート サービスにお問い合わせください。

- <!-- TBD - IS --> ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを復元するには、ポータルの右上にある歯車アイコンを選択し、**[既定の設定に戻す]** を選択します。

- <!-- TBD - IS ASDK --> ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- <!-- TBD - IS ASDK --> Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。


### <a name="health-and-monitoring"></a>正常性と監視
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

  どちらのアラートも無視しても問題ありません。時間が経過すると、自動的に閉じられます。  

- <!-- 2368581 - IS. ASDK --> Azure Stack オペレーターで、メモリ不足のアラートを受信し、テナント仮想マシンが*ファブリック VM の作成エラー*でデプロイできなかった場合、Azure Stack スタンプに使用できるメモリが不足している可能性があります。 ワークロードに使用できる容量の詳細については、[Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) に関するページを参照してください。


### <a name="compute"></a>コンピューティング
- スケール ユニットの管理に <!-- 2724873 - IS --> PowerShell コマンドレット **Start-AzsScaleUnitNode** または **Stop-AzsScaleunitNode** を使った場合、スケール ユニットを開始または停止しようとすると、1 回目は失敗することがあります。 初回実行時にコマンドレットが失敗した場合は、もう一度コマンドレットを実行してください。 2 回目は操作が正常に完了します。 

- <!-- 2494144 - IS, ASDK --> 仮想マシンの展開用に仮想マシンのサイズを選択すると、VM の作成するときに F シリーズの VM のサイズはサイズ セレクターの一部として表示されません。 セレクターに *F8s_v2*、*F16s_v2*、*F32s_v2*、および *F64s_v2* の VM サイズが表示されません。  
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

  次に、前に失敗した VM イメージのダウンロードを再試行できます。

- <!-- TBD - IS ASDK --> VM の展開で拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 IS ASDK --> Linux の VM 診断は、Azure Stack でサポートされていません。 VM 診断を有効にして Linux VM を展開すると、展開が失敗します。 診断設定で Linux VM の基本メトリックを有効にした場合も、展開が失敗します。  


### <a name="networking"></a>ネットワーク  

- <!-- 1766332 - IS ASDK --> **[ネットワーク]** で、**[Create VPN Gateway]\(VPN ゲートウェイの作成\)** をクリックして VPN 接続を設定する場合、VPN の種類として **[ポリシー ベース]** が表示されます。 このオプションを選択しないでください。 Azure Stack では **[ルート ベース]** オプションのみがサポートされています。

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
> <!-- TBD - IS --> このバージョンの Azure Stack に更新した後も、以前にデプロイした SQL および MySQL リソース プロバイダーを引き続き使用できます。  新しいリリースが公開されたら、SQL と MySQL を更新することをお勧めします。 Azure Stack と同様に、SQL と MySQL リソース プロバイダーに順番に更新プログラムを適用します。 たとえば、バージョン 1804 を使用している場合、最初にバージョン 1805 を適用してから 1807 に更新します。      
>   
> この更新プログラムをインストールしても、ユーザーによる現在の SQL または MySQL リソース プロバイダーの使用には影響しません。
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
Azure Stack 1807 更新プログラム パッケージは、[ここから](https://aka.ms/azurestackupdatedownload)ダウンロードできます。


## <a name="see-also"></a>関連項目
- 特権エンドポイント (PEP) を使用して更新プログラムを監視および再開するには、「[特権エンドポイントを使用して Azure Stack での更新プログラムをモニターする](azure-stack-monitor-update.md)」をご覧ください。
- Azure Stack での更新プログラム管理の概要については、「[Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)」を参照してください。
- Azure Stack に更新プログラムを適用する方法については、「[Azure Stack で更新を適用する](azure-stack-apply-updates.md)」を参照してください。
