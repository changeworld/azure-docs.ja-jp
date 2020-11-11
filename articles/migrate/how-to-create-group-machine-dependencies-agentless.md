---
title: Azure Migrate Server Assessment でエージェントレスの依存関係の分析を設定する
description: Azure Migrate Server Assessment でエージェントレスの依存関係の分析を設定します。
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 8893b0f41bb7a06c02a6c2aa28002980144b582a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337695"
---
# <a name="analyze-machine-dependencies-agentless"></a>マシンの依存関係の分析 (エージェントレス)

この記事では、Azure Migrate:Server Assessment でエージェントレスの依存関係の分析を設定する方法について説明します。 [依存関係の分析](concepts-dependency-visualization.md)は、評価や Azure への移行に関してマシン間の依存関係を特定し、把握するために役立ちます。


> [!IMPORTANT]
> 現在、エージェントレスの依存関係の視覚化は、Azure Migrate:Server Assessment ツールで検出された VMware VM についてプレビュー段階です。
> 機能が制限されているか、不完全である可能性があります。
> このプレビューはカスタマー サポートの対象であり、運用ワークロードで使用できます。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="current-limitations"></a>現在の制限

- 依存関係の分析ビューでは、グループのサーバーを追加または削除することはできません。
- 現在、サーバーのグループに対する依存関係マップは使用できません。
- Azure Migrate プロジェクトでは、依存関係のデータ収集は、1000 台のサーバーで同時に設定できます。 1000 をバッチ単位としてシーケンス処理することで、さらに多くのサーバーを分析することができます。

## <a name="before-you-start"></a>開始する前に

- サポートされているオペレーティング システムと必要なアクセス許可を[確認します](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)。
- 以下を実行します。
    - Azure Migrate プロジェクトがあることを確認します。 ない場合は、今すぐ[作成](how-to-add-tool-first-time.md)します。
    - Azure Migrate:Server Assessment ツールがプロジェクトに[追加](how-to-assess.md)されていることを確認します。
    - オンプレミスのマシンを検出するための [Azure Migrate アプライアンス](migrate-appliance.md)を設定します。 VMware VM 用の[アプライアンスを設定します](how-to-set-up-appliance-vmware.md)。 アプライアンスは、オンプレミス マシンを検出して、Azure Migrate:Server Assessment にメタデータとパフォーマンス データを送信します。
- 分析する各 VM に VMware Tools (10.2 以降) がインストールされていることを確認します。


## <a name="create-a-user-account-for-discovery"></a>検出用のユーザー アカウントを作成する

依存関係を検出するためにサーバー評価が VM にアクセスできるように、ユーザー アカウントを設定します。 Windows および Linux の VM に対するアカウント要件について[学習します](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)。


## <a name="add-the-user-account-to-the-appliance"></a>アプライアンスにユーザー アカウントを追加する

アプライアンスにユーザー アカウントを追加します。

