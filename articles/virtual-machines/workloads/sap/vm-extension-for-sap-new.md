---
title: SAP ソリューション用 Azure VM 拡張機能の新しいバージョン | Microsoft Docs
description: 新しいバージョンの SAP 用 VM 拡張機能をデプロイする方法について説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: ce34b313661106e903a92aaf1a3b2f65213a1634
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577271"
---
# <a name="new-version-of-azure-vm-extension-for-sap-solutions"></a>SAP ソリューション用 Azure VM 拡張機能の新しいバージョン 
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[std-extension]:vm-extension-for-sap-standard.md (SAP ソリューション用 Azure VM 拡張機能の標準バージョン)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (PowerShell を使用して新しい SAP ソリューション用 Azure VM 拡張機能を構成する)
[troubleshoot-windows]:vm-extension-for-sap-new.md#dee9099b-7b8a-4cdd-86a2-3f6ee964266f (Windows でのトラブルシューティング)
[troubleshoot-linux]:vm-extension-for-sap-new.md#02783aa4-5443-43f5-bc11-7af19ebf0c36 (Linux でのトラブルシューティング)
[deployment-guide-4.1]:vm-extension-for-sap-new.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell コマンドレットのデプロイ)
[azure-cli-2]:/cli/azure/install-azure-cli
[configure-linux]:vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481 (Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する)
[configure-windows]:vm-extension-for-sap-new.md#a876ee7b-43b4-4782-aa5f-73753b6af0ea (PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する)
[health-check]:vm-extension-for-sap-new.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (正常性チェック)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[readiness-check]:vm-extension-for-sap-new.md#5774c1db-1d3c-4b34-8448-3afd0b0f18ab (適合性チェック)

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> 一般的なサポートに関する声明: Azure Extension for SAP のサポートは、SAP のサポート チャネルを通じて提供されます。
> SAP ソリューション用 Azure VM 拡張機能に関するサポートが必要な場合は、SAP サポートでサポート ケースを開いてください
  
> [!NOTE]
> Azure Extension for SAP の標準バージョンと新しいバージョンを切り替える前に、必ず VM 拡張機能をアンインストールしてください。

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この記事では、**新しい** バージョンの SAP 用 Azure VM 拡張機能について説明します。 標準バージョンのインストール方法に関するガイダンスについては、「[SAP ソリューション用 Azure VM 拡張機能の標準バージョン][std-extension]」を参照してください。

* 必ず SAP Host Agent 7.21 PL 47 以上を使用してください。
* 拡張機能が有効になっている仮想マシンが management.azure.com にアクセスできることを確認します。

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell コマンドレットのデプロイ

