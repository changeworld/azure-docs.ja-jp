---
title: SAP ソリューション用 Azure VM 拡張機能の標準バージョン | Microsoft Docs
description: 標準バージョンの SAP 用 VM 拡張機能をデプロイする方法について説明します。
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
ms.openlocfilehash: 8e723e39364207e1075d66eeafcdf12b74e16624
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261388"
---
# <a name="standard-version-of-azure-vm-extension-for-sap-solutions"></a>SAP ソリューション用 Azure VM 拡張機能の標準バージョン
[new-extension]:vm-extension-for-sap-new.md (SAP ソリューション用 Azure VM 拡張機能の新しいバージョン)
[configure-windows]:vm-extension-for-sap-standard.md#cb095b34-51c9-41f3-aeed-30a16072a1dc (PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する)
[configure-linux]:vm-extension-for-sap-standard.md#c691e304-3524-4bfd-8612-992d5715a689 (Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する)
[configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[troubleshoot-windows]:vm-extension-for-sap-standard.md#f566b77e-5427-451b-b4ca-6b93114d51e8 (Windows でのトラブルシューティング)
[troubleshoot-linux]:vm-extension-for-sap-standard.md#a4dae567-e7fd-4d4b-8279-510b8e5fae4a (Linux でのトラブルシューティング)
[healthcheck]:vm-extension-for-sap-standard.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Azure Extension for SAP の構成の正常性チェック)
[deployment-guide-4.1]:vm-extension-for-sap-standard.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell コマンドレットのデプロイ)
[deploy-cli]:vm-extension-for-sap-standard.md#1ded9453-1330-442a-86ea-e0fd8ae8cab3 (Azure CLI のデプロイ)
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[azure-cli-2]:/cli/azure/install-azure-cli
[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[readiness-check]:vm-extension-for-sap-standard.md#6b2561a0-b476-4579-847d-6c68117eb26e (適合性チェック)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP リソース)
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[deployment-guide-contact-support]:vm-extension-for-sap-standard.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Azure Extension for SAP のトラブルシューティング - サポートに問い合わせる)
[deployment-guide-run-the-script]:vm-extension-for-sap-standard.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Azure Extension for SAP のトラブルシューティング - セットアップ スクリプトを実行する)
[deployment-guide-redeploy-after-sysprep]:vm-extension-for-sap-standard.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Azure Extension for SAP のトラブルシューティング - sysprep 後に再デプロイする)
[deployment-guide-fix-internet-connection]:vm-extension-for-sap-standard.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 (Azure Extension for SAP のトラブルシューティング - インターネット接続を修正する)



## <a name="prerequisites"></a>前提条件

> [!NOTE]
> 一般的なサポートに関する声明: Azure Extension for SAP のサポートは、SAP のサポート チャネルを通じて提供されます。
> SAP ソリューション用 Azure VM 拡張機能に関するサポートが必要な場合は、SAP サポートでサポート ケースを開いてください
  
> [!NOTE]
> Azure Extension for SAP の標準バージョンと新しいバージョンを切り替える前に、必ず VM 拡張機能をアンインストールしてください。

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この記事では、**標準** バージョンの SAP 用 Azure VM 拡張機能について説明します。 新しいバージョンのインストール方法に関するガイダンスについては、「[SAP ソリューション用 Azure VM 拡張機能の新しいバージョン][new-extension]」を参照してください。


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


## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-powershell"></a><a name="cb095b34-51c9-41f3-aeed-30a16072a1dc"></a>PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する 
 
PowerShell を使用して Azure Extension for SAP をインストールするには:

