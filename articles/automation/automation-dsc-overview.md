---
title: Azure Automation State Configuration の概要
description: この記事では、Azure Automation State Configuration (DSC) の概要、その利用規約、および既知の問題について説明します。
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406176"
---
# <a name="state-configuration-overview"></a>状態の構成の概要

Azure Automation State Configuration は、PowerShell の Desired State Configuration (DSC) [構成](/powershell/scripting/dsc/configurations/configurations)の書き込み、管理、コンパイルを実行できる Azure サービスです。 また、サービスでは、[DSC リソース](/powershell/scripting/dsc/resources/resources)のインポートと、ターゲット ノードへの構成の割り当てがすべて、クラウドで行われます。

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用する理由

Azure Automation State Configuration には、Azure の外部で DSC を使用する際にいくつかの利点があります。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation State Configuration では、[Windows 機能 DSC サービス](/powershell/scripting/dsc/pull-server/pullserver)に似た DSC プル サーバーが提供されます。 ターゲット ノードでは、自動的に構成を受信し、目的の状態に適合させ、コンプライアンスについて報告することができます。 Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。 Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="manage-all-your-dsc-artifacts"></a>DSC のすべての成果物を管理する

Azure Automation State Configuration では、PowerShell スクリプト用に提供されるものと同じ管理レイヤーが、[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) に提供されます。 Azure portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ページのスクリーンショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Azure Monitor ログにレポート データをインポートする

Azure Automation State Configuration で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。 Azure Automation State Configuration を構成して、このデータを Log Analytics ワークスペースに送信できます。 詳しくは、「[Azure Monitor ログへの Azure Automation State Configuration レポート データの転送](automation-dsc-diagnostics.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

DSC に対して Azure Automation State Configuration を使用する場合は、次の要件を検討します。

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
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) のスタンドアロン製品 SKU には DSC の実装が含まれていないため、PowerShell DSC または Azure Automation State Configuration で管理することはできません。

Linux が実行されているノードの場合、DSC Linux 拡張機能では、「[サポートされている Linux ディストリビューション](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)」の一覧で示されている Linux ディストリビューションがすべてサポートされます。

### <a name="dsc-requirements"></a>DSC 要件

Azure で実行されているすべての Windows ノードに対して、[Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) がオンボード中にインストールされます。 Windows Server 2012 および Windows 7 を実行しているノードでは、[WinRM が有効になります](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

Azure で実行しているすべての Linux ノードで、オンボード中に [Linux 用の PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされます。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>プライベート ネットワークの構成

ノードがプライベート ネットワークに配置されている場合は、次のポートと URL が必要です。 これらのリソースにより、マネージド ノードに対するネットワーク接続が提供され、DSC で Azure Automation と通信できます。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: * **.azure-automation.net**
* US Gov バージニアのグローバル URL: * **.azure automation.us**
* エージェント サービス: **https://\<workspaceId\>.agentsvc.azure-automation.net**

ノード間で通信する DSC リソース ([WaitFor* リソース](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)など) を使用している場合、ノード間の通信を許可する必要もあります。 これらのネットワーク要件を理解するには、各 DSC リソースのドキュメントを参照してください。

#### <a name="proxy-support"></a>プロキシのサポート

DSC エージェントのプロキシは、Windows バージョン 1809 以降でサポートされます。 このオプションを有効にするには、ノードの登録に使用される[メタ構成スクリプト](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)で `ProxyURL` と `ProxyCredential` の値を設定します。

>[!NOTE]
>Azure Automation State Configuration では、以前のバージョンの Windows に対する DSC プロキシ サポートは提供されません。

Linux ノードでは、DSC エージェントによってプロキシ サーバーがサポートされ、`http_proxy` 変数を使用して URL が指定されます。

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration のネットワークの範囲と名前空間

例外を定義するときは、次の表に示す IP アドレスを使用することをお勧めします。 IP アドレスについては、[Microsoft Azure データセンター IP アドレス範囲](https://www.microsoft.com/download/details.aspx?id=41653)の XML ファイルを Microsoft ダウンロード センターからダウンロードできます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP アドレス範囲の変更が含まれます。 これは毎週更新されます。

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

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。
>
>更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 そうすれば、Azure で実行されているサービスを正しく識別するようにサイトを更新できます。 

Azure ExpressRoute を使用している場合は、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために、このファイルが使用されることにご注意ください。

## <a name="next-steps"></a>次のステップ

- Azure Automation State Configuration で DSC の使用を始めるには、「[Azure Automation State Configuration の使用を開始する](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration で構成をコンパイルする](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的デプロイ パイプラインでの Azure Automation State Configuration の使用例については、[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)に関するページをご覧ください。
