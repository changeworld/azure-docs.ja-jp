---
title: Azure Monitor メトリック ストアの従来のクラウド サービスにゲスト OS メトリックを送信する
description: Azure Monitor メトリック ストアの従来のクラウド サービスにゲスト OS メトリックを送信する
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986916"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Azure Monitor メトリック ストアの従来のクラウド サービスにゲスト OS メトリックを送信する

Azure Monitor [Windows Azure 診断拡張機能](azure-diagnostics.md) (WAD) を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として実行中のゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。  拡張機能により、以前にリンクされた記事に記載されている多くの場所にテレメトリを送信できます。  

この記事では、従来の Azure クラウド サービス用のゲスト OS のパフォーマンス メトリックを Azure Monitor メトリック ストアに送信するプロセスについて説明します。 WAD バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 この場所にこれら書き込むことで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。  アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能があります。  これまで、WAD 拡張機能は Azure Storage に書き込みましたが、Azure Monitor データ ストアには書き込みませんでした。  

この記事で説明されているプロセスは、Azure クラウド サービスでのパフォーマンス カウンターに対してのみ機能します。 他のカスタム メトリックに対しては機能しません。 
   

## <a name="pre-requisites"></a>前提条件

- Azure サブスクリプションで、[サービス管理者または共同管理者](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md)である必要があります 

- サブスクリプションを [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) で登録する必要があります 

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) をインストールしておく必要があります。[Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) を使用することもできます 


## <a name="provision-cloud-service-and-storage-account"></a>クラウド サービスとストレージ アカウントのプロビジョニング 

1. 従来のクラウド サービスを作成して配置します (従来のクラウド サービスのアプリケーションと配置のサンプルは[ここ](../cloud-services/cloud-services-dotnet-get-started.md)にあります)。 

2. 既存のストレージ アカウントを使用できるか、新しいストレージ アカウントを配置できます。 ストレージ アカウントが、作成した従来のクラウド サービスと同じ領域にある場合は最適です。 Azure portal で、ストレージ アカウントのリソース ブレードに移動し、**キー**を選択します。 ストレージ アカウント名とストレージ アカウント キーを書き留めます。これらは後の手順で必要になります。

   ![ストレージ アカウント キー](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>サービス プリンシパルを作成する 

../azure/azure-resource-manager/resource-group-create-service-principal-portal にある指示を使用して、Azure Active Directory テナントでサービス プリンシパルを作成します。 このプロセスを進める際には、次の点に注意してください。 
  - サインオン URL には任意の URL を入力できます。  
  - このアプリ用に新しいクライアント シークレットを作成する  
  - 後の手順で使用するために、キーとクライアント ID を保存します。  

以前の手順の*メトリック パブリッシャーの監視*で作成したアプリに、メトリックを発行するリソースへのアクセス許可を付与します。 アプリを使用して多くのリソースに対してカスタム メトリックを発行する予定である場合は、リソース グループまたはサブスクリプション レベルでこれらのアクセス許可を付与できます。  

> [!NOTE]
> 診断拡張機能では、サービス プリンシパルを使用して、Azure Monitor を認証し、クラウド サービス用のメトリックを発行します 

## <a name="author-diagnostics-extension-configuration"></a>診断拡張機能の構成の作成 

WAD 診断拡張機能の構成ファイルを準備します。 このファイルにより、お使いのクラウド サービスに対して、診断拡張機能が収集するパフォーマンス カウンターとログが決まります。 サンプルの診断構成ファイルを以下に示します。  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

診断ファイルの "SinksConfig" セクションで、新しい Azure Monitor シンクを定義します。 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

収集するパフォーマンス カウンターのリストがある構成ファイルのセクションに、Azure Monitor シンクを追加します。 このエントリにより、指定されたすべてのパフォーマンス カウンターが Azure Monitor にメトリックとしてルーティングされます。 必要に応じて、パフォーマンス カウンターを自由に追加/削除できます。 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
最後に、プライベート構成に *Azure Monitor アカウント*セクションを追加します。 前の手順で作成されたサービス プリンシパルのクライアント ID とシークレットを入力します。 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
この診断ファイルをローカルに保存します。  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>診断拡張機能をクラウド サービスに配置します 

PowerShell を起動し、Azure にログインします 

```PowerShell
Login-AzureRmAccount 
```

次のコマンドを使用して、前の手順で作成されたストレージ アカウントの詳細に関する詳細な情報を変数に格納します。 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
同様に、次のコマンドを使用して、診断ファイルのパスを変数に設定します。 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
次のコマンドを使用して、Azure Monitor シンクが構成された診断ファイルで、診断拡張機能をクラウド サービスに配置します 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> 診断拡張機能のインストールの一環として、引き続きストレージ アカウントを指定する必要があります。 診断構成ファイルで指定されたログやパフォーマンス カウンターは、指定したストレージ アカウントに書き込まれます。  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure portal でメトリックをプロットします 

Azure portal に移動します 

 ![メトリック Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. 左側のメニューで、[モニター] をクリックします 

1. モニターのブレードで、[メトリックのプレビュー] タブをクリックします 

1. リソースのドロップダウンで、お使いのクラシック クラウド サービスを選択します 

1. 名前空間のドロップダウンで、**[azure.vm.windows.guest]** を選択します 

1. メトリックのドロップダウンで、*[Memory\Committed Bytes in Use]* を選択します 

ディメンションのフィルタリング機能と分割機能を使用することで、特定のロールと各ロール インスタンスにより使用されている合計メモリを選択して表示することができます。 

 ![メトリック Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>次の手順
- [カスタム メトリック](metrics-custom-overview.md)の詳細を確認します。