1. 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認します。 詳細については、「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」をご覧ください。
1. 次の PowerShell コマンドレットを実行します。 使用可能な環境の一覧を表示するには、コマンドレット `Get-AzEnvironment` を実行します。 グローバル Azure を使う場合の環境は **AzureCloud** です。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。 
   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   
   Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> 
   ``` 
アカウント データを入力すると、スクリプトによって必要な拡張機能がデプロイされ、必要な機能が有効化されます。 これには数分かかる場合があります。
`Set-AzVMAEMExtension` の詳細については、「[Set-AzVMAEMExtension][msdn-set-Azvmaemextension]」を参照してください。

![SAP 固有の Azure コマンドレット Set-AzVMAEMExtension の正常な実行][deployment-guide-figure-900]

`Set-AzVMAEMExtension` の構成では、SAP 用のホスト データ収集を構成するためのすべての手順が実行されます。

スクリプトの出力には次の情報が含まれています。

* OS ディスクとすべての追加データ ディスクのデータ収集が構成されていることの確認。
* 次の 2 つのメッセージで、特定のストレージ アカウントのストレージ メトリックの構成が確認されます。
* 出力の 1 つの行に、VM Extension for SAP の構成に関する実際の更新の状態が示されます。
* 出力の別の行で、構成がデプロイまたは更新されたことが確認されます。
* 出力の最後の行は情報提供です。 この行には、VM Extension for SAP の構成をテストするためのオプションが示されます。
* SAP 用 Azure VM 拡張機能のすべての手順が正常に実行されたこと、および Azure インフラストラクチャから必要なデータが提供されていることを確認するには、「[適合性チェック][readiness-check]」の説明に従って、Azure Extension for SAP の適合性チェックを実行します。
* Azure Diagnostics が関連データを収集するまで 15 ～ 30 分待ちます。

## <a name="configure-the-azure-vm-extension-for-sap-solutions-with-azure-cli"></a><a name="c691e304-3524-4bfd-8612-992d5715a689"></a>Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する
 
Azure CLI を使用して SAP 用 Azure VM 拡張機能をインストールするには:

1. 最新バージョンの Azure CLI がインストールされていることを確認します。 詳細については、「[Azure CLI のデプロイ][deploy-cli]」を参照してください。 

1. Azure アカウントでサインインします。
   ```azurecli
   az login
   ```

1. Azure CLI AEM Extension をインストールします。 バージョン 0.2.2 以降を使用していることを確認してください。
   ```azurecli
   az extension add --name aem
   ```

1. 拡張機能の有効化: 
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> 
   ```

1. Azure Extension for SAP が Azure Linux VM 上で有効になっていることを確認します。 /var/lib/AzureEnhancedMonitor/PerfCounters ファイルが存在するかどうかを確認します。 存在する場合は、コマンド プロンプトで次のコマンドを実行して、Azure Extension for SAP によって収集された情報を表示します。

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   出力は次のようになります。

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Azure Extension for SAP の構成を更新する

次のいずれかのシナリオで、Azure Extension for SAP の構成を更新します。
* Microsoft と SAP の共同チームによって VM 拡張機能が拡張され、カウンターの増減が要求されている場合。
* Microsoft によって、データを提供する基になる Azure インフラストラクチャの新しいバージョンが導入され、Azure Extension for SAP を変更に適応させる必要がある場合。
* Azure VM に追加のデータ ディスクをマウントするか、データ ディスクを削除します。 このシナリオでは、ストレージ関連のデータのコレクションを更新します。 エンドポイントを追加または削除することで、または VM に IP アドレスを割り当てることで構成を変更しても、拡張機能の構成には影響しません。
* A5 から他の VM サイズへの変更など、Azure VM のサイズを変更した場合。
* Azure VM に新しいネットワーク インターフェイスを追加した場合。

設定を更新するには、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」、または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の手順に従って、Azure Extension for SAP の構成を更新します。

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>確認とトラブルシューティング

Azure VM をデプロイし、関連する Azure Extension for SAP を設定した後は、拡張機能のすべてのコンポーネントが意図したとおりに機能していることを確認します。

