---
title: Azure Automation State Configuration の概要
description: Azure Automation State Configuration (DSC) の概要、その利用規約、および既知の問題
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 46cf0d6a12ffbc836db7bd79c0f2738a94e23085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283193"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration の概要

Azure Automation State Configuration は、PowerShell の Desired State Configuration (DSC) [構成](/powershell/scripting/dsc/configurations/configurations)の書き込み、管理、コンパイルを実行できる Azure サービスです。 また、サービスでは、[DSC リソース](/powershell/scripting/dsc/resources/resources)のインポートと、ターゲット ノードへの構成の割り当てをすべて、クラウドで実行できます。

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用する理由

Azure Automation State Configuration には、Azure の外部で DSC を使用する際にいくつかの利点があります。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation State Configuration では、[Windows 機能 DSC サービス](/powershell/scripting/dsc/pull-server/pullserver)に似た DSC プル サーバーが提供されます。 ターゲット ノードでは、自動的に構成を受信し、目的の状態に適合させ、コンプライアンスについて報告することができます。 Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。 Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="management-of-all-your-dsc-artifacts"></a>すべての DSC アーティファクトの管理

Azure Automation State Configuration では、PowerShell スクリプト用に提供されるものと同じ管理レイヤーが、[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) に提供されます。 Azure portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ページのスクリーンショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Azure Monitor ログへのレポート データのインポート

Azure Automation State Configuration で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。 Azure Automation State Configuration を構成して、このデータを Log Analytics ワークスペースに送信できます。 「[Azure Monitor ログへの Azure Automation State Configuration レポート データの転送](automation-dsc-diagnostics.md)」を参照してください。

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用するための前提条件

DSC に対して Azure Automation State Configuration を使用する場合は、次の要件を検討してください。

### <a name="operating-system-requirements"></a>オペレーティング システムの要件

Windows を実行しているノードの場合は、次のバージョンがサポートされます。

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) スタンドアロン製品 SKU には、DSC の実装は含まれていません。 したがって、PowerShell DSC または Azure Automation State Configuration で管理することはできません。

Linux が実行されているノードの場合、DSC Linux 拡張機能では、「[サポートされている Linux ディストリビューション](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)」の一覧で示されている Linux ディストリビューションがすべてサポートされます。

### <a name="dsc-requirements"></a>DSC 要件

Azure で実行されているすべての Windows ノードに対して、[WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) がオンボード中にインストールされます。 Windows Server 2012 および Windows 7 を実行しているノードでは、[WinRM が有効になります](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

Azure で実行しているすべての Linux ノードで、オンボード中に [Linux 用の PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされます。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>プライベート ネットワークの構成

ノードがプライベート ネットワークに配置されている場合は、次のポートと URL が必要です。 これらのリソースにより、マネージド ノードに対するネットワーク接続が提供され、DSC で Azure Automation と通信できます。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: * **.azure-automation.net**
* US Gov バージニアのグローバル URL: * **.azure automation.us**
* エージェント サービス: **https://\<workspaceId\>.agentsvc.azure-automation.net**

ノード間で通信する DSC リソース ([WaitFor * リソース](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)など) を使用している場合、ノード間のトラフィックを許可する必要もあります。 これらのネットワーク要件を理解するには、各 DSC リソースのドキュメントを参照してください。

#### <a name="proxy-support"></a>プロキシのサポート

DSC エージェントのプロキシは、Windows バージョン 1809 以降でサポートされます。 このオプションを有効にするには、ノードの登録に使用される[メタ構成スクリプト](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)で `ProxyURL` と `ProxyCredential` の値を設定します。

>[!NOTE]
>Azure Automation State Configuration では、以前のバージョンの Windows に対する DSC プロキシ サポートは提供されません。

Linux ノードでは、DSC エージェントによってプロキシがサポートされ、`http_proxy` 変数を使用して URL が特定されます。

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration のネットワークの範囲と名前空間

例外を定義するときは、次の一覧に示されているアドレスを使用することをお勧めします。 IP アドレスについては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの通信を制限できます。 次の表は、リージョンごとの DNS レコードを示しています。

| **リージョン** | **DNS レコード** |
| --- | --- |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 米国東部    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) XML ファイルをダウンロードします。

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。
>
>更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 

Azure ExpressRoute ユーザーは、このファイルが、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために使用されることに注意してください。

## <a name="next-steps"></a>次のステップ

- Azure Automation State Configuration で DSC の使用を始めるには、「[Azure Automation State Configuration の使用開始](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
