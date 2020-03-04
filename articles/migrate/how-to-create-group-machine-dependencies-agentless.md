---
title: Azure Migrate でエージェントレスの依存関係の視覚化を設定する
description: Azure Migrate Server Assessment でエージェントレスの依存関係の視覚化を使用してグループを設定します。
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589132"
---
# <a name="set-up-agentless-dependency-visualization"></a>エージェントレスの依存関係の視覚化を設定する 

この記事では、Azure Migrate:Server Assessment で依存関係の視覚化を設定する方法について説明します。 [依存関係の視覚化](concepts-dependency-visualization.md#what-is-dependency-visualization)は、評価や Azure への移行を行うマシン間の依存関係を特定し、理解するために役立ちます。

エージェントレスの依存関係を視覚化すると、マシンにエージェントをインストールせずにマシンの依存関係を特定できます。 これは、エージェントレスの視覚化が有効になっているマシンから TCP 接続データをキャプチャすることで機能します。

> [!IMPORTANT]
> 現在、エージェントレスの依存関係の視覚化は、Azure VMware VM 用のみのプレビュー段階であり、Azure Migrate:Server Assessment ツールで検出されます。
> 機能が制限されているか、不完全である可能性があります。
> このプレビューはカスタマー サポートの対象であり、運用ワークロードで使用できます。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="current-limitations"></a>現在の制限

- 現在、依存関係の分析ビューでは、グループに対してサーバーを追加または削除することができません。
- サーバー グループの依存関係マップは現在使用できません。
- 現在、依存関係データを表形式でダウンロードすることはできません。

## <a name="before-you-start"></a>開始する前に

- エージェントレスの依存関係の視覚化に関連する要件とコストを[確認](concepts-dependency-visualization.md#agentless-visualization)します。
- エージェントレスの依存関係の視覚化を設定するための[サポート要件](migrate-support-matrix-vmware.md#agentless-dependency-visualization)を確認します。
- Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
- プロジェクトを既に作成している場合は、Azure Migrate Server:Assessment ツールを[追加](how-to-assess.md)済みであることを確認します。
- オンプレミスのマシンを検出するための [Azure Migrate アプライアンス](migrate-appliance.md)を設定済みであることを確認します。 [VMware VM](how-to-set-up-appliance-vmware.md) のアプライアンスを設定する方法を確認します。 アプライアンスは、オンプレミス マシンを検出して、Azure Migrate:Server Assessment にメタデータとパフォーマンス データを送信します。


## <a name="create-a-user-account-for-discovery"></a>検出用のユーザー アカウントを作成する

サーバー評価で検出のために VM にアクセスできるように、ユーザー アカウントを設定します。 1 つのユーザー アカウントを指定できます。

- **Windows VM**:ユーザー アカウントは、ローカル管理者またはドメイン管理者である必要があります。
- **Linux VM**:アカウントには root 特権が必要です。 また、ユーザー アカウントには /bin/netstat ファイルと /bin/ls ファイルに対する次の 2 つの機能が必要です。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。

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
3. **[サーバーの追加]** ページで、関連するマシンを検出しているアプライアンスを選択します。
4. マシン一覧からマシンを選択します。
5. **[サーバーの追加]** をクリックします。

    ![依存関係の検出を開始する](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

依存関係の検出を開始してから 6 時間前後に依存関係を視覚化することができます。

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

## <a name="stop-dependency-discovery"></a>依存関係の検出を停止する

依存関係の検出を停止するマシンを選択します。

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. **[依存関係の分析]** アイコンをクリックします。
3. **[サーバーの削除]** をクリックします。
3. **[サーバーの削除]** ページで、依存関係の検出を停止する対象の VM が検出された**アプライアンス**を選択します。
4. マシン一覧からマシンを選択します。
5. **[サーバーの削除]** をクリックします。


## <a name="next-steps"></a>次のステップ

評価用の[マシンをグループ化](how-to-create-a-group.md)します。