「[適合性チェック][readiness-check]」の説明に従って、Azure Extension for SAP の適合性チェックを実行します。 適合性チェックのすべての結果が良好であり、関連するすべてのパフォーマンス カウンターで OK と示されていれば、Azure Extension for SAP は正常に設定されています。 この場合、「[SAP リソース][deployment-guide-2.2]」に示されている SAP Note に記載された SAP Host Agent のインストールに進むことができます。 適合性チェックの結果に、カウンターが見つからないことが示されている場合は、「[Azure Extension for SAP の構成の正常性チェック][healthcheck]」の説明に従って、Azure Extension for SAP の正常性チェックを実行します。 トラブルシューティング オプションの詳細については、「[Windows でのトラブルシューティング][troubleshoot-windows]」または「[Linux でのトラブルシューティング][troubleshoot-linux]」をご覧ください。

### <a name="readiness-check"></a><a name="6b2561a0-b476-4579-847d-6c68117eb26e"></a>移行対応性チェック

このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる Azure Extension for SAP によって提供されていることを確認します。

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM での適合性チェックの実行
 
1. Azure 仮想マシンにサインインします (管理者アカウントを使用する必要はありません)。
1. コマンド プロンプト ウィンドウを開きます。
1. コマンド プロンプトで、Azure Extension for SAP のインストール フォルダー (C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;バージョン>\\drop) にディレクトリを変更します。

   拡張機能のパスに含まれる "*バージョン*" は異なる場合があります。 インストール フォルダーに拡張機能の複数のバージョンのフォルダーがある場合は、AzureEnhancedMonitoring Windows サービスの構成を確認し、"*実行可能ファイルへのパス*" として示されているフォルダーに切り替えます。

   ![Azure Extension for SAP が実行されているサービスのプロパティ][deployment-guide-figure-1000]

1. コマンド プロンプトで、パラメーターを指定せずに **azperflib.exe** を実行します。

   > [!NOTE]
   > azperflib.exe はループで実行され、収集したカウンターを 60 秒ごとに更新します。 ループを終了するには、コマンド プロンプト ウィンドウを閉じます。
   >
   >

Azure Extension for SAP がインストールされていない場合、または AzureEnhancedMonitoring サービスが実行されていない場合は、拡張機能が正しく構成されていません。 拡張機能のトラブルシューティング方法の詳細については、「[Windows でのトラブルシューティング][troubleshoot-windows]」または「[Linux でのトラブルシューティング][troubleshoot-linux]」をご覧ください。

> [!NOTE]
> Azperflib.exe は、独自の目的には使用できないコンポーネントです。 そのコンポーネントでは、SAP Host Agent 用の VM だけに関連する Azure インフラストラクチャ データが提供されます。
> 
##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe の出力の確認
 
azperflib.exe の出力には、SAP 用に設定されたすべての Azure パフォーマンス カウンターが表示されます。 収集されたカウンターのリストの下部に表示される概要と正常性インジケーターでは、Azure Extension for SAP の状態が示されています。

![azperflib.exe の実行による正常性チェックの、問題がないことを示す出力][deployment-guide-figure-1100]
<a name="figure-11"></a>

上記の図に示されている **Counters total** の返された結果 ("empty" と報告されているもの) と、**Health status** の返された結果を確認します。

結果の値は次のように解釈します。

| azperflib.exe の結果の値 | Azure Extension for SAP の正常性状態 |
| --- | --- |
| **API Calls - not available (API 呼び出し － 使用不可)** | "not available" であるカウンターは、仮想マシン構成に適用できないか、エラーであると考えられます。 「**Health status**」を参照してください。 |
| **Counters total - empty** |次の 2 つの Azure Storage カウンターは空の場合があります。 <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>他のすべてのカウンターには値が含まれている必要があります。 |
| **Health status** |リターン状態が **OK** の場合にのみ、"OK" と表示されます。 |
| **診断** |正常性状態に関する詳細情報。 |

**正常性状態** の値が **[OK]** ではない場合は、「[Azure Extension for SAP の構成の正常性チェック][healthcheck]」の手順に従います。
 
##### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM での適合性チェックの実行