1. アプライアンス管理アプリを開きます。 
2. **[Provide vCenter details]\(vCenter の詳細\)** パネルに移動します。
3. **[VM でのアプリケーションと依存関係の検出]** で、 **[資格情報の追加]** をクリックします。
3. **[オペレーティング システム]** を選択し、アカウントのフレンドリ名と、 **[ユーザー名]** / **[パスワード]** を入力します
6. **[保存]** をクリックします。
7. **[保存して検出を開始]** をクリックします。

    ![VM ユーザー アカウントを追加する](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>依存関係の検出を開始する

依存関係の検出を有効にするマシンを選択します。 

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係の分析]** アイコンをクリックします。
3. **[サーバーの追加]** をクリックします。
4. **[サーバーの追加]** ページで、関連するマシンを検出しているアプライアンスを選択します。
5. マシン一覧からマシンを選択します。
6. **[サーバーの追加]** をクリックします。

    ![依存関係の検出を開始する](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

依存関係の検出を開始してから 6 時間前後に依存関係を視覚化することができます。 複数のマシンを有効にしたい場合は、[PowerShell](#start-or-stop-dependency-discovery-using-powershell) を使用してそのようにできます。

## <a name="visualize-dependencies"></a>依存関係を視覚化する

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. 表示するマシンを検索します。
3. **[依存関係]** 列の **[依存関係の表示]** をクリックします。
4. **[Time duration]\(期間\)** ドロップダウンを使用して、マップを表示する期間を変更します。
5. **[クライアント]** グループを展開して、選択したマシンと依存関係があるマシンの一覧を表示します。
6. **[ポート]** グループを展開して、選択したマシンからの依存関係があるマシンの一覧を表示します。
7. 依存関係があるマシンのいずれかのマップ ビューに移動するには、マシン名 > **[サーバー マップを読み込む]** の順にクリックします。

    ![サーバーのポート グループを展開してサーバー マップを読み込む](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![クライアント グループを展開する ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. 選択したマシンを展開して、各依存関係のプロセスレベルの詳細を表示します。

    ![サーバーを展開してプロセスを表示する](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> 依存関係のプロセス情報は、常に使用できるとは限りません。 使用できない場合、依存関係は "不明なプロセス" というマークが付いたプロセスとして示されます。

## <a name="export-dependency-data"></a>依存関係データをエクスポートする

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係の分析]** アイコンをクリックします。
3. **[アプリケーションの依存関係のエクスポート]** をクリックします。
4. **[アプリケーションの依存関係のエクスポート]** ページで、関連するマシンが検出されているアプライアンスを選択します。
5. 開始時刻と終了時刻を選択します。 ダウンロードできるのは過去 30 日間のデータだけであることに注意してください。
6. **[Export dependency]\(依存関係のエクスポート\)** をクリックします。

依存関係データは、CSV 形式でエクスポートおよびダウンロードされます。 ダウンロードされたファイルには、依存関係の分析が有効になっているすべてのマシン間の依存関係データが含まれています。 

![依存関係をエクスポートする](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>依存関係の情報

エクスポートされた CSV 内の各行は、指定した時間スロットで観察された依存関係に対応します。 

次の表は、エクスポートされた CSV のフィールドをまとめたものです。 サーバー名、アプリケーション、プロセスの各フィールドは、エージェントレスの依存関係分析が有効になっているサーバーに対してのみ設定されることに注意してください。

**フィールド名** | **詳細**
--- | --- 
Timeslot (タイムスロット) | 依存関係が観察された時間帯。 <br/> 依存関係データは、現在、6 時間のスロットに対してキャプチャされます。
Source server name (依存元サーバー名) | 依存元マシンの名前 
Source application (依存元アプリケーション) | 依存元マシン上のアプリケーションの名前 
Source process (依存元プロセス) | 依存元マシン上のプロセスの名前 
Destination server name (依存先サーバー名) | 依存先マシンの名前
宛先 IP | 依存先マシンの IP アドレス
Destination application (依存先アプリケーション) | 依存先マシン上のアプリケーションの名前
Destination process (依存先プロセス) | 依存先マシン上のプロセスの名前 
宛先ポート | 依存先マシンのポート番号


## <a name="stop-dependency-discovery"></a>依存関係の検出を停止する

依存関係の検出を停止するマシンを選択します。 

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係の分析]** アイコンをクリックします。
3. **[サーバーの削除]** をクリックします。
3. **[サーバーの削除]** ページで、依存関係の検出を停止する対象の VM が検出された **アプライアンス** を選択します。
4. マシン一覧からマシンを選択します。
5. **[サーバーの削除]** をクリックします。

複数のマシンで依存関係を停止したい場合は、[PowerShell](#start-or-stop-dependency-discovery-using-powershell) を使用してそのようにできます。


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>PowerShell を使用して依存関係の検出を開始または停止する

GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) リポジトリから PowerShell モジュールをダウンロードします。


### <a name="log-in-to-azure"></a>Azure にログインする

1. Connect-AzAccount コマンドレットを使用して、Azure サブスクリプションにログインします。

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government を使用している場合は、次のコマンドを使用します。
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Azure Migrate プロジェクトを作成したサブスクリプションを選択します 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. ダウンロードした AzMig_Dependencies PowerShell モジュールをインポートします

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>依存関係データの収集を有効または無効にする

1. 次のコマンドを使用して、Azure Migrate プロジェクト内の検出された VMware VM の一覧を取得します。 下の例では、プロジェクト名は FabrikamDemoProject で、それが属しているリソース グループは FabrikamDemoRG です。 マシンの一覧は FabrikamDemo_VMs.csv に保存されます。

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    このファイルでは、VM の表示名、依存関係収集の現在の状態、検出されたすべての VM の ARM ID を確認できます。 

2. 依存関係を有効または無効にするには、入力 CSV ファイルを作成します。 このファイルには、"ARM ID" というヘッダーを持つ列が必要です。 CSV ファイル内の追加のヘッダーは無視されます。 前の手順で生成されたファイルを使用して CSV を作成できます。 依存関係を有効または無効にする VM を保持したファイルのコピーを作成します。 

    次の例では、入力ファイル FabrikamDemo_VMs_Enable.csv の VM の一覧で依存関係分析が有効になっています。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    次の例では、入力ファイル FabrikamDemo_VMs_Disable.csv の VM の一覧で依存関係分析が無効になっています。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI でネットワーク接続を視覚化する

Azure Migrate には、一度に多数のサーバーのネットワーク接続を視覚化し、プロセスとサーバーによってフィルター処理するために使用できる Power BI テンプレートが用意されています。 視覚化するには、下の手順に従って、依存関係データを Power BI に読み込みます。

1. GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) リポジトリから PowerShell モジュールと Power BI テンプレートをダウンロードします。

2. 下の手順に従って、Azure にログインします。 
- Connect-AzAccount コマンドレットを使用して、Azure サブスクリプションにログインします。

    ```PowerShell
    Connect-AzAccount
    ```

- Azure Government を使用している場合は、次のコマンドを使用します。

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Azure Migrate プロジェクトを作成したサブスクリプションを選択します 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. ダウンロードした AzMig_Dependencies PowerShell モジュールをインポートします

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 次のコマンドを実行します。 このコマンドは、依存関係データを CSV にダウンロードして処理し、Power BI での視覚化に使用できる一意の依存関係の一覧を生成します。 下の例では、プロジェクト名は FabrikamDemoProject で、それが属しているリソース グループは FabrikamDemoRG です。 FabrikamAppliance によって検出されたマシンの依存関係がダウンロードされます。 一意の依存関係は FabrikamDemo_Dependencies.csv に保存されます。

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. ダウンロードした Power BI テンプレートを開きます

6. ダウンロードした依存関係データを Power BI に読み込みます。
    - Power BI でテンプレートを開きます。
    - ツール バーの **[データの取得]** をクリックします。 
    - 一般的なデータ ソースから **[テキスト/CSV]** を選択します。
    - ダウンロードした依存関係ファイルを選択します。
    - **[読み込み]** をクリックします。
    - テーブルが CSV ファイルの名前でインポートされていることがわかります。 右側のフィールド バーにテーブルが表示されます。 その名前を AzMig_Dependencies に変更します
    - ツールバーの [最新の情報に更新] をクリックします。

    ネットワーク接続のグラフと、依存元サーバー名、依存先サーバー名、依存元プロセス名、依存先プロセス名の各スライサーが、インポートされたデータと共に点灯します。

7. サーバーとプロセスによってフィルター処理されたネットワーク接続のマップを視覚化します。 ファイルを保存します。


## <a name="next-steps"></a>次のステップ

評価のために[マシンをグループ化します](how-to-create-a-group.md)。
