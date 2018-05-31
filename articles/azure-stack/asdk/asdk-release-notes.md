---
title: Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs
description: Azure Stack Development Kit の機能強化、修正、既知の問題。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 9a396c51e8eb6262726b7590498500bfb4528924
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258406"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack Development Kit のリリース ノート
以下のリリース ノートでは、Azure Stack Development Kit の機能強化、修正、既知の問題に関する情報を提供します。 実行しているバージョンが不明な場合は、[ポータルを使用して確認](.\.\azure-stack-updates.md#determine-the-current-version)できます。

> [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [フィード](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)をサブスクライブして、ASDK の新着情報を常に把握するようにしてください。


## <a name="build-201805131"></a>ビルド 20180513.1

### <a name="new-features"></a>新機能 
このビルドには、Azure Stack に対する次の機能強化と修正が含まれています。  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - *既定のプロバイダー サブスクリプション*。 このサブスクリプションはコア インフラストラクチャにのみ使用します。 このサブスクリプションには、リソースまたはリソース プロバイダーをデプロイしないでください。
  - *測定サブスクリプション*。 このサブスクリプションは、リソース プロバイダーのデプロイに使用します。 このサブスクリプションでデプロイされているリソースには課金されません。
  - *消費サブスクリプション*。 このサブスクリプションは、デプロイするその他のワークロードに使用します。 ここでデプロイされたリソースは、通常の使用料で課金されます。


### <a name="fixed-issues"></a>修正された問題
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **さまざまな修正** - パフォーマンス、安定性、セキュリティ、Azure Stack で使用されるオペレーティング システムが修正されました。

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>この更新に合わせた追加のリリース  
次が利用できるようになりましたが、Azure Stack 更新プログラム 1804 は必要ありません。
- **Microsoft Azure Stack System Center Operations Manager Monitoring Pack の更新プログラム**。 Microsoft System Center Operations Manager Monitoring Pack for Azure Stack の新しいバージョン (1.0.3.0) が[ダウンロード](https://www.microsoft.com/download/details.aspx?id=55184)できるようになりました。 このバージョンでは、接続済みの Azure Stack のデプロイを追加するときに、サービス プリンシパルを使用できます。 このバージョンでは、Operations Manager 内から直接修復アクションを実行できるようにする Update Management エクスペリエンスの機能もあります。 リソース プロバイダー、スケール単位、スケール ユニットのノードを表示する新しいダッシュボードもあります。

- **新しい Azure Stack 管理の PowerShell バージョン 1.3.0**。  Azure Stack PowerShell 1.3.0 がインストールに使用できるようになりました。 このバージョンでは、Azure Stack を管理するためにすべての管理リソース プロバイダーにコマンドを提供します。  このリリースにより、Azure Stack ツールの GitHub [リポジトリ](https://github.com/Azure/AzureStack-Tools)から一部のコンテンツが廃止される予定です。 

   インストールの詳細については、Azure Stack Module 1.3.0 の[指示](.\.\azure-stack-powershell-install.md)または[ヘルプ](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) コンテンツに従ってください。 

- **Azure Stack API Rest リファレンスの初回リリース**。 [Azure Stack のすべての管理リソース プロバイダーの API リファレンス](https://docs.microsoft.com/rest/api/azure-stack/)が発行されました。 

### <a name="known-issues"></a>既知の問題
 
#### <a name="portal"></a>ポータル
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure Stack Development Kit の場合は、https://aka.ms/azurestackforum を使います。    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![階層リンク](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

    このアラートは無視してかまいません。 

#### <a name="compute"></a>コンピューティング
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>ネットワーク
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  この問題を回避するには、プランがサブスクリプションに既に関連付けられている場合は、アドオン プランを使用して、ネットワーク クォータを増やします。 詳細については、[アドオン プランを利用できるようにする方法](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)を参照してください。

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL および MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>使用法  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>ビルド 20180329.1

### <a name="new-features-and-fixes"></a>新機能と修正
Azure Stack 統合システム バージョン 1803 に対してリリースされた新機能と修正は、Azure Stack Development Kit に適用されます。 詳しくは、Azure Stack 1803 更新プログラム リリース ノートの「[新機能](.\.\azure-stack-update-1803.md#new-features)」および「[修正された問題](.\.\azure-stack-update-1803.md#fixed-issues)」セクションをご覧ください。  
> [!IMPORTANT]    
> 「**新機能**」および「**修正された問題**」セクションに記載されている項目の一部は、Azure Stack 統合システムにのみ関連します。

### <a name="changes"></a>変更点
- 新しく作成されたオファーの状態を *[プライベート]* から *[パブリック]* または *[使用停止]* に切り替える方法が変更されました。 詳細については、[オファ―の作成](.\.\azure-stack-create-offer.md)に関するページをご覧ください。 


### <a name="known-issues"></a>既知の問題
 
#### <a name="portal"></a>ポータル
- 管理者ポータルの[ドロップダウン リストから新しいサポート要求を開く](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)機能は使用できません。 代わりに、次のリンクを使用します。     
    - Azure Stack Development Kit の場合は、https://aka.ms/azurestackforum を使います。    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Azure Stack Development Kit を登録するように勧める**アクティブ化が必要**という警告アラートが表示されます。 これは正しい動作です。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- 管理ポータルのダッシュボードの [更新] タイルで、更新プログラムに関する情報を表示できません。 この問題を解決するには、そのタイルをクリックして更新してください。

-   管理ポータルに、Microsoft.Update.Admin コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、解決策が、次のようにすべて表示されます。  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

    このアラートは無視してかまいません。 



#### <a name="marketplace"></a>マーケットプレース
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。
 
#### <a name="compute"></a>コンピューティング
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

- Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

- VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

-  VM のデプロイで拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>ネットワーク
- **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定した場合は、可能な接続の種類として **[VNet 対 VNet]** が一覧に表示されます。 このオプションを選択しないでください。 現時点でサポートされているのは、**[サイト対サイト (IPsec)]** オプションのみです。

- VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。



- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。
 
- Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。



#### <a name="sql-and-mysql"></a>SQL および MySQL 
- ユーザーが新しい SQL SKU または MySQL SKU でデータベースを作成できるまでに、最大で 1 時間かかる場合があります。

- データベースをホストするサーバーは、リソース プロバイダーとユーザーのワークロード専用にする必要があります。 他のコンシューマー (App Services など) が使用中のインスタンスは使用できません。

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

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

- Azure Stack Development Kit を登録するように勧める**アクティブ化が必要**という警告アラートが表示されます。 これは正しい動作です。

- ユーザー サブスクリプションを削除すると、リソースが孤立します。 回避策として、まず、ユーザー リソースまたはリソース グループ全体を削除してから、ユーザー サブスクリプションを削除します。

- Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。 この問題を回避するには、PowerShell を使用してアクセス許可を確認します。

- 管理ポータルのダッシュボードの [更新] タイルで、更新プログラムに関する情報を表示できません。 この問題を解決するには、そのタイルをクリックして更新してください。

-   管理ポータルに、Microsoft.Update.Admin コンポーネントに関する重大なアラートが表示される場合があります。 アラート名、説明、解決策が、次のようにすべて表示されます。  
    - *エラー - FaultType ResourceProviderTimeout 用のテンプレートが見つかりません。*

    このアラートは無視してかまいません。 

- 管理ポータルとユーザー ポータルの両方で、古い API バージョン (2015-06-15 など) で作成されたストレージ アカウントの [概要] ブレードを選択した場合、[概要] ブレードの読み込みに失敗します。 

  この問題を回避するには、PowerShell を使用して **Start-ResourceSynchronization.ps1** スクリプトを実行し、ストレージ アカウントの詳細へのアクセスを復元します。 [このスクリプトは GitHub から入手可能]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)であり、ASDK を使用する場合は開発キットのホスト上でサービス管理者の資格情報を使用して実行する必要があります。  

- **[サービス正常性]** ブレードの読み込みに失敗します。 管理ポータルまたはユーザー ポータルで [サービス正常性] ブレードを開くと、Azure Stack にエラーが表示され、情報は読み込まれません。 これは正しい動作です。 [サービス正常性] を選択して開けますが、この機能はまだ利用できません。Azure Stack の今後のバージョンに実装されます。

#### <a name="health-and-monitoring"></a>正常性と監視
Azure Stack 管理ポータルに、**保留中の外部証明書の有効期限**という重大なアラートが表示される場合があります。  このアラートは無視してかまいませんが、Azure Stack Development Kit の操作に影響を与えます。 


#### <a name="marketplace"></a>マーケットプレース
- ユーザーはサブスクリプションなしですべてのマーケットプレースを参照し、プランやオファーなどの管理アイテムを表示できます。 ユーザーはこれらのアイテムを使用できません。
 
#### <a name="compute"></a>コンピューティング
- 仮想マシン スケール セットのスケーリング設定は、ポータルで使用できません。 回避策として、[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set) を使用できます。 PowerShell のバージョンの違いにより、`-VMScaleSetName` パラメーターの代わりに `-Name` を使用する必要があります。

- Azure Stack では、固定タイプ VHD の使用のみがサポートされます。 Azure Stack のマーケットプレースで提供されていた一部のイメージはダイナミック VHD を使用しますが、これらは削除されました。 ダイナミック ディスクがアタッチされている仮想マシン (VM) のサイズを変更すると、VM は失敗した状態のままになります。

  この問題を軽減するには、VM のディスク、つまりストレージ アカウントの VHD BLOB を削除せずに VM を削除します。 次に、VHD をダイナミック ディスクから固定ディスクに変換した後、仮想マシンを再作成します。

- Azure Stack ユーザー ポータルで仮想マシンを作成するとき、ポータルでは、DS シリーズ VM にアタッチできるデータ ディスクの数に誤った値が表示されます。 DS シリーズ VM は Azure の構成と同数のデータ ディスクをアタッチできます。

- VM イメージの作成に失敗した場合に、失敗したのに削除できない項目が、VM イメージのコンピューティング ブレードに追加される可能性があります。

  この問題を回避するには、Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) で作成できるダミーの VHD で新しい VM イメージを作成します。 このプロセスによって、失敗した項目の削除を妨げている問題が修正されます。 その後、ダミーのイメージを作成してから 15 分経つと、正常に削除できます。

  次に、前に失敗した VM イメージの再ダウンロードを試行できます。

-  VM のデプロイで拡張機能のプロビジョニングに時間がかかりすぎる場合、ユーザーは、プロセスを停止して VM の割り当て解除または削除を試みるのではなく、プロビジョニングをタイムアウトさせる必要があります。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>ネットワーク
- **[ネットワーク]** で **[接続]** をクリックして VPN 接続を設定した場合は、可能な接続の種類として **[VNet 対 VNet]** が一覧に表示されます。 このオプションを選択しないでください。 現時点でサポートされているのは、**[サイト対サイト (IPsec)]** オプションのみです。

- VM を作成してパブリック IP アドレスに関連付けた後は、IP アドレスからその VM の関連付けを解除することはできません。 関連付けの解除は機能したように見えますが、以前に割り当てられたパブリック IP アドレスは、元の VM に関連付けられたままになります。

  現時点では、作成した新しい VM には新しいパブリック IP アドレスのみを使用する必要があります。

  この動作は、IP アドレスを新しい VM に 再割り当てした (一般に *VIP スワップ*と呼ばれます) 場合でも行われます。 以降のこの IP アドレスによるすべての接続の試みは、新しい VM ではなく、元々関連付けられていた VM に接続する結果になります。

-   IP 転送機能がポータルに表示されますが、IP 転送を有効にしても何も起こりません。 この機能は、まだサポートされていません。

- Azure Stack では、IP アドレスごとに 1 つの "*ローカル ネットワーク ゲートウェイ*" がサポートされます。 これは、テナントのすべてのサブスクリプションに当てはまります。 最初のローカル ネットワーク ゲートウェイ接続を作成した後に、続いて同じ IP アドレスでローカル ネットワーク ゲートウェイ リソースを作成しようとすると、ブロックされます。

- "*自動*" の DNS サーバー設定を使用して作成した仮想ネットワークで、カスタム DNS サーバーに変更すると失敗します。 更新した設定は、その Vnet 内の VM にプッシュされません。
 
- Azure Stack では、VM を展開した後に、VM インスタンスにネットワーク インターフェイスをさらに追加することはできません。 VM に複数のネットワーク インターフェイスが必要な場合は、展開時に定義する必要があります。



#### <a name="sql-and-mysql"></a>SQL および MySQL 
- ユーザーが新しい SQL SKU または MySQL SKU でデータベースを作成できるまでに、最大で 1 時間かかる場合があります。

- データベースをホストするサーバーは、リソース プロバイダーとユーザーのワークロード専用にする必要があります。 他のコンシューマー (App Services など) が使用中のインスタンスは使用できません。

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

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

  この問題を解決するには、スクリプトの先頭に `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` を追加して、GitHub リポジトリからダウンロードするときに TLSv1.2 を使用するように PowerShell コンソールに強制します。