1. SSH を使用して Azure 仮想マシンに接続します。
1. Azure Extension for SAP の出力を確認します。

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters` を実行します。

   **予測される結果**: パフォーマンス カウンターの一覧を返します。 ファイルを空にすることはできません。

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` を実行します。

   **予測される結果**: エラーが **none** である 1 行 (**3;config;Error;;0;0;none;0;1456416792;tst-servercs;** など) を返します。

   c. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` を実行します。

   **予測される結果**: 空または存在しないとして返します。

前のチェックが失敗した場合は、次の追加チェックを実行します。

1. waagent がインストールされ、有効になっていることを確認します。

   a.  `sudo ls -al /var/lib/waagent/` を実行します。

     **予測される結果**: waagent ディレクトリの内容を一覧表示します。

   b.  `ps -ax | grep waagent` を実行します。

   **予測される結果**: `python /usr/sbin/waagent -daemon` のような 1 つのエントリを表示します。

1. Azure Extension for SAP がインストールされ、実行されていることを確認します。

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` を実行します。

   **予測される結果**: Azure Extension for SAP ディレクトリの内容が一覧表示されます。

   b. `ps -ax | grep AzureEnhanced` を実行します。

   **予測される結果**: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon` のような 1 つのエントリを表示します。

1. SAP Note [1031096] の説明に従って SAP Host Agent をインストールし、`saposcol` の出力を確認します。

   a.  `/usr/sap/hostctrl/exe/saposcol -d` を実行します。

   b.  `dump ccm` を実行します。

   c.  **Virtualization_Configuration\Enhanced Monitoring Access** メトリックが **true** であるかどうかを確認します。

既に SAP NetWeaver ABAP アプリケーション サーバーをインストール済みである場合は、トランザクション ST06 を開き、監視が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合、また拡張機能を再デプロイする方法の詳細については、「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」をご覧ください。

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

   ![Azure Extension for SAP のテストが成功した場合の出力][deployment-guide-figure-1300]

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **[OK]** が表示されない場合は、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、更新コマンドレットを実行します。 15 分待ってから、「[適合性チェック][readiness-check]」とこの章で説明されているチェックを繰り返します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」をご覧ください。

> [!Note]
> Azure Standard Managed Disks を使用している場合、警告が発生する可能性があります。 テストから "OK" が返る代わりに、警告が表示されます。 これは正常な動作であり、このディスク タイプの場合は意図されたものです。 「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」も参照してください。
>

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
   ```azurecliw
   az vm aem verify -g <resource-group-name> -n <vm name> 
   ```
選択した仮想マシンの構成がスクリプトによってテストされます。

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **[OK]** が表示されない場合は、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、更新コマンドレットを実行します。 15 分待ってから、「[適合性チェック][readiness-check]」とこの章で説明されているチェックを繰り返します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[Linux でのトラブルシューティング][troubleshoot-linux]」または「[Windows でのトラブルシューティング][troubleshoot-windows]」をご覧ください。

## <a name="troubleshooting-for-windows"></a><a name="f566b77e-5427-451b-b4ca-6b93114d51e8"></a>Windows でのトラブルシューティング
 
### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 サービスが正しくインストールされていない場合、または VM で実行されていない場合は、パフォーマンス メトリックを収集できません。
 
#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である

##### <a name="issue"></a>問題
インストール ディレクトリ C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\<version>\drop が空です。

##### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

#### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Azure Extension for SAP のサービスが存在しない

##### <a name="issue"></a>問題

AzureEnhancedMonitoring Windows サービスが存在しません。

azperflib.exe の出力で次のエラーがスローされます。

![Azure Extension for SAP のサービスが実行されていないことを示す azperflib.exe の実行][deployment-guide-figure-1400]
<a name="figure-14"></a>
 
##### <a name="solution"></a>解決策

サービスが存在しない場合、Azure Extension for SAP が正しくインストールされていません。 「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、拡張機能を再デプロイします。

拡張機能をデプロイしたら、Azure VM 内で Azure パフォーマンス カウンターが提供されているかどうかをもう一度確認します。

#### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Azure Extension for SAP のサービスは存在するが、起動に失敗する
##### <a name="issue"></a>問題
AzureEnhancedMonitoring Windows サービスが存在し、有効になっていますが、起動に失敗します。 詳細については、アプリケーション イベント ログを確認してください。
##### <a name="solution"></a>解決策
構成が正しくありません。 「Azure Extension for SAP を構成する」の説明に従って、VM で Azure Extension for SAP を再起動します。
 
### <a name="some-azure-performance-counters-are-missing"></a>一部の Azure パフォーマンス カウンターが見つからない
Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 このサービスは、複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Diagnostics から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログ記録から使用されます。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、`Set-AzVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