[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関する記事で説明されている手順に従ってください

PowerShell コマンドレットの更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。 [こちら](/powershell/azure/install-az-ps#update-the-azure-powershell-module)の記事で説明されている手順に従ってください。 SAP Note [1928533] または [2015553] に特に記載されていない限り、最新バージョンの Azure PowerShell コマンドレットを使用することをお勧めします。

コンピューターにインストールされている Azure PowerShell コマンドレットのバージョンを確認するには、次の PowerShell コマンドを実行します。

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI のデプロイ

記事「[Azure CLI のインストール](/cli/azure/install-azure-cli)」で説明されている手順に従ってください

Azure CLI の更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。

コンピューターにインストールされている Azure CLI のバージョンを確認するには、次のコマンドを実行します。

```console
az --version
```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="a876ee7b-43b4-4782-aa5f-73753b6af0ea"></a>PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する
 
SAP 用の新しい VM 拡張機能では、VM の監視と構成のデータにアクセスするために、VM に割り当てられたマネージド ID が使用されます。 PowerShell を使用して新しい Azure Extension for SAP をインストールするには、まずそのような ID を VM に割り当ててから、その VM で使用されているすべてのリソース (ディスクやネットワーク インターフェイスなど) に対するアクセス権をその ID に付与する必要があります。

> [!NOTE]
> 次の手順では、リソース グループまたは個々のリソース (仮想マシン、データ ディスク、ネットワーク インターフェイス) に対する所有者特権が必要です。

1. 必ず SAP Host Agent 7.21 PL 47 以上を使用してください。
1. 必ず標準バージョンの SAP 用 VM 拡張機能をアンインストールしてください。 同じ仮想マシンに両方のバージョンの SAP 用 VM 拡張機能をインストールすることはサポートされていません。
1. 最新バージョンの Azure PowerShell コマンドレット (4.3.0 以上) がインストールされていることを確認します。 詳細については、「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」をご覧ください。
1. 次の PowerShell コマンドレットを実行します。
    使用可能な環境の一覧を表示するには、コマンドレット `Get-AzEnvironment` を実行します。 グローバル Azure を使う場合の環境は **AzureCloud** です。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

    VM Extension for SAP では、拡張機能が外部リソース (Azure Resource Manager API など) への接続に使用するプロキシの構成がサポートされています プロキシを設定するには、パラメーター -ProxyURI を使用してください。

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```
 
## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="fa4428b9-bed6-459a-9dfb-74cc27454481"></a>Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する
 
SAP 用の新しい VM 拡張機能では、VM の監視と構成データにアクセスするために、VM に割り当てられたマネージド ID が使用されます。

> [!NOTE]
> 次の手順では、リソース グループまたは個々のリソース (仮想マシン、データ ディスクなど) に対する所有者特権が必要です。

1. SAP Host Agent 7.21 PL 47 以降を使用していることを確認します。
1. 現在のバージョンの SAP 用 VM 拡張機能をアンインストールしていることを確認します。 同じ VM に両方のバージョンの SAP 用 VM 拡張機能をインストールすることはできません。 
1. 最新バージョンである [Azure CLI 2.0][azure-cli-2] (バージョン 2.19.1 以降) をインストールします。
1. Azure アカウントでサインインします。

   ```azurecli
   az login
   ```

1. Azure CLI AEM Extension をインストールします。 バージョン 0.2.2 以降を使用していることを確認してください。
  
   ```azurecli
   az extension add --name aem
   ```
  
1. 新しい拡張機能の有効化:
  
   VM Extension for SAP では、拡張機能が外部リソース (Azure Resource Manager API など) への接続に使用するプロキシの構成がサポートされています プロキシを設定するには、パラメーター --proxy-uri を使用してください。

   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```
 
 
## <a name="readiness-check"></a><a name="5774c1db-1d3c-4b34-8448-3afd0b0f18ab"></a>移行対応性チェック

このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる Azure Extension for SAP によって提供されていることを確認します。

### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM での適合性チェックの実行

1. Azure 仮想マシンにサインインします (管理者アカウントを使用する必要はありません)。
1. Web ブラウザーを開き、 http://127.0.0.1:11812/azure4sap/metrics にアクセスします
1. ブラウザーによって、仮想マシンの監視データを含む XML ファイルが表示またはダウンロードされるはずです。 そうでない場合は、Azure Extension for SAP がインストールされていることを確認してください。
1. XML ファイルの内容を確認します。 http://127.0.0.1:11812/azure4sap/metrics でアクセスできる XML ファイルには、SAP 用に設定されたすべての Azure パフォーマンス カウンターが含まれています。 また、Azure Extension for SAP の状態の概要と正常性インジケーターも含まれています。
1. **Provider Health Description** 要素の値を確認します。 値が **[OK]** ではない場合は、「[正常性チェック][health-check]」の章の手順に従います。
 
### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM での適合性チェックの実行

1. SSH を使用して Azure 仮想マシンに接続します。
1. 次のコマンドの出力を確認します
   ```bash
   curl http://127.0.0.1:11812/azure4sap/metrics
   ```
   **予測される結果**: 仮想マシン、そのディスク、およびネットワーク インターフェイスの監視情報を含む XML ドキュメントが返されます。
   1. SSH を使用して Azure 仮想マシンに接続します。

1. 次のコマンドの出力を確認します

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **予測される結果**: 仮想マシン、そのディスク、およびネットワーク インターフェイスの監視情報を含む XML ドキュメントが返されます。

前のチェックが失敗した場合は、次の追加チェックを実行します。

1. waagent がインストールされ、有効になっていることを確認します。

   a.  `sudo ls -al /var/lib/waagent/` を実行します。

     **予測される結果**: waagent ディレクトリの内容を一覧表示します。

   b.  `ps -ax | grep waagent` を実行します。

   **予測される結果**: `python /usr/sbin/waagent -daemon` のような 1 つのエントリを表示します。

1. Azure Extension for SAP がインストールされ、実行されていることを確認します。

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'` を実行します。

   **予測される結果**: Azure Extension for SAP ディレクトリの内容が一覧表示されます。

   b. `ps -ax | grep AzureEnhanced` を実行します。

   **予測される結果**: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor` のような 1 つのエントリを表示します。

1. SAP Note [1031096] の説明に従って SAP Host Agent をインストールし、`saposcol` の出力を確認します。

   a.  `/usr/sap/hostctrl/exe/saposcol -d` を実行します。

   b.  `dump ccm` を実行します。

   c.  **Virtualization_Configuration\Enhanced Monitoring Access** メトリックが **true** であるかどうかを確認します。

既に SAP NetWeaver ABAP アプリケーション サーバーをインストール済みである場合は、トランザクション ST06 を開き、監視が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合、また拡張機能を再デプロイする方法の詳細については、「[Windows でのトラブルシューティング][troubleshoot-windows]」または「[Linux でのトラブルシューティング][troubleshoot-linux]」をご覧ください。
 
## <a name="health-checks"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>正常性チェック

一部のインフラストラクチャ データが、「[適合性チェック][readiness-check]」で説明したテストで示されているように正しく提供されない場合は、この章に記載されている正常性チェックを実行して、Azure インフラストラクチャと Azure Extension for SAP が正しく構成されているかどうかを確認します。

### <a name="health-checks-using-powershell"></a>PowerShell を使用した正常性チェック

1. 「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」で説明したように、Azure PowerShell コマンドレットの最新バージョンがインストールされていることを確認します。
1. 次の PowerShell コマンドレットを実行します。 使用可能な環境のリストを表示するには、`Get-AzEnvironment` コマンドレットを実行します。 グローバル Azure を使う場合は、**AzureCloud** 環境を選びます。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```
1. 選択した仮想マシンの構成がスクリプトによってテストされます。

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **[OK]** が表示されない場合は、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、更新コマンドレットを実行します。 「[適合性チェック][readiness-check]」とこの章で説明されているチェックを繰り返します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」をご覧ください。

### <a name="health-checks-using-azure-cli"></a>Azure CLI を使用した正常性チェック

Azure CLI を使用して SAP 用 Azure VM 拡張機能の正常性チェックを実行します。
 
1. [Azure CLI 2.0][azure-cli-2] をインストールします。 バージョン 2.19.1 以降を使用していることを確認します (最新バージョンを使用してください)。 
1. Azure アカウントでサインインします。
   ```azurecli
   az login
   ```

1. Azure CLI AEM Extension をインストールします。 バージョン 0.2.2 以降を使用していることを確認してください。
   ```azurecli
   az extension add --name aem
   ```

1. 拡張機能がインストールされていることを確認します。 
   ```azurecli
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
選択した仮想マシンの構成がスクリプトによってテストされます。

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **[OK]** が表示されない場合は、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、更新コマンドレットを実行します。 「[適合性チェック][readiness-check]」とこの章で説明されているチェックを繰り返します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」をご覧ください。


## <a name="troubleshooting-for-windows"></a><a name="dee9099b-7b8a-4cdd-86a2-3f6ee964266f"></a>Windows でのトラブルシューティング
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring プロセスによって収集されます。 VM でこのプロセスが実行されていない場合、パフォーマンス メトリックを収集することはできません。

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である
##### <a name="issue"></a>問題
インストール ディレクトリ C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;バージョン> が空です。
##### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 場合によっては、マシンを再起動するか、VM 拡張機能をもう一度インストールする必要があります。
 
### <a name="some-azure-performance-counters-are-missing"></a>一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows プロセスによって収集されます。 このプロセスでは、複数のソースからデータが取得されます。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Monitor から読み取られます。

SAP Note [1999351] を使用してトラブルシューティングを行っても問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。 ログ ファイル C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;バージョン>\\logapp.txt をインシデントに添付してください。

## <a name="troubleshooting-for-linux"></a><a name="02783aa4-5443-43f5-bc11-7af19ebf0c36"></a>Linux でのトラブルシューティング

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、デーモンによって収集されます。 デーモンが実行されていない場合、パフォーマンス メトリックを収集できません。

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である
##### <a name="issue"></a>問題
/var/lib/waagent/ ディレクトリに、Azure Extension for SAP のサブディレクトリがありません。
##### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 場合によっては、マシンを再起動するか、VM 拡張機能をもう一度インストールする必要があります。
 
### <a name="some-azure-performance-counters-are-missing"></a>一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。デーモンは複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Monitor から読み取られます。
既知の問題の最新のリストについては、SAP Note [1999351] を参照してください。この SAP Note には、Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
SAP Note [1999351] を使用してトラブルシューティングを行っても問題が解決しない場合は、「[Azure Extension for SAP を構成する][configure-windows]」の説明に従って、拡張機能をもう一度インストールしてください。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。 ログ ファイル /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;バージョン>/logapp.txt をインシデントに添付してください。


## <a name="azure-extension-error-codes"></a>Azure 拡張機能のエラー コード
 
エラー ID はすべて a-# の形式の一意のタグを含んでいます。ここで、# は数字です。 これにより、特定のエラーと考えられる解決策を迅速に検索できます。
 
| エラー ID | エラーの説明 | 解決策 |
|---|---|---|
| `a-0116` | 認証トークンなし | 詳細情報: <br />拡張機能は、Azure Monitor で VM メトリックにアクセスするための認証トークンを取得できません。 VM メトリックを配信するには、VM 自体、VM に接続されているすべてのディスクとすべての NIC など、VM リソースへのアクセスが必要です<br />解決方法:<br />VM マネージド ID を有効にして、VM リソース グループの閲覧者ロールを割り当ててください。 セットアップ スクリプトを使用すると、スクリプトによって割り当てが実行されます。 通常、VM マネージド ID を手動で有効にして割り当てる必要はありません。 |

## <a name="next-steps"></a>次のステップ
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ](./deployment-guide.md)
* [SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)
