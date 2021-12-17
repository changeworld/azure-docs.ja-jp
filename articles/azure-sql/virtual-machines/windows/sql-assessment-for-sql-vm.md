---
title: SQL の評価
description: パフォーマンスの問題を特定し、SQL Server VMがベストプラクティスに従って構成されていることを評価するには、Azure portalのSQL Assessments 機能を使用します。
author: ebruersan
ms.author: ebrue
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/02/2021
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0c163f3ea101ec47eda25bc81f78033893cad6a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500281"
---
# <a name="sql-assessment-for-sql-server-on-azure-vms-preview"></a>SQLAzure VMs SQL Serverの評価 (プレビュー)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure portalのSQL Assessment 機能は、考えられるパフォーマンスの問題を特定し、 Azure Virtual Machines (VMs)上のSQL Serverが[SQL Assessment API](/sql/sql-assessment-api/sql-assessment-api-overview)によって提供される豊富な[ルールセット](https://github.com/microsoft/sql-server-samples/blob/master/samples/manage/sql-assessment-api/DefaultRuleset.csv)を使用してベスト プラクティスに従って構成されていることを評価します。 

現在、SQL評価機能はプレビュー中です。

## <a name="overview"></a>概要

SQL Assessment 機能が有効になると、SQL Serverインスタンスとデータベースがスキャンされ、インデックス、非推奨の機能、トレース フラグや統計の有効化または欠落などの推奨事項が提供されます。推奨事項は、[SQL VM管理ページ](manage-sql-vm-portal.md)に表示[Azure portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 


評価結果は、 [Microsoft Monitoring Agent(MMA)](../../../azure-monitor/agents/log-analytics-agent.md)を使用して[Log Analyticsワークスペース](../../../azure-monitor/logs/quick-create-workspace.md)にアップロードされます VM が Log Analytics を使用するように既に構成されている場合、SQL 評価機能では既存の接続が使用されます。  それ以外の場合、MMA拡張機能はVMのSQL Serverインストールされ、指定されたLog Analyticsワークスペースに接続されます。

評価の実行時間は、環境 (データベースやオブジェクトの数など) に依存し、数分から最大 1時間の期間があります。 同様に、評価結果のサイズも環境によって異なります。 評価は、インスタンスと、そのインスタンス上のすべてのデータベースに対して実行されます。

## <a name="prerequisites"></a>前提条件

SQL評価機能を使用するには、次の前提条件が必要です。 

- SQL Server VM は、[フルモードのSQL Server IaaS](sql-agent-extension-manually-register-single-vm.md#full-mode) 拡張機能に登録されている必要があります。 
- 評価結果をアップロードするVMと同じサブスクリプションSQL Server [Log Analytics ワークスペース。](../../../azure-monitor/logs/quick-create-workspace.md) 
- SQL Server は 2012 以上のバージョンにする必要があります。


## <a name="enable"></a>有効にする

評価を有効SQL、次の手順に従います。 

1. [Azure portal](https://portal.azure.com)にサインインし、 [SQL ServerVM リソース](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)に移動します。 
1. **設定** の下に **SQL Assessment** を選択します。 
1. **SQL Assessmentを有効にする** 若しくは **構成** を選択して、**構成** ページに移動します。 
1. **Enable SQL Assessments** をオンにして、次の情報を指定します。
    1. 評価 がアップロードされる[ Log Analytics ワークスペース](../../../azure-monitor/logs/quick-create-workspace.md) VM がSQL Serverワークスペースに関連付けされていない場合は、ドロップダウンからサブスクリプション内の既存のワークスペースを選択します。 それ以外の場合は、以前に関連付けられたワークスペースが既に設定されています。  
    1. **実行スケジュール** 必要に応じて評価を実行するか、スケジュールに基んで自動的に評価を実行することもできます。 スケジュールを選択した場合は、頻度 (毎週または毎月)、週の日、繰り返し (1 ~ 6 週間ごとに)、評価を開始する時刻 (VM 時間に対してローカル) を指定します。 
1. **適用** を選択して変更を保存し、Microsoft Monitoring Agentデプロイされていない場合SQL Server VM にデプロイします。 VM Azure portal評価機能の準備がSQLすると、新しい通知SQL Serverされます。 
    

## <a name="assess-sql-server-vm"></a>VMのSQL Server評価

評価の実行。
- [スケジュールに従う]
- オン デマンド

### <a name="run-scheduled-assessment"></a>スケジュールされた評価を実行する

構成ブレードでスケジュールを設定すると、指定した日時に評価が自動的に実行されます。 **構成** を選択して評価スケジュールを変更します。 新しいスケジュールを指定すると、以前のスケジュールが上書きされます。  

### <a name="run-on-demand-assessment"></a>オンデマンド評価を実行します。

VM SQL評価機能が有効SQL Server、必要に応じて評価を実行できます。 [Azure portal SQL ServerVM リソース](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines)ページのSQL 評価ブレードから **評価実行する** を選択します。


## <a name="view-results"></a>結果の表示

**評価結果** の **評価SQLセクション** ページには、最新の評価の実行の一覧が表示されます。 各行には、実行の開始時刻と状態 (スケジュール済み、実行中、結果のアップロード、完了、失敗) が表示されます。 各評価の実行には、インスタンスを評価し、結果を Log Analyticsワークスペースにアップロードする 2 つの部分があります。 状態フィールドは両方の部分を対象とします。 価結果は Azureブックに表示されます。 

評価結果のAzureブックにアクセスするには、次の 3つの方法があります。 
- **SQL評価** ページの **最新の成功した評価ボタン** を選択します。
- **SQL評価** ページの **評価結果** セクションからの完了実行を選択します。 
- SQL VMリソースページの **概要** ページに表示される **上位10のレコメンデーション** から **評価結果の表示** を選択します。 

ブックを開いた後、ドロップダウンを使用して以前の実行を選択できます。 **結果** ページを使用して1回の実行の結果を表示したり、**傾向** ページを使用して履歴の傾向を確認したりできます。

### <a name="results-page"></a>[結果] ページ

**結果** ページでは、*すべて、 新規、および 解決済み* のタブを使用して、推奨事項が整理されます。 これらのタブを使用して、現在の実行のすべての推奨事項、すべての新しい推奨事項 (以前の実行からの差分)、または以前の実行から解決された推奨事項を表示します。 タブを使用すると、実行間の進行状況を追跡できます。 *インサイト* タブでは、最も繰り返される問題と、最も問題が発生しているデータベースが識別されます。 これらを使用して、作業を集中する場所を決定します。 

グラフでは、評価の結果として、重大度のカテゴリ (高、中、低) が異なります。 各カテゴリを選択して推奨事項の一覧を表示するか、検索ボックスでキー フレーズを検索します。 最も厳しい推奨事項から始め、一覧に移動するのが最善です。

テーブルの **名前** で並べ替え、特定のデータベースまたはインスタンス別にグループ化された推奨事項を表示します。 検索ボックスを使用すると、タグ値やキー フレーズ (パフォーマンスなど) に基づいて特定の種類の推奨事項を表示できます。 テーブルの右にある下矢印を使用して、Excel ファイルに結果を見分けることができるようになりました。 

グラフの **渡れた** セクションは、システムが既に従っている推奨事項を識別します。 

長い説明や関連するオンライン リソースなど、**メッセージ** フィールドを選択して、各推奨事項の詳細情報を表示します。 
 
### <a name="trends-page"></a>傾向ページ

**傾向** ページには、すべての問題、新しい問題、解決された問題の3つのグラフがあります。 グラフは、進行状況を確認するのに役立ちます。 解決された問題の数が増え続けながら、推奨事項の数が少なからず行くのが理想的です。 凡例には、各重大度レベルの問題の平均数が表示されます。 バーにカーソルを合わせると、実行ごとに個々の値が表示されます。 

1日に複数の実行がある場合、最新の実行だけが **傾向** ページのグラフに 含まれます。 

## <a name="known-issues"></a>既知の問題

評価の一部を使用すると、次の既知の問題SQLがあります。 

### <a name="configuration-error-for-enable-assessment"></a>評価の有効化の構成エラー

アクセスできないか別のサブスクリプションに含まれる Log Analytics ワークスペースに仮想マシンが既に関連付けられている場合、構成ブレードにエラーが表示されます。 前者の場合、そのワークスペースのアクセス許可を取得するか、[こちらの手順](../../../azure-monitor/agents/agent-manage.md)で Microsoft Monitoring Agent を削除し、VM を別の Log Analytics ワークスペースに切り替えることができます。 Microsoft は Log Analytics ワークスペースが別のサブスクリプションにあるシナリオを有効にするための作業に取り組んでいます。

### <a name="deployment-failure-for-enable-or-run-assessment"></a>評価の有効化または実行に関するデプロイエラー 

失敗したアクションに関連付けられているエラー メッセージを表示するには、SQL VMを含むリソース グループの[デプロイ履歴](../../../azure-resource-manager/templates/deployment-history.md)を参照してください。 
 
### <a name="failed-assessments"></a>失敗した評価 

**評価の実行に失敗しました** - これは、評価の実行中に SQL IaaS 拡張機能で問題が発生したかどうかを示します。 詳細なエラー メッセージは、VM内の拡張機能ログで使用できます。`C:\WindowsAzure\Logs\Plugins\Microsoft.SqlServer.Management.SqlIaaSAgent\2.0.X.Y`ここ`2.0.X.Y `では最新バージョンのフォルダーです。  

**アップロード Log Analytics ワークスペースへの結果が失敗しました** - これは、Microsoft Monitoring Agent (MMA) が時間にバインドされた方法で結果をアップロードできなかったかどうかを示します。 MMA 拡張機能が[正しくプロビジョニングされている](../../../azure-monitor/visualize/vmext-troubleshoot.md)ことを確認し、この[トラブルシューティング ガイド](../../../azure-monitor/agents/agent-windows-troubleshoot.md)に記載されている接続問題とデータ収集問題を参照してください。 

>[!TIP]
>WindowsでTLS 1.0 以降を適用し、[ここ](/troubleshoot/windows-server/windows-security/restrict-cryptographic-algorithms-protocols-schannel#schannel-specific-registry-keys)で説明するように古いSSLプロトコルを無効にした場合は、強力な暗号化を使用するように .NET Framework が[構成](../../../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)されていることを確認する必要があります。 

**Log Analytics ワークスペースのデータ保有期間が原因で結果の有効期限が切れた** - これは、結果が保持ポリシーに基づいて Log Analytics ワークスペースに保持されなくなった状態を示します。 ワークスペースの[保持期間](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) を変更できます

## <a name="next-steps"></a>次の手順

- SQL Server VM を azure VM 上の SQL Server に SQL Server IaaS 拡張機能に登録するには、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、[単一VMs](sql-agent-extension-manually-register-single-vm.md)、または[VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。
- Azure VM 上の SQL Server に対して SQL Server IaaS 拡張機能で使用できるその他の機能については、「SQL Server VM を管理する」を参照[Azure portal](manage-sql-vm-portal.md)