## <a name="troubleshooting-for-linux"></a><a name="a4dae567-e7fd-4d4b-8279-510b8e5fae4a"></a>Linux でのトラブルシューティング

### <a name="azure-performance-counters-do-not-show-up-at-all"></a>Azure パフォーマンス カウンターまったく表示されない
Azure のパフォーマンス メトリックは、デーモンによって収集されます。 デーモンが実行されていない場合、パフォーマンス メトリックを収集できません。

#### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である
##### <a name="issue"></a>問題
\\var\\lib\\waagent\\ ディレクトリに、Azure Extension for SAP のサブディレクトリがありません。
##### <a name="solution"></a>解決策
拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

#### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Set-AzVMAEMExtension および Test-AzVMAEMExtension を実行すると、Standard Managed Disks はサポートされていないことを示す警告メッセージが表示されます。
##### <a name="issue"></a>問題
Set-AzVMAEMExtension または Test-AzVMAEMExtension を実行すると、次のようなメッセージが表示されます。

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

前に説明したように、azperfli.exe を実行すると、非正常状態を示す結果が返る場合があります。

##### <a name="solution"></a>解決策
このメッセージの原因は、Standard Azure ストレージ アカウントの統計情報を確認するために Azure Extension for SAP によって使用される API が、Standard Managed Disks で提供されていないためです。 これは、重要な問題ではありません。 Standard Disk Storage アカウントにデータ収集が導入されたのは、頻繁に発生する入出力を調整するためでした。 Managed Disks では、ストレージ アカウント内のディスクの数を制限することによってこのような調整は回避されます。 そのため、その種のデータがなくても重要ではありません。

### <a name="some-azure-performance-counters-are-missing"></a>一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。デーモンは複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Diagnostics から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログから使用されます。

