---
title: Windows 仮想マシン (クラシック) についてゲスト OS メトリックを Azure Monitor データ ストアに送信する
description: Windows 仮想マシン (クラシック) についてゲスト OS メトリックを Azure Monitor データ ストアに送信する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978929"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Windows 仮想マシン (クラシック) についてゲスト OS メトリックを Azure Monitor データ ストアに送信する

Azure Monitor [Windows Azure 診断拡張機能](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として、ゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。 拡張機能により、以前のリンク先の記事に記載されている多くの場所にテレメトリを送信することができます。

この記事では、Windows 仮想マシン (クラシック) 用のゲスト OS のパフォーマンス メトリックを Azure Monitor メトリック ストアに送信するプロセスについて説明します。 WAD バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。  アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能が含まれています。  これまで WAD 拡張機能は Azure Storage に書き込まれましたが、Azure Monitor のデータ ストアには書き込まれませんでした。 

この記事で説明するプロセスは、Windows オペレーティング システムが実行されているクラシック仮想マシンでのみ機能します。

## <a name="pre-requisites"></a>前提条件

- Azure サブスクリプションの[サービス管理者または共同管理者](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md)である必要があります 

- サブスクリプションを [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) に登録する必要があります 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) がインストールされている必要があります。[Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) を使用することもできます 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>クラシック仮想マシンおよびストレージ アカウントを作成する

1. Azure portal を使用してクラシック VM を作成します ![クラシック VM の作成](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. この VM を作成するとき、新しいクラシック ストレージ アカウントの作成を選択します。 このストレージ アカウントは後の手順で使用します。

1. Azure portal で、ストレージ アカウント リソースのブレードに移動し、**[キー]** を選択して、ストレージ アカウント名とストレージ アカウント キーを書き留めておきます。 これらのキーは後の手順で必要です ![ストレージ アクセス キー](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>サービス プリンシパルを作成する

[サービス プリンシパルの作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)に関するページの手順を使用して、お使いの Azure Active Directory テナントでサービス プリンシパルを作成します。 このプロセスでは、以下の点に注意してください。 
- このアプリに対して新しいクライアント シークレットを作成する  
- 後の手順で使用するキーとクライアント ID を保存する。

このアプリに、メトリックを出力するリソースへの "監視メトリック パブリッシャー" アクセス許可を付与します。 リソース グループまたはサブスクリプション全体を使用できます。  

> [!NOTE]
> 診断拡張機能では、サービス プリンシパルは、Azure Monitor に対する認証と、ご自身のクラシック VM 用メトリックの出力に使用されます。

## <a name="author-diagnostics-extension-configuration"></a>その他の診断拡張機能の構成

1. ご自身の WAD 診断拡張機能の構成ファイルを準備します。 このファイルにより、ご自身のクラシック VM について、診断拡張機能の収集対象のパフォーマンス カウンターとログが決まります。 以下に例を示します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. 診断ファイルの "SinksConfig" セクションにより、新しい Azure Monitor シンクが定義されます。

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. 収集対象のパフォーマンス カウンターの一覧が表示されている構成ファイルのセクションで、パフォーマンス カウンターを Azure Monitor シンク "AzMonSink" にルーティングします。

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. プライベート構成で、Azure Monitor アカウントを定義し、メトリックの出力に使用するサービス プリンシパル情報を追加します。

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. このファイルをローカルに保存します。

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>診断拡張機能をクラウド サービスにデプロイする

1. PowerShell を起動し、サインインします

    ```powershell
    Login-AzureRmAccount
    ```

1. 最初に、お使いのクラシック VM にコンテキストを設定します

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. VM で作成されたクラシック ストレージ アカウントのコンテキストを設定します。

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  次のコマンドを使用して、診断ファイルのパスを変数に設定します。

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Azure Monitor シンクが構成されている診断ファイルを使って、クラシック VM への更新を準備します

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  次のコマンドを実行して、ご自身の VM に更新をデプロイします

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> 診断拡張機能のインストールの一環として、引き続きストレージ アカウントを指定する必要があります。 診断構成ファイルで指定されたログやパフォーマンス カウンターは、指定したストレージ アカウントに書き込まれます。

## <a name="plot-the-metrics-in-the-azure-portal"></a>Azure portal でメトリックをプロットする

1.  Azure portal に移動します

1.  左側のメニューで [モニター] をクリックします

1.  [モニター] ブレードで、**[メトリック]** をクリックします
   ![メトリックに移動](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. リソースのドロップダウンで、ご自身のクラシック VM を選択します

1. 名前空間のドロップダウンで、**[azure.vm.windows.guest]** を選択します

1. メトリックのドロップダウンで、**[Memory\Committed Bytes in Use]** を選択します
   ![メトリックのプロット](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。
