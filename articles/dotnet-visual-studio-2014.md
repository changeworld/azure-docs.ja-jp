<properties linkid="dotnet-visual-studio-2014" urlDisplayName="Visual Studio 14 CTP2" pageTitle="Installing the Azure SDK 2.4 for Visual Studio 14 CTP2" metaKeywords="Visual Studio, Azure SDK" description="Install Azure SDK 2.4 and Visual Studio 14 CTP2" metaCanonical="" services="" documentationCenter="" title="Installing Azure SDK 2.4 for Visual Studio 14 CTP2" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

## Azure SDK 2.4 for Visual Studio "14" CTP のインストール

Azure SDK 2.4 for .NET と Visual Studio "14" CTP をインストールするには、次の手順を実行します。この手順では、Visual Studio "14" CTP による Azure 開発用の SDK、基本的なツール、および拡張機能ツールをインストールします。これは、他のバージョンの Visual Studio での使用を前提にしていません。

**メモ**:Azure SDK 2.4 には Visual Studio "14" CTP1 との互換性はありません。

Azure SDK 2.4 for .NET をインストールするには、次の手順に従います。

1.  最新の [Visual Studio "14" CTP][Visual Studio "14" CTP] をインストールします。

2.  次のリストのリンクをこの順序で使用して、Azure SDK の各コンポーネントをインストールします。次の各コンポーネントの x86 バージョンまたは x64 バージョンを選択します。

    -   Azure Authoring Tools: [WindowsAzureAuthoringTools-x86.msi][WindowsAzureAuthoringTools-x86.msi] または [WindowsAzureAuthoringTools-x64.msi][WindowsAzureAuthoringTools-x64.msi]。
    -   Azure コンピューティング エミュレーター: [WindowsAzureEmulator-x86.exe][WindowsAzureEmulator-x86.exe] または [WindowsAzureEmulator-x64.exe][WindowsAzureEmulator-x64.exe]。
    -   Azure クライアント ライブラリ: [WindowsAzureLibsForNet-x86.msi][WindowsAzureLibsForNet-x86.msi] または [WindowsAzureLibsForNet-x64.msi][WindowsAzureLibsForNet-x64.msi]。
    -   ストレージ エミュレーター: [WindowsAzureStorageEmulator.msi][WindowsAzureStorageEmulator.msi]。ローカル SQL データベースに関する警告を受け取った場合、x86 に対しては[この場所][この場所]から、x64 に対しては[この場所][1]から SQL Server LocalDB 11.0 をインストールします。
    -   Azure Tools for Visual Studio: [WindowsAzureTools.vs140.exe][WindowsAzureTools.vs140.exe]。

    </p>

## 既知の問題

1.  Visual Studio 2013 がインストールされているコンピューターに Visual Studio "14" CTP2 をインストールすると、Visual Studio "14" CTP2 でモバイル サービスを開始できません。この問題を回避するには、モバイル サービス プロジェクトで以下のアセンブリへの参照を追加します。

    -   packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
    -   packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2.  Azure の Web サイトおよびモバイル サービス向けのリモート デバッグは、Visual Studio "14" CTP2 では動作しません。

## リリース ノート

Azure SDK 2.4 の[リリース ノート][リリース ノート]を参照してください。

  [Visual Studio "14" CTP]: http://go.microsoft.com/fwlink/p/?LinkId=400776
  [WindowsAzureAuthoringTools-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400892
  [WindowsAzureAuthoringTools-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400893
  [WindowsAzureEmulator-x86.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400894
  [WindowsAzureEmulator-x64.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400895
  [WindowsAzureLibsForNet-x86.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400896
  [WindowsAzureLibsForNet-x64.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400897
  [WindowsAzureStorageEmulator.msi]: http://go.microsoft.com/fwlink/p/?LinkId=400904
  [この場所]: http://go.microsoft.com/fwlink/p/?LinkId=400778
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=400779
  [WindowsAzureTools.vs140.exe]: http://go.microsoft.com/fwlink/p/?LinkId=400903
  [リリース ノート]: http://go.microsoft.com/fwlink/?LinkId=507517