既知の問題の最新のリストについては、SAP Note [1999351] を参照してください。この SAP Note には、Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、「[Azure CLI を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-linux]」または「[PowerShell を使用して SAP ソリューション用 Azure VM 拡張機能を構成する][configure-windows]」の説明に従って、`Set-AzVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

## <a name="azure-extension-error-codes"></a>Azure 拡張機能のエラー コード

| エラー ID | エラーの説明 | 解決策 |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | アプリの構成が見つかりません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | アプリの構成にデプロイ ID がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | アプリの構成に RoleInstanceId がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | アプリの構成に RoleInstanceId がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure の構成を読み取ることができません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | アプリの構成ファイルが見つかりません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | アプリの構成に VM サイズがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | GlobalMemoryStatusEx カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | NIC カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | プロセッサ名カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | 拡張機能の構成ファイル config.xml にメトリック 'Disk type' がありません。 'Disk type' は、他のいくつかのカウンターと共に、v2.2.0.68 2015/12/16 で導入されました。 2015 年 12 月 16 日より前に拡張機能をデプロイした場合は、古い構成ファイルが使用されます。 Azure 拡張機能フレームワークによって拡張機能は自動的に新しいバージョンにアップグレードされますが、config.xml は変更されません。 構成を更新するには、最新の PowerShell セットアップ スクリプトをダウンロードして実行します。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | ディスク キャッシュがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | ディスクの SLA スループットがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | ディスクの SLA IOPS がありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | 最後のハードウェア変更カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC カウンターが失敗しました | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | VM の sysprep により、Windows SID が変更されました。 | [sysprep 後に再デプロイする][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Storage Analytics へのアクセスに失敗しました。 <br /><br />新しく作成された VM 上での Storage Analytics データの作成には、最大で 30 分かかる場合があるため、しばらくするとエラーが表示されなくなる可能性があります。 エラーがまだ表示されている場合は、セットアップ スクリプトを再実行してください。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Storage Analytics カウンターがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Storage Analytics が失敗しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | WAD 構成が不適切です。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | 予期しない WAD 形式です。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | WAD カウンターが見つかりませんでした。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | 古い WAD カウンターが見つかりました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD テーブルを読み取ることができません。 WAD テーブルへの接続がありません。 これにはいくつかの原因が考えられます。<br /><br /> 1) 期限切れの構成 <br />2) Azure へのネットワーク接続がない <br />3) WAD セットアップに関する問題 | [セットアップ スクリプトの実行][deployment-guide-run-the-script]<br />[インターネット接続の修正][deployment-guide-fix-internet-connection]<br />[サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Perfmon NIC メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Perfmon ディスク メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | 一部の prefmon メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon でカウンターを作成できませんでした。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | メトリック プロバイダーが構成されていません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Storage Analytics 構成が不適切です。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Storage Analytics のメトリックが失敗しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | メトリック プロバイダーの 1 つでエラーが発生しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | プロバイダー スレッドが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>指定されたソリューションに関する詳細なガイドライン

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>セットアップ スクリプトを実行する

このガイドの「[Azure Extension for SAP を構成する][configure-windows]」の章の手順に従って、拡張機能をもう一度インストールします。 カウンターによっては、プロビジョニングに最大 30 分かかる場合があることに注意してください。

エラーが解消されない場合は、[サポートにお問い合わせください][deployment-guide-contact-support]。

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>サポートに問い合わせる

予期しないエラーが発生したか、既知の解決策がありません。 フォルダー C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop (Windows) または /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) にある AzureEnhancedMonitoring_service.log ファイルを収集し、SAP サポートに連絡してさらに支援を求めてください。

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>sysprep 後に再デプロイする

一般化された sysprep 済み OS イメージ (SAP ソフトウェアを含めることが可能です) をビルドする予定の場合は、このイメージに Azure Extension for SAP を含めないことをお勧めします。 一般化された OS イメージの新しいインスタンスがデプロイされた後に、Azure Extension for SAP をインストールする必要があります。

ただし、一般化および sysprep された OS イメージに既に Azure Extension for SAP が含まれている場合は、新しくデプロイされた VM インスタンス上で、次の回避策を適用して拡張機能を再構成することができます。

* 新しくデプロイされた VM インスタンス上で、次のフォルダーの内容を削除します。  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* このガイドの「[Azure Extension for SAP を構成する][configure-windows]」の章の手順に従って、拡張機能をもう一度インストールします。

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>インターネット接続の修正

Azure Extension for SAP を実行している Microsoft Azure 仮想マシンには、インターネットへのアクセスが必要です。 Azure VM が Azure 仮想ネットワークに属しているか、オンプレミス ドメインにある場合は、関連するプロキシ設定が構成されていることを確認してください。 インターネットにアクセスするには、これらの設定がローカル システム アカウントでも有効である必要があります。 このガイドの「[プロキシの構成][configure-proxy]」の章に従ってください。

また、Azure VM の静的 IP アドレスを設定する必要がある場合は、Azure VM 内で手動で設定せず、[Azure PowerShell](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-ps.md)、[Azure CLI](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-cli.md)、[Azure portal](../../../virtual-network/ip-services/virtual-networks-static-private-ip-arm-pportal.md) を使用して設定してください。 静的 IP は、Azure DHCP サービスを介して伝達されます。

Azure VM 内での静的 IP アドレスの手動による設定はサポートされておらず、Azure Extension for SAP に関する問題が発生する可能性があります。


## <a name="next-steps"></a>次のステップ
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ](./deployment-guide.md)
* [SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)