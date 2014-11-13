<properties urlDisplayName="Diagnostics" pageTitle="診断の使用方法 (.NET) - Azure の機能ガイド" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Azure で、デバッグ、パフォーマンス測定、監視、トラフィック解析などに診断データを使用する方法を説明します。" metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure (Azure の診断機能)" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Azure のクラウド サービスおよび仮想マシンの診断機能

Azure 診断 1.2 を使用すると、Azure で実行している Worker ロール、Web ロール、または仮想マシンから診断データを収集できます。このガイドでは、Azure 診断 1.2 の使用方法について説明します。ログやトレース方法を作成する方法と、診断機能やその他の手法で問題のトラブルシューティングを行う方法の詳細なガイダンスについては、「[Azure アプリケーション開発のトラブルシューティングのベスト プラクティス][Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]」を参照してください。

## 目次

-   [概要][概要]
-   [Worker ロールの診断を有効にする方法][Worker ロールの診断を有効にする方法]
-   [仮想マシンの診断を有効にする方法][仮想マシンの診断を有効にする方法]
-   [サンプル構成ファイルとスキーマ][サンプル構成ファイルとスキーマ]
-   [トラブルシューティング][トラブルシューティング]
-   [よく寄せられる質問][よく寄せられる質問]
-   [Azure 診断 1.0 および 1.2 の比較][Azure 診断 1.0 および 1.2 の比較]
-   [その他のリソース][その他のリソース]

## <a name="overview"></a><span class="short-header">概要</span>概要

Azure 診断 1.2 は、Azure で実行している Worker ロール、Web ロール、または仮想マシンから診断利用統計情報を収集できる Azure 拡張機能です。利用統計情報は Azure ストレージ アカウントに格納され、デバッグ、トラブルシューティング、パフォーマンス測定、リソース使用状況の監視、トラフィック解析と容量計画、および監査に使用できます。

診断 1.0 を以前使用したことがある場合は、診断 1.2 と比較して 3 つの大きな違いがあります。

1.  診断 1.2 はクラウドサービスに加えて、仮想マシン上で展開できます
2.  診断 1.0 は Azure SDK の一部で、クラウド サービスの展開時に展開されます。診断 1.2 は拡張機能であり、個別に展開されます。
3.  診断 1.2 では、ETW イベントおよび .NET EventSource イベントの収集が可能です。

詳細な比較については、この記事の最後にある「[Azure 診断 1.0 および 1.2 の比較][Azure 診断 1.0 および 1.2 の比較]」を参照してください。

Azure 診断では、次の種類の利用統計情報を収集できます。

|-------------------------------------|--------------------------------------------------------------------|
| **データ ソース**                   | **説明**                                                           |
| IIS Logs                            | IIS Web サイトに関する情報。                                       |
| Azure 診断インフラストラクチャ ログ | 診断自体に関する情報。                                             |
| IIS の失敗した要求ログ              | IIS サイトまたはアプリケーションへの失敗した要求に関する情報。     |
| Windows イベント ログ               | Windows イベント ログ システムに送信された情報。                   |
| パフォーマンス カウンター           | オペレーティング システムとカスタム パフォーマンス カウンター      |
| クラッシュ ダンプ                   | アプリケーションがクラッシュした場合のプロセスの状態に関する情報。 |
| カスタム エラー ログ                | アプリケーションまたはサービスで作成されたログ。                   |
| .NET EventSource                    | .NET [EventSource クラス][EventSource クラス]を使用してコードで生成されたイベント。  |
| マニフェスト ベースの ETW           | すべてのプロセスで生成された ETW イベント。                        |

## <a name="worker-role"></a><span class="short-header">Worker ロールの診断の有効化</span>Worker ロールの診断を有効にする方法

このチュートリアルでは、.NET EventSource クラスを使用して利用統計情報を生成する Azure Worker ロールの実装方法について説明します。Azure 診断を使用して利用統計情報を収集し、これを Azure ストレージ アカウントに格納します。Worker ロールを作成すると、Visual Studio はソリューションの一部として自動的に診断 1.0 を有効にします。次の手順では、Worker ロールの作成、ソリューションからの診断 1.0 の無効化、Worker ロールへの診断 1.2 の展開に関するプロセスについて説明します。

### 前提条件

この記事では、Azure サブスクリプションがあり、Azure SDK で Visual Studio 2013 を使用していることを前提としています。Azure サブスクリプションがない場合でも、[無料評価版][無料評価版]にサインアップできます。[Azure PowerShell Version 0.8.7 以降をインストールして構成している][Azure PowerShell Version 0.8.7 以降をインストールして構成している]ことを確認してください。

### 手順 1.Worker ロールを作成する

1.  **Visual Studio 2013** を起動します。
2.  .NET Framework 4.5 をターゲットとする **[クラウド]** テンプレートから、新しい**[Windows Azure クラウド サービス]** プロジェクトを作成します。プロジェクト名として「WadExample」と入力します。
3.  **[Worker ロール]** を選択します。
4.  **[ソリューション エクスプローラー]** で、**[WorkerRole1]** プロパティ ファイルをダブルクリックします。
5.  **[構成]** タブで **[診断を有効にする]** をオフにし、診断 1.0 を無効にします。
6.  ソリューションを構築してエラーが発生しないことを確認します。

### 手順 2.コードをインストルメント化する

WorkerRole.cs の内容を次のコードに置き換えます。[EventSource クラス][EventSource クラス]から継承された SampleEventSourceWriter クラスは、4 つのログの作成方法 (**SendEnums**、**MessageMethod**、**SetOther**、**HighFreq**) を実装しています。**WriteEvent** メソッドの最初のパラメーターは各イベントの ID を定義しています。Run メソッドは、**SampleEventSourceWriter** クラスに実装されているログ作成方法をぞれぞれ 10 秒ごとに呼び出す無限ループを実装します。

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Diagnostics;
    using System.Diagnostics.Tracing;
    using System.Net;
    using System.Threading;

    namespace WorkerRole1
    {
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
    }

### 手順 3.Worker ロールを展開する

1.  **[ビルド]** メニューから **[WadExample]** プロジェクト、**[発行]** の順に選択して、Worker ロールを Visual Studio から Azure に展開します。
2.  サブスクリプションを選択します。
3.  **[Microsoft Azure 発行設定]** ダイアログで **<create new…>** を選択します。
4.  **[クラウド サービスとストレージ アカウントの作成]** ダイアログで **[名前]** を入力し (「WadExampl」など)、リージョンまたはアフィニティ グループを選択します。
5.  **[環境]** を **[Staging]** に設定します。
6.  必要に応じて、他の **[設定]** を変更し、**[発行]** をクリックします。
7.  展開が完了したら、クラウド サービスが **[実行中]** 状態になっていることを Azure ポータルで確認します。

### 手順 4.診断構成ファイルを作成して拡張機能をインストールする

1.  次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義をダウンロードします。
2.  (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

3.  **[WorkerRole1]** プロジェクトを右クリックし、**[追加]** -\> **[新しい項目]** -\> **[Visual C# items]** -\> **[データ]** -\> **[XML ファイル]** の順に選択して、XML ファイルを **[WorkerRole1]** プロジェクトに追加します。ファイルに「WadExample.xml」という名前を付けます。

    ![CloudServices\_diag\_add\_xml][CloudServices\_diag\_add\_xml]

4.  構成ファイルに WadConfig.xsd を関連付けます。WadExample.xml エディタ ウィンドウがアクティブになっていることを確認します。**F4** キーを押し、**[プロパティ]** ウィンドウを開きます。**[プロパティ]** ウィンドウで **[スキーマ]** プロパティをクリックします。**[スキーマ]** プロパティで **[…]** をクリックします。**[追加]** ボタンをクリックし、XSD ファイルを保存した場所に移動して [WadConfig.xsd] を選択します。**[OK]** をクリックします。
5.  WadExample.xml 構成ファイルの内容を次の XML に置き換え、ファイルを保存します。この構成ファイルは、収集するいくつかのパフォーマンス カウンターを定義します。1 つは CPU 使用率、1 つはメモリ使用率です。次に、SampleEventSourceWriter クラスのメソッドに対応する 4 つのイベントを定義します。

        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>

### 手順 5.Worker ロールに診断をインストールする

Web ロールまたは Worker ロールの診断を管理する PowerShell コマンドレットは、Set-AzureServiceDiagnosticsExtension、Get-AzureServiceDiagnosticsExtension、Remove-AzureServiceDiagnosticsExtension です。

1.  Windows Azure PowerShell を開きます。
2.  スクリプトを実行して Worker ロールに診断をインストールします (*StorageAccountKey* を wadexample ストレージ アカウントのストレージ アカウント キーに置き換えます)。

        $storage_name = "wadexample"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
        $service_name="wadexample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging

### 手順 6.利用統計情報を確認する

Visual Studio の **[サーバー エクスプローラー]** で wadexample ストレージ アカウントに移動します。クラウド サービスを 5 分程実行すると、**WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable**、**WADSetOtherTable** の各テーブルが表示されます。いずれかのテーブルをダブルクリックして収集した利用統計情報を表示します。
![CloudServices\_diag\_tables][CloudServices\_diag\_tables]

## <a name="virtual-machine"></a><span class="short-header">仮想マシンの診断の有効化</span>仮想マシンの診断を有効にする方法

このチュートリアルでは、開発コンピューターから Azure の仮想マシンに診断をリモートでインストールする方法について説明します。また、その Azure の仮想マシン上で実行するアプリケーションの実装方法と .NET [EventSource クラス][EventSource クラス]を使用した利用統計情報の生成方法を学習します。Azure 診断を使用して利用統計を収集し、これを Azure ストレージ アカウントに格納します。

### 前提条件

このチュートリアルでは、Azure サブスクリプションがあり、Azure SDK で Visual Studio 2013 を使用していることを前提としています。Azure サブスクリプションがない場合でも、[無料評価版][無料評価版]にサインアップできます。[Azure PowerShell Version 0.8.7 以降をインストールして構成している][Azure PowerShell Version 0.8.7 以降をインストールして構成している]ことを確認してください。

### 手順 1.仮想マシンを作成する

1.  開発コンピューター上で Visual Studio 2013 を起動します。
2.  Visual Studio の **[サーバー エクスプローラー]** で **[Windows Azure]** を右クリックし、**[仮想マシン]** -\> **[仮想マシンの作成]** の順に選択します。
3.  **[サブスクリプションの選択]** ダイアログで Azure サブスクリプションを選択し、**[次へ]** をクリックします。
4.  **[仮想マシン イメージの選択]** ダイアログで **[Windows Server 2012 R2 Datacenter]** を選択し、**[次へ]** をクリックします。
5.  **[仮想マシン基本設定]** で、仮想マシンに「wadexample」という名前を付けます。管理者ユーザー名とパスワードを設定し、**[次へ]** をクリックします。
6.  **[クラウド サービスの設定]** ダイアログで「wadexampleVM」という名前の新しいクラウド サービスを作成します。「wadexample」という名前の新しいストレージ アカウントを作成し、**[次へ]** をクリックします。
7.  **[作成]** をクリックします。

### 手順 2.アプリケーションを作成する

1.  開発コンピューター上で Visual Studio 2013 を起動します。
2.  .NET Framework 4.5 をターゲットとする Visual C# の新しいコンソール アプリケーションを作成します。プロジェクト名として「WadExampleVM」と入力します。
    ![CloudServices\_diag\_new\_project][CloudServices\_diag\_new\_project]
3.  Program.cs の内容を次のコードに置き換えます。**SampleEventSourceWriter** クラスは、4 つのログの作成方法 (**SendEnums**、**MessageMethod**、**SetOther**、**HighFreq**) を実装しています。WriteEvent メソッドの最初のパラメーターは各イベントの ID を定義しています。Run メソッドは、**SampleEventSourceWriter** クラスに実装されているログ作成方法をぞれぞれ 10 秒ごとに呼び出す無限ループを実装します。

        using System;
        using System.Diagnostics;
        using System.Diagnostics.Tracing;
        using System.Threading;

        namespace WadExampleVM
        {
        sealed class SampleEventSourceWriter : EventSource
        {
            public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
            public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
            public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
            public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
            public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

        }

        enum MyColor
        {
            Red,
            Blue,
            Green
        }

        [Flags]
        enum MyFlags
        {
            Flag1 = 1,
            Flag2 = 2,
            Flag3 = 4
        }

        class Program
        {
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
        }
        }

4.  ファイルを保存し、**[ビルド]** メニューから **[ソリューションのビルド]** を選択してコードをビルドします。

### 手順 3.アプリケーションを展開する

1.  **[ソリューション エクスプローラー]** で **[WadExampleVM]** を右クリックし、ファイル エクスプローラーの **[フォルダーを開く]** を選択します。
2.  *bin\\Debug* フォルダーに移動し、すべてのファイル (WadExampleVM.\*) をコピーします。
3.  **[サーバー エクスプローラー]** で仮想マシンを右クリックし、**[リモート デスクトップを使用して接続]** を選択します。
4.  VM に接続されたら、WadExampleVM という名前のフォルダーを作成し、コピーしたアプリケーション ファイルをそのフォルダーに貼り付けます。
5.  アプリケーション ファイルの WadExampleVM.exe を起動します。空白のコンソール ウィンドウが表示されます。

### 手順 4.診断構成を作成して拡張機能をインストールする

1.  次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義を開発コンピューターにダウンロードします。

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.  既に開いているプロジェクトか、プロジェクトを開かずに Visual Studio インスタンスで、Visual Studio の新しい XML ファイルを開きます。Visual Studio で、**[追加]** -\> **[新しい項目]** -\> **[Visual C# アイテム]** -\> **[データ]** -\> **[XML ファイル]** の順に選択します。ファイルに「WadExample.xml」という名前を付けます。
3.  構成ファイルに WadConfig.xsd を関連付けます。WadExample.xml エディタ ウィンドウがアクティブになっていることを確認します。**F4** キーを押し、**[プロパティ]** ウィンドウを開きます。**[プロパティ]** ウィンドウで **[スキーマ]** プロパティをクリックします。**[スキーマ]** プロパティで **[…]** をクリックします。**[追加]** ボタンをクリックし、XSD ファイルを保存した場所に移動して [WadConfig.xsd] を選択します。**[OK]** をクリックします。
4.  WadExample.xml 構成ファイルの内容を次の XML に置き換え、ファイルを保存します。この構成ファイルは、収集するいくつかのパフォーマンス カウンターを定義します。1 つは CPU 使用率、1 つはメモリ使用率です。次に、SampleEventSourceWriter クラスのメソッドに対応する 4 つのイベントを定義します。

        <?xml version="1.0" encoding="utf-8"?>
        <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
            <WadCfg>
                <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
                <PerformanceCounters scheduledTransferPeriod="PT1M">
                    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
                    <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
                    </PerformanceCounters>
                    <EtwProviders>
                        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
                            <Event id="1" eventDestination="EnumsTable"/>
                            <Event id="2" eventDestination="MessageTable"/>
                            <Event id="3" eventDestination="SetOtherTable"/>
                            <Event id="4" eventDestination="HighFreqTable"/>
                            <DefaultEvents eventDestination="DefaultTable" />
                        </EtwEventSourceProviderConfiguration>
                    </EtwProviders>
                </DiagnosticMonitorConfiguration>
            </WadCfg>
        </PublicConfig>

### 手順 5.Azure の仮想マシンに診断をリモートでインストールする

VM の診断を管理する PowerShell コマンドレットは、Set-AzureVMDiagnosticsExtension、Get-AzureVMDiagnosticsExtension、Remove-AzureVMDiagnosticsExtension です。

1.  開発コンピューター上で Windows Azure PowerShell を開きます。
2.  スクリプトを実行して VM に診断をリモートでインストールします (*StorageAccountKey* を wadexamplevm ストレージ アカウントのストレージ アカウント キーに置き換えます)。

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### 手順 6.利用統計情報を確認する

Visual Studio の **[サーバー エクスプローラー]** で wadexample ストレージ アカウントに移動します。VM を 5 分程実行すると、**WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable**、**WADSetOtherTable** の各テーブルが表示されます。いずれかのテーブルをダブルクリックして収集した利用統計情報を表示します。
![CloudServices\_diag\_wadexamplevm\_tables][CloudServices\_diag\_wadexamplevm\_tables]

## <a name="configuration-file-schema"></a><span class="short-header">サンプル構成ファイルとスキーマ</span>構成ファイル スキーマ

診断構成ファイルは、診断モニターを開始するときに診断構成設定の初期化に使用される値を定義します。サンプル構成ファイルとそのスキーマの詳細なドキュメントについては、「[Azure Diagnostics 1.2 Configuration Schema (Azure 診断 1.2 構成スキーマ)][Azure Diagnostics 1.2 Configuration Schema (Azure 診断 1.2 構成スキーマ)]」を参照してください。

## <a name="troubleshooting"></a><span class="short-header">トラブルシューティング</span>トラブルシューティング

### Azure 診断が起動しない

診断は、ゲスト エージェント プラグインと監視エージェントの 2 つのコンポーネントで構成されます。ゲスト エージェント プラグインのログ ファイルは次のファイルにあります。

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics\\<diagnosticsversion>*\\CommandExecution.log

プラグインによって次のエラー コードが返されます。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>終了コード</strong></td>
<td align="left"><strong>説明</strong></td>
</tr>
<tr class="even">
<td align="left">0</td>
<td align="left">成功。</td>
</tr>
<tr class="odd">
<td align="left">-1</td>
<td align="left">一般的なエラー。</td>
</tr>
<tr class="even">
<td align="left">-2</td>
<td align="left"><p>rcf ファイルを読み込めませんでした。</p>
<p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="odd">
<td align="left">-3</td>
<td align="left"><p>診断構成ファイルを読み込めませんでした。</p>
<p>解決策: これは、構成ファイルがスキーマ検証を渡さないことが原因です。スキーマに準拠する構成ファイルを使用してください。</p></td>
</tr>
<tr class="even">
<td align="left">-4</td>
<td align="left"><p>監視エージェント診断の別のインスタンスが既にローカル リソース ディレクトリを使用しています。</p>
<p>解決策: <strong>LocalResourceDirectory</strong> に別の値を指定します。</p></td>
</tr>
<tr class="odd">
<td align="left">-6</td>
<td align="left"><p>ゲスト エージェント プラグインの起動ツールが無効なコマンド·ラインで診断を起動しようとしました。</p>
<p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="even">
<td align="left">-10</td>
<td align="left">診断がハンドルされていない例外で終了しました。</td>
</tr>
<tr class="odd">
<td align="left">-11</td>
<td align="left"><p>ゲスト エージェントが、監視エージェントの起動と監視を処理するプロセスを作成できませんでした。</p>
<p>解決策: 新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。</p></td>
</tr>
<tr class="even">
<td align="left">-101</td>
<td align="left"><p>診断プラグインを呼び出すときの引数が無効です。</p>
<p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="odd">
<td align="left">-102</td>
<td align="left"><p>プラグイン プロセスの初期化でエラーが発生しました。</p>
<p>解決策: 新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。</p></td>
</tr>
<tr class="even">
<td align="left">-103</td>
<td align="left"><p>プラグイン プロセスの初期化でエラーが発生しました。具体的にはロガー オブジェクトを作成できません。</p>
<p>解決策: 新しいプロセスを起動するのに十分なシステム リソースを使用できることを確認します。</p></td>
</tr>
<tr class="odd">
<td align="left">-104</td>
<td align="left"><p>ゲスト エージェントが指定した rcf ファイルを読み込むことができません。</p>
<p>これは、ゲスト エージェント プラグインの起動ツールが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="even">
<td align="left">-105</td>
<td align="left"><p>診断プラグインが診断構成ファイルを開くことができません。</p>
<p>これは、診断プラグインが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="odd">
<td align="left">-106</td>
<td align="left"><p>診断構成ファイルを読み取れませんでした。</p>
<p>解決策: これは、構成ファイルがスキーマ検証を渡さないことが原因です。そのため、スキーマに準拠する構成ファイルを使用してください。VM 上の <em>%SystemDrive%\WindowsAzure\Config</em> フォルダーには、診断拡張機能に渡される XML があります。適切な XML ファイルを開いて <strong>Microsoft.Azure.Diagnostics</strong> を検索し、<strong>xmlCfg</strong> フィールドを見つけます。データは base64 でエンコードされているため、診断で読み込まれた XML を表示するにはこれを<a href="http://www.bing.com/search?q=base64+decoder">デコードする</a>必要があります。</p></td>
</tr>
<tr class="even">
<td align="left">-107</td>
<td align="left"><p>監視エージェントへのリソース ディレクトリのパスが無効です。</p>
<p>これは、監視エージェントが VM 上で不正に手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="odd">
<td align="left">-108</td>
<td align="left"><p>診断構成ファイルを監視エージェント構成ファイルに変換できません。</p>
<p>これは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="even">
<td align="left">-110</td>
<td align="left"><p>一般的な診断構成エラー。</p>
<p>これは、診断プラグインが無効な構成ファイルを使用して手動で起動された場合にのみ発生する内部エラーです。</p></td>
</tr>
<tr class="odd">
<td align="left">-111</td>
<td align="left"><p>監視エージェントを開始できません。</p>
<p>解決策: 十分なシステムリソースを使用できることを確認します。</p></td>
</tr>
<tr class="even">
<td align="left">-112</td>
<td align="left">一般的なエラー</td>
</tr>
</tbody>
</table>

### 診断データがストレージに記録されない

イベント データが見つからない最も一般的な原因は、不正に定義されたストレージ アカウント情報です。

解決策: 診断構成ファイルを修正し、診断を再インストールします。
イベント データは、ストレージ アカウントにアップロードされる前にフォルダーに格納されます。**LocalResourceDirectory** の詳細については、上記を参照してください。

このフォルダーにファイルがない場合、監視エージェントは起動できません。これは通常、無効な構成ファイルが原因であり、CommandExecution.log で報告される必要があります。監視エージェントが正常にイベント·データを収集している場合は、構成ファイルで定義されたイベントごとに .tsf ファイルが表示されます。

監視エージェントは、MaEventTable.tsf ファイルに発生したすべてのエラーを記録します。このファイルの内容を確認するには、次のコマンドを実行します。

        %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics.2.0.0\Monitor\x64\table2csv maeventtable.tsf

このツールでは maeventtable.csv というファイルが生成されます。このログを開いてエラーを確認できます。

## <a name="faq"></a><span class="short-header">FAQ</span>よく寄せられる質問

よく寄せられる質問とその回答は次のとおりです。

**Q.** Visual Studio ソリューションをどのようにして Azure 診断 1.0 から Azure 診断 1.1 にアップグレードしますか。

**A.** Visual Studio ソリューションを診断 1.0 から診断 1.1 (以降) にアップグレードするには、次の手動のプロセスを行います。

-   Visual Studio ソリューションの診断を無効にし、診断 1.0 がロールと共に展開されないようにします。
-   コードがトレース リスナーを使用している場合は、.NET EventSource を使用するようにコードを変更する必要があります。診断 1.1 以降はトレース リスナーをサポートしません。
-   展開プロセスを変更して診断 1.1 拡張機能をインストールします。

**Q.** 診断 1.1 拡張機能をロールまたは VM 上にインストールしている場合は、どのように診断 1.2 にアップグレードしますか。

**A.** 診断 1.1 のインストール時に「バージョン 1」を指定した場合は、*次回ロールまたは VM が再起動するときに、自動的に正規表現「1」に一致する最新バージョンに更新されます*。診断 1.1 のインストール時に「バージョン 1.1」を指定した場合は、Set- コマンドレットを再実行し、インストールするバージョンを指定できる最新バージョンに更新されます。

**Q.**テーブルにはどのようにして名前が付けられますか。

**A.** テーブルは次のようにして名前が付けられます。

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

たとえば次のようになります。

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

次の 4 つのテーブルが生成されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**イベント**

</td>
<td>
**テーブル名**

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”1” /\>

</td>
<td>
WADEvent+MD5(“prov1”)+”1”

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”2” eventDestination=”dest1” /\>

</td>
<td>
WADdest1

</td>
</tr>
<tr>
<td>
provider=”prov1” \<DefaultEvents /\>

</td>
<td>
WADDefault+MD5(“prov1”)

</td>
</tr>
<tr>
<td>
provider=”prov2” \<DefaultEvents eventDestination=”dest2” /\>

</td>
<td>
WADdest2

</td>
</tr>
</table>
</tbody>
## <a name="comparing"></a><span class="short-header">Azure 診断 1.0 および 1.2</span>Azure 診断 1.0 および 1.2 の比較

次の表は、Azure 診断のバージョン 1.0 および 1.1/1.2 でサポートされる機能を比較します。

|--------------------------------|--------------|------------------|
| **サポートされるロールの種類** | **診断 1.0** | **診断 1.1/1.2** |
| Web ロール                     | あり         | あり             |
| Worker ロール                  | あり         | あり             |
| IaaS                           | いいえ       | あり             |

|----------------------------------------------------------------------------------|--------------|------------------|
| **構成と展開**                                                                   | **診断 1.0** | **診断 1.1/1.2** |
| Visual Studio との統合 - Azure Web/Worker 開発機能への統合。                     | あり         | いいえ           |
| PowerShell スクリプト - 診断のロールへのインストールと構成を管理するスクリプト。 | あり         | あり             |

|-------------------------------------|------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|---------------------------|
| **データ ソース**                   | **既定のコレクション** | **形式** | **説明**                                                                                                                                                                                                                                                                                                                                                    | **診断 1.0** | **診断 1.1/1.2**          |
| System.Diagnostics.Trace ログ       | あり                   | テーブル | コードからトレース リスナーに送信されるトレース メッセージを記録します (トレース リスナーを web.config ファイルまたは app.config ファイルに追加する必要があります)。ログ データは、scheduledTransferPeriod の転送間隔でストレージ テーブル WADLogsTable に転送されます。                                                                                    | あり         | なし (EventSource を使用) |
| IIS ログ                            | あり                   | BLOB     | IIS サイトに関する情報を記録します。ログ データは、scheduledTransferPeriod の転送間隔で、指定したコンテナーに転送されます。                                                                                                                                                                                                                                 | あり         | あり                      |
| Azure 診断インフラストラクチャ ログ | あり                   | テーブル | 診断インフラストラクチャ、RemoteAccess モジュール、RemoteForwarder モジュールに関する情報を記録します。ログ データは、scheduledTransferPeriod の転送間隔でストレージ テーブル WADDiagnosticInfrastructureLogsTable に転送されます。                                                                                                                         | あり         | あり                      |
| IIS の失敗した要求ログ              | いいえ                 | BLOB     | IIS サイトまたはアプリケーションへの失敗した要求に関する情報を記録します。Web.config の system.WebServer の下でトレース オプションを設定することでも有効にする必要があります。ログ データは、scheduledTransferPeriod の転送間隔で、指定したコンテナーに転送されます。                                                                                       | あり         | あり                      |
| Windows イベント ログ               | いいえ                 | テーブル | オペレーティング システム、アプリケーション、またはドライバーが適切に動作しているかどうかを示す情報を記録します。パフォーマンス カウンターは明示的に指定する必要があります。これらが追加されると、パフォーマンス カウンター データは scheduledTransferPeriod の転送間隔でストレージ テーブル WADPerformanceCountersTable に転送されます。                   | あり         | あり                      |
| パフォーマンス カウンター           | いいえ                 | テーブル | オペレーティング システム、アプリケーション、またはドライバーが適切に動作しているかどうかを示す情報を記録します。パフォーマンス カウンターは明示的に指定する必要があります。これらが追加されると、パフォーマンス カウンター データは scheduledTransferPeriod の転送間隔でストレージ テーブル WADPerformanceCountersTable に転送されます。                   | あり         | あり                      |
| クラッシュ ダンプ                   | いいえ                 | BLOB     | システム クラッシュ時のオペレーティング システムの状態に関する情報を記録します。小さいクラッシュ ダンプがローカルで収集されます。完全なダンプを有効にできます。ログ データは、scheduledTransferPeriod の転送間隔で、指定したコンテナーに転送されます。ほとんどの例外は ASP.NET によって処理されるため、これは通常 Worker ロールまたは VM でのみ役立ちます。 | あり         | あり                      |
| カスタム エラー ログ                | いいえ                 | BLOB     | ローカル ストレージ リソースを使用することで、カスタム データを記録し、指定したコンテナーにすぐに転送できます。                                                                                                                                                                                                                                             | あり         | あり                      |
| EventSource                         | いいえ                 | テーブル | .NET EventSource クラスを使用してコードで生成されたログ イベント。                                                                                                                                                                                                                                                                                          | いいえ       | あり                      |
| マニフェスト ベースの ETW           | いいえ                 | テーブル | すべてのプロセスで生成された ETW イベント。                                                                                                                                                                                                                                                                                                                 | いいえ       | あり                      |

## <a name="additional"></a><span class="short-header">その他のリソース</span>その他のリソース

-   [Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発時のトラブルシューティングのベスト プラクティス)][Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]
-   [Azure 診断を使用したログ データの収集][Azure 診断を使用したログ データの収集]
-   [クラウド サービスのデバッグ][クラウド サービスのデバッグ]
-   [Azure 診断の構成][Azure 診断の構成]

  [Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh771389.aspx
  [概要]: #overview
  [Worker ロールの診断を有効にする方法]: #worker-role
  [仮想マシンの診断を有効にする方法]: #virtual-machine
  [サンプル構成ファイルとスキーマ]: #configuration-file-schema
  [トラブルシューティング]: #troubleshooting
  [よく寄せられる質問]: #faq
  [Azure 診断 1.0 および 1.2 の比較]: #comparing
  [その他のリソース]: #additional
  [EventSource クラス]: http://msdn.microsoft.com/ja-jp/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  [無料評価版]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [Azure PowerShell Version 0.8.7 以降をインストールして構成している]: http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/
  [CloudServices\_diag\_add\_xml]: ./media/cloud-services-dotnet-diagnostics/AddXmlFile.png
  [CloudServices\_diag\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleTables.png
  [CloudServices\_diag\_new\_project]: ./media/cloud-services-dotnet-diagnostics/NewProject.png
  [CloudServices\_diag\_wadexamplevm\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png
  [Azure Diagnostics 1.2 Configuration Schema (Azure 診断 1.2 構成スキーマ)]: http://msdn.microsoft.com/ja-jp/library/azure/dn782207.aspx
  [Azure 診断を使用したログ データの収集]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433048.aspx
  [クラウド サービスのデバッグ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405479.aspx
  [Azure 診断の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn186185.aspx
