---
title: Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成
description: Azure Storage アカウントで Azure 診断データを収集して、使用可能なツールのいずれかで表示できるようにする方法について説明します。
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473219"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成
Azure Diagnostics 拡張機能は Azure Monitor のエージェントで、ゲスト オペレーティング システムと Azure 仮想マシンと他のコンピューティング リソースのワークロードから監視データを収集します。 この記事では、Windows 診断拡張機能のインストールと構成の詳細と、Azure ストレージ アカウントでデータを保存する方法について説明します。

診断拡張機能は、Azure では[仮想マシン拡張機能](/virtual-machines/extensions/overview)として実装されているため、Resource Manager テンプレート、PowerShell、および CLI を使用した場合と同じインストール オプションがサポートされています。 仮想マシン拡張機能のインストールと保守の詳細については、[Windows 用の仮想マシン拡張機能と機能](/virtual-machines/extensions/features-windows)に関する記事を参照してください。

## <a name="install-with-azure-portal"></a>Azure portal を使用してインストールする
Azure portal で、個々の仮想マシンに診断拡張機能をインストールし、構成することができます。構成を直接操作するのではなく、インターフェイスが用意されています。 診断拡張機能を有効にすると、最も一般的なパフォーマンス カウンターとイベントを備えた既定の構成が自動的に使用されます。 特定の要件に応じて、この既定構成を変更できます。

> [!NOTE]
> Azure Event Hubs へのデータ送信など、Azure portal を使用して構成できない診断拡張機能の設定があります。 このような設定には、他のいずれかの構成方法を使用する必要があります。

1. Azure portal で仮想マシンのメニューを開きます。
2. VM メニューの **[監視]** セクションで、 **[診断設定]** をクリックします。
3. 診断拡張機能がまだ有効になっていない場合は、 **[ゲスト レベルの監視を有効にする]** をクリックします。
4. VM のリソース グループの名前に基づいた名前を持つ VM 用の新しい Azure Storage アカウントが作成されます。 **[エージェント]** タブを選択して、VM を別のストレージ アカウントに接続できます。

![診断設定](media/diagnostics-extension-windows-install/diagnostic-settings.png)


診断拡張機能を有効にすると、既定の構成を変更できます。 次の表は、さまざまなタブで変更できるオプションを説明したものです。 一部のオプションには、より詳細な構成を指定できる**カスタム** コマンドがあります。さまざまな設定の詳細については、「[Windows 診断拡張機能のスキーマ](diagnostics-extension-schema-windows.md)」を参照してください。

| タブ | 説明 |
|:---|:---|
| 概要 | 現在の構成と、他のタブへのリンクが表示されます。 |
| パフォーマンス カウンター | 収集するパフォーマンス カウンターとそれぞれのサンプル レートを選択します。  |
| ログ | 収集するログ データを選択します。 これには、Windows イベント ログ、IIS ログ、.NET アプリケーション ログ、ETW イベントが含まれます。  |
| クラッシュ ダンプ | さまざまなプロセスのクラッシュ ダンプを有効にします。 |
| シンク | Azure Storage に加えて、データ シンクから宛先にデータを送信できるようにします。<br>Azure Monitor - パフォーマンス データを Azure Monitor メトリックに送信します。<br>Application Insights - データを Application Insights アプリケーションに送信します。 |
| エージェント | エージェントの次の構成を変更します。<br>- ストレージ アカウントを変更します。<br>- エージェントに使用するローカル ディスクの最大数を指定します。<br>- エージェント自体の正常性のログを構成します。|


> [!NOTE]
> 診断拡張機能の構成は、JSON と XML のどちらの形式にすることもできますが、Azure portal で実行される構成は常に JSON 形式で保存されます。 別の構成方法で XML を使用していて、Azure portal で構成を変更した場合、設定は JSON に変更されます。

## <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure Resource Manager テンプレートを使用した診断拡張機能のデプロイについては、「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](../../virtual-machines/extensions/diagnostics-template.md)」を参照してください。 

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ
次の例のように、Azure CLI を使用して、[az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) を使用して Azure Diagnostics 拡張機能を既存の仮想マシンにデプロイできます。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

保護された設定は、構成スキーマの [PrivateConfig 要素](diagnostics-extension-schema-windows.md#privateconfig-element)で定義されます。 ストレージ アカウントを定義する保護された設定ファイルの最小限の例を次に示します。 プライベート設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#privateconfig-element)に関する記事を参照してください。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
パブリック設定は、構成スキーマの [Public 要素](diagnostics-extension-schema-windows.md#publicconfig-element)で定義されます。 診断インフラストラクチャのログ、1 つのパフォーマンス カウンター、および 1 つのイベント ログの収集を有効にするパブリック設定ファイルの最小限の例を次に示します。 パブリック設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#publicconfig-element)に関する記事を参照してください。

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell でのデプロイ
次の例のように、PowerShell を使用し、[Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) を使用して、Azure Diagnostics 拡張機能を既存の仮想マシンにデプロイできます。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

プライベート設定は [PrivateConfig 要素](diagnostics-extension-schema-windows.md#privateconfig-element)で定義され、パブリック設定は構成スキーマの [Public 要素](diagnostics-extension-schema-windows.md#publicconfig-element)で定義されます。 また、ストレージ アカウントの詳細をプライベート設定に含めるのではなく、Set-AzVMDiagnosticsExtension コマンドレットのパラメーターとして指定することもできます。

診断インフラストラクチャのログ、1 つのパフォーマンス カウンター、および 1 つのイベント ログの収集を有効にする構成ファイルの最小限の例を次に示します。 プライベートおよびパブリックの設定の詳細については、[構成例](diagnostics-extension-schema-windows.md#publicconfig-element)に関する記事を参照してください。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

また、「[PowerShell を使用して Windows を実行している仮想マシンで Azure Diagnostics を有効にする](../../virtual-machines/extensions/diagnostics-windows.md)」も参照してください。

## <a name="data-storage"></a>データ ストレージ
次の表は、診断拡張機能から収集されるさまざまな種類のデータと、それらがテーブルまたは BLOB のどちらとして格納されているかを示しています。 テーブルに格納されているデータは、パブリック構成の [StorageType 設定](diagnostics-extension-schema-windows.md#publicconfig-element)に応じて BLOB に保存することもできます。


| Data | ストレージの種類 | 説明 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | テーブル | 診断モニターと構成の変更。 |
| WADDirectoriesTable | テーブル | 診断モニターで監視されているディレクトリ。  これには、IIS ログ、IIS 失敗要求ログ、およびカスタム ディレクトリが含まれます。  BLOB ログ ファイルの場所は Container フィールドで指定され、BLOB の名前は RelativePath フィールドで指定されます。  AbsolutePath フィールドでは、Azure 仮想マシンに存在するファイルの場所と名前を示します。 |
| WadLogsTable | テーブル | トレース リスナーを使用してコードに書き込まれたログ。 |
| WADPerformanceCountersTable | テーブル | パフォーマンス カウンター。 |
| WADWindowsEventLogsTable | テーブル | Windows イベント ログ。 |
| wad-iis-failedreqlogfiles | BLOB | IIS の失敗した要求ログの情報が含まれています。 |
| wad-iis-logfiles | BLOB | IIS ログに関する情報が含まれています。 |
| "custom" | BLOB | 診断モニターによって監視されるディレクトリの構成に基づくカスタム コンテナーです。  この BLOB コンテナーの名前は WADDirectoriesTable で指定されます。 |

## <a name="tools-to-view-diagnostic-data"></a>診断データを表示するツール
ストレージへの転送後にデータを表示するには、いくつかのツールを利用できます。 次に例を示します。

* Visual Studio のサーバー エクスプローラー - Azure Tools for Microsoft Visual Studio がインストールされている場合、サーバー エクスプローラーの Azure Storage ノードを使用して、Azure ストレージ アカウントの読み取り専用の BLOB およびテーブル データを表示できます。 データは、ローカルのストレージ エミュレーター アカウントから表示できます。また、Azure 用に作成したストレージ アカウントから表示することもできます。 詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照と管理](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)」を参照してください。
* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、OSX、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) に含まれている Azure Diagnostics Manager では、Azure で実行されているアプリケーションによって収集された診断データの表示、ダウンロード、管理を行うことができます。

## <a name="next-steps"></a>次の手順
- Azure Event Hubs への監視データの転送の詳細については、[Windows Azure Diagnostics 拡張機能から Event Hubs へのデータ送信](diagnostics-extension-stream-event-hubs.md)に関する記事を参照してください。
