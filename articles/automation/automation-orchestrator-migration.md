<properties
   pageTitle="Orchestrator からの Runbook と統合パックを移行する"
   description="System Center Orchestrator の Runbook と統合パックを Azure Automation に移行する方法を説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/22/2015"
   ms.author="bwren" />


# Orchestrator から Azure Automation へ移行する

Azure Automation の Runbook は Windows PowerShell ワークフローに基づいていますが、[System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) の Runbook は特に Orchestrator 用として作成された統合パックの活動に基づいています。Azure Automation のグラフィカル Runbook の外観は Orchestrator Runbook と似ていて、PowerShell コマンドレット、子 Runbook、および資産を表す活動が含まれています。

[System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) には、Runbook を Orchestrator から Azure Automation に変換するために役立つツールが含まれています。Runbook 自体を変換するだけでなく、Windows PowerShell コマンドレットとモジュールを統合するために使用する、活動が含まれる統合パックも変換する必要があります。

Orchestrator Runbook を Azure Automation に変換するための基本的なプロセスを次に示します。これらの各手順の詳細については、以降のセクションで詳しく説明します。

1.  この記事で説明されているツールとモジュールが含まれる [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) をダウンロードします。
2.  [標準活動モジュール](#standard-activities-module)を Azure Automation にインストールします。このモジュールには、変換後の Runbook が使用する可能性がある、変換されたバージョンの Orchestrator 標準活動が含まれます。
2.  ご使用の Runbook で使用する統合パック用に、[System Center Orchestrator 統合モジュール](#system-center-orchestrator-integration-modules)を Azure Automation にインストールします。
3.  [統合パック コンバーター](#integration-pack-converter)を使用してカスタムとサード パーティ製の統合パックを変換し、Azure Automation にインストールします。
4.  手動で、Orchestrator 内のグローバル アセットを Azure Automation に再作成します。この移行を実行する自動化された方式はありません。
5.  [Runbook コンバーター](#runbook-converter-coming-soon) (近日対応予定) を使用して Orchestrator Runbook を変換し、Azure Automation にインストールします。
6.  ローカル データセンターで、変換後の Runbook を実行するように[ハイブリッド Runbook Worker](#hybrid-runbook-worker) を構成します。

## Service Management Automation

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) は Orchestrator などのローカル データセンターに Runbook を格納して実行します。使用する統合モジュールは Azure Automation と同じです。使用可能な場合には、[Runbook コンバーター](#runbook-converter-coming-soon)が、SMA ではサポートされていないグラフィカル Runbook に Orchestrator Runbook を変換します。SMA に[標準活動モジュール](#standard-activities-module)と [System Center Orchestrator 統合モジュール](#system-center-orchestrator-integration-modules)をインストールできますが、[ご利用の Runbook の再作成](http://technet.microsoft.com/library/dn469262.aspx)を手動で実行する必要があります。

## ハイブリッド Runbook Worker

Orchestrator の Runbook はデータベース サーバー上に格納され、Runbook サーバーで実行されます。どちらのサーバーもローカル データセンター上にあります。Azure Automation の Runbook は Azure Cloud に格納され、[ハイブリッド Runbook Worker](automation-hybrid-runbook-worker.md) を使用してローカル データセンターで実行できます。これが、Orchestrator から変換された Runbook を実行する通常の方法です。これらの Runbook はローカル サーバー上で実行されるように設計されているためです。

## 統合パック コンバーター

統合パック コンバーターは、Orchestrator Integration Toolkit (OIT) を使用して作成された統合パックを、Windows PowerShell に基づく統合モジュールに変換します。それらは、Azure Automation または Service Management Automation にインポートできます。

統合パック コンバーターを実行すると、ウィザードが表示され、統合パック (.oip) ファイルを選択できます。次に、ウィザードに、その統合パックに含まれる活動が一覧表示され、移行する活動を選択できます。ウィザードを完了すると、元の統合パックの各活動に対応するコマンドレットが含まれるモジュールが作成されます。


### パラメーター

統合パック内の活動のプロパティは、その統合モジュールで対応するコマンドレットのパラメーターに変換されます。Windows PowerShell コマンドレットには、すべてのコマンドレットで使用できる一連の[共通パラメーター](http://technet.microsoft.com/library/hh847884.aspx)が含まれています。たとえば、-Verbose パラメーターを使用すると、対象操作についての詳細情報を出力するコマンドレットが実行されます。コマンドレットに、共通パラメーターと同じ名前のパラメーターを含めることはできません。活動に共通パラメーターと同じ名前を持つプロパティが含まれる場合、対象パラメーターに別の名前を指定するように求めるプロンプトがウィザードによって表示されます。

### モニター活動

Orchestrator のモニター Runbook では、まず[モニター活動](http://technet.microsoft.com/library/hh403827.aspx)から開始され、特定のイベントが呼び出されるのを待機しながら継続的に実行されます。Azure Automation ではモニター Runbook がサポートされていないため、統合パックのモニター活動は変換されません。代わりに、プレース ホルダー コマンドレットが、モニター活動用に統合モジュールに作成されます。このコマンドレットには機能がありませんが、それを使用する変換後 Runbook がインストール可能になります。この Runbook は、Azure Automation で実行することはできませんが、インストールが可能なので、ユーザーが変更できます。

### 変換できない統合パック

OIT によって作成されていない統合パック (Microsoft によって作成された一部の統合パックも含まれます) は、このツールを使用して変換できません。Microsoft 提供の統合パックは統合モジュールに変換されているので、Azure Automation または Service Management Automation でインストールが可能です。


## 標準活動モジュール

Orchestrator には、統合パックには含まれていないものの多数の Runbook で使用される、一連の[標準活動](http://technet.microsoft.com/library/hh403832.aspx)が含まれています。標準活動モジュールは、これらの各活動に対応するコマンドレットが含まれている統合モジュールです。標準活動を使用する変換された Runbook をインポートする前に、Azure Automation にこの統合モジュールをインストールする必要があります。

標準活動モジュールのコマンドレットは、変換された Runbook をサポートするだけでなく、Orchestrator に精通しているユーザーが Azure Automation で新しい Runbook をビルドするために使用できます。標準活動すべての機能はコマンドレットで実行できますが、動作方法が異なる場合があります。変換後の標準活動モジュール内のコマンドレットは、対応する活動と同じように動作し、同じパラメーターを使用します。これによって、既存の Orchestrator Runbook の作成者は Azure Automation の Runbook に移行するのが容易になります。

## System Center Orchestrator 統合モジュール
Microsoft は、System Center のコンポーネントと他の製品を自動化する Runbook をビルドするための[統合パック](http://technet.microsoft.com/library/hh295851.aspx)を提供しています。現在、[TechNet](http://www.microsoft.com/download/details.aspx?id=39622) からこうした統合パックの一部をダウンロードする場合、統合パック コンバーターで変換できません。これは、System Center Orchestrator Migration Toolkit の RC リリースで修正予定の既知の問題が原因です。[System Center Orchestrator 統合モジュール](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all)には、変換されたバージョンの統合パックが含まれ、今回のリリースより前に Azure Automation と Service Management Automation にインポートできるようになっています。

## Runbook コンバーター (近日対応予定)

このツールは、Orchestrator Runbook を、Azure Automation にインポート可能なグラフィカル Runbook に変換します。このツールの詳細については、使用可能になった段階でこちらでお知らせします。

## 関連記事:

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [ハイブリッド Runbook Worker](automation-hybrid-runbook-worker.md)
- [Orchestrator 標準活動](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=July15_HO4-->