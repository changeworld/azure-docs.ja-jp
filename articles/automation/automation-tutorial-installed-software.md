---
title: "Azure Automation でマシンにインストールされているソフトウェアを検出する | Microsoft Docs"
description: "インベントリを使用して、環境全体のマシンにインストールされているソフトウェアを検出します。"
services: automation
keywords: "インベントリ, オートメーション, 変更, 追跡"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: hero-article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 49eedd975e456d97f9eee44f9b00993b115ad4fa
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Azure マシンと Azure 以外のマシンにインストールされているソフトウェアを検出する

このチュートリアルでは、環境内にインストールされているソフトウェアを検出する方法について説明します。 コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 変更履歴とインベントリのために VM をオンボードする
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (http://portal.azure.com) にログインします。

## <a name="enable-change-tracking-and-inventory"></a>変更履歴とインベントリを有効にする

このチュートリアルでは、まず VM の変更履歴とインベントリを有効にする必要があります。 VM の別の Automation ソリューションで有効にした場合、この手順は不要です。

1. 左側のメニューで **[仮想マシン]** を選択し､一覧から VM を選択します。
2. 左側のメニューの **[操作]** セクションで、**[インベントリ]** をクリックします。 **[変更履歴とインベントリを有効化]** ページが開きます。

この VM でインベントリが有効かどうかを確認する検証が行われます。
この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペースは、インベントリのような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) とハイブリッド worker でプロビジョニングされているかどうかが確認されます。
このエージェントは VM との通信に使用され、インストールされているソフトウェアに関する情報を取得します。
また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation ハイブリッド Runbook worker でプロビジョニングされているかどうかが確認されます。

これらの前提条件が満たされていない場合、バナーが表示され、ソリューションを有効にするオプションを選択できます。

![インベントリのオンボード構成バナー](./media/automation-tutorial-installed-software/enableinventory.png)

ソリューションを有効にするには、バナーをクリックします。
検証後、次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペース
* [Automation](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) が VM で有効になっている

**[変更履歴とインベントリ]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、**[有効にする]** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![変更履歴ソリューションを有効にするウィンドウ](./media/automation-tutorial-installed-software/installed-software-enable.png)

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。
ソリューションが有効になると、VM にインストールされているソフトウェアと変更に関する情報が Log Analytics に送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="view-installed-software"></a>インストールされているソフトウェアを表示する

変更履歴とインベントリ ソリューションが有効になると、**[インベントリ]** ページで結果を表示できます。

VM 内から **[操作]** の **[インベントリ]** を選択します。

**[インベントリ]** ページの **[ソフトウェア]** タブをクリックします。

**[ソフトウェア]** タブには、検出されたソフトウェアの一覧が表示されます。 ソフトウェアは、ソフトウェアの名とバージョンでグループ分けされています。

各ソフトウェア レコードの詳細情報を表で確認できます。 たとえば、ソフトウェア名、バージョン、発行元、最終更新時刻 (グループ内のマシンから報告された最新の更新時間)、マシン数 (そのソフトウェアがインストールされているマシンの数) などの情報です。

![ソフトウェア インベントリ](./media/automation-tutorial-installed-software/inventory-software.png)

行をクリックすると、ソフトウェア レコードのプロパティと、そのソフトウェアがインストールされたマシンの名前が表示されます。

個々のソフトウェアまたはソフトウェアのグループを探すには、ソフトウェア一覧の上部にあるテキスト ボックスで直接検索します。
このフィルターを使用すると、ソフトウェアの名前、バージョン、または発行元に基づいて検索することができます。

たとえば、"Contoso" を検索すると、名前、発行元、またはバージョンに "Contoso" を含むすべてのソフトウェアが返されます。

## <a name="search-inventory-logs-for-installed-software"></a>インストールされているソフトウェアのインベントリ ログを検索する

インベントリは、Log Analytics に送信されるログ データを生成します。 クエリを実行してログを検索するには、**[インベントリ]** ウィンドウの上部にある **[Log Analytics]** ウィンドウを選択します。

インベントリ データは、型 **ConfigurationData** に格納されます。
次のサンプル Log Analytics クエリは、"Microsoft" を含む発行元と各発行元のソフトウェア レコード数 (SoftwareName と Computer でグループ化された数) を返します。

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Log Analytics でのログ ファイルの実行と検索については、[Azure Log Analytics ](https://docs.loganalytics.io/index) に関するページを参照してください。

### <a name="single-machine-inventory"></a>1 台のマシンのインベントリ

1 台のマシンのソフトウェア インベントリを表示するには、Azure VM リソース ページから [インベントリ] にアクセスするか、Log Analytics でフィルターを使用して対応するマシンを表示します。 次の例の Log Analytics クエリは、ContosoVM という名前のマシンのソフトウェア一覧を返します。

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のようなソフトウェア インベントリの表示方法について学習しました。

> [!div class="checklist"]
> * 変更履歴とインベントリのために VM をオンボードする
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

さらに詳しく学ぶには、変更履歴とインベントリ ソリューションの概要に進んでください。

> [!div class="nextstepaction"]
> [変更管理とインベントリ ソリューション](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)