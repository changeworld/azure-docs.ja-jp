---
title: エージェントレスの依存関係の視覚化を使用して Azure Migrate のマシンをグループ化する
description: エージェントレス方式でマシンの依存関係を使用してグループを作成する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: ff267aabe2f9e4cec38c307fe4382a84ba6d62df
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288959"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>エージェントレスの依存関係の視覚化を評価用に設定する

この記事では、エージェントレスの依存関係マッピングを Azure Migrate:Server Assessment を使用して作成する方法について説明します。 

> [!IMPORTANT]
> Azure Migrate アプライアンスを使用して検出された Azure VMware VM のエージェントレスの依存関係の視覚化は現在プレビュー段階です。
> 特定の機能はサポート対象ではなく、機能が制限されることがあります。 このプレビューはカスタマー サポートの対象であり、運用ワークロードで使用できます。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="about-dependency-mapping"></a>依存関係マッピングの概要

依存関係マッピングは、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。 一般的に依存関係マッピングは、より高い信頼度でマシンを評価したい場合に使用します。

- Azure Migrate:Server Assessment では、評価のためにマシンを集めてグループ化します。 通常、グループは、一緒に移行するマシンで構成されます。依存関係のマッピングは、マシンの依存関係を照合するのに役立つため、マシンを正確にグループ化できます。
- マッピングを使用することで、一緒に移行する必要がある相互に依存するシステムを検出できます。 また、実行中のシステムがまだユーザーにサービスを提供しているか、または移行ではなく使用停止の対象であるかを特定できます。
- 依存関係を視覚化することにより、残されたものを確実になくして、移行中の突然の停止を回避することができます。

## <a name="about-agentless-visualization"></a>エージェントレスの視覚化の概要

エージェントレスの依存関係の視覚化では、マシンにエージェントをインストールする必要はありません。 これは、エージェントレスの視覚化が有効になっているマシンから TCP 接続データをキャプチャすることで機能します。

- 依存関係の検出が開始された後、アプライアンスでは 5 分間のポーリング間隔でマシンからデータが収集されます。
- 収集されるデータは次のとおりです。
    - TCP 接続
    - アクティブな接続を含むプロセスの名前
    - 上記のプロセスを実行するインストール済みのアプリケーションの名前
    - いいえ。 ポーリング間隔ごとに検出される接続の数

## <a name="current-limitations"></a>現在の制限

- 現在、エージェントレスの依存関係の視覚化は、VMware VM でのみ使用できます。
- 現在、依存関係の分析ビューでは、グループに対してサーバーを追加または削除することができません。
- サーバー グループの依存関係マップは現在使用できません。
- 現在、依存関係データを表形式でダウンロードすることはできません。

## <a name="before-you-start"></a>開始する前に

- Azure Migrate プロジェクトを[作成](how-to-add-tool-first-time.md)していることを確認します。
- 現在、エージェントレスの依存関係の分析は、VMware マシンでのみ使用できます。
- プロジェクトを既に作成してある場合は、次のツールを[追加済み](how-to-assess.md)であることを確認します。Azure Migrate: Server Assessment ツールを追加済みであることを確認してください。
- VMWare マシンが Azure Migrate で検出されていることを確認します。これを実行するには、[VMware](how-to-set-up-appliance-vmware.md) 用に Azure Migrate アプライアンスを設定します。 アプライアンスでオンプレミスのマシンが検出されて、メタデータとパフォーマンス データが Azure Migrate: Server Assessment を使用して作成する方法について説明します。 [詳細については、こちらを参照してください](migrate-appliance.md)。
- エージェントレスの依存関係の視覚化を設定するための[要件を確認](migrate-support-matrix-vmware.md#agentless-dependency-visualization)します。



## <a name="create-a-user-account-for-discovery"></a>検出用のユーザー アカウントを作成する

検出のために Server Assessment から VM にアクセスできるように、必要なアクセス許可を持つユーザー アカウントを設定します。 1 つのユーザー アカウントを指定できます。

- **Windows VM 上で必要なアクセス許可**:ユーザー アカウントには 'ゲスト' アクセスが必要です。
- **Linux VM 上で必要なアクセス許可**:アカウントには root 特権が必要です。 また、ユーザー アカウントには /bin/netstat ファイルと /bin/ls ファイルに対する次の 2 つの機能が必要です。CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。

## <a name="add-the-user-account-to-the-appliance"></a>アプライアンスにユーザー アカウントを追加する

ユーザー アカウントをアプライアンスに追加する必要があります。

次のようにアカウントを追加します。

1. アプライアンス管理アプリを開きます。 **[Provide vCenter details]\(vCenter の詳細\)** パネルに移動します。
2. **[Discover application and dependencies on VMs]\(VM のアプリケーションと依存関係の検出\)** セクションで、 **[資格情報の追加]** をクリックします。
3. **[オペレーティング システム]** を選択します。
4. アカウントのフレンドリ名を指定します。
5. **[ユーザー名]** と **[パスワード]** を指定します
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

依存関係の検出を開始してから 6 時間後に依存関係を視覚化できます。

## <a name="visualize-dependencies"></a>依存関係を視覚化する

1. **Azure Migrate:Server Assessment** で、 **[Discovered servers]\(検出済みサーバー\)** をクリックします。
2. 依存関係マップを表示するマシンを検索します。
3. **[依存関係]** 列の **[依存関係の表示]** をクリックします。
4. **[Time duration]\(期間\)** ドロップダウンを使用して、マップを表示する期間を変更します。
5. **[クライアント]** グループを展開して、選択したマシンと依存関係があるマシンの一覧を表示します。
6. **[ポート]** グループを展開して、選択したマシンからの依存関係があるマシンの一覧を表示します。
7. 依存関係があるマシンのいずれかのマップ ビューに移動するには、マシン名をクリックし、 **[サーバー マップを読み込む]** をクリックします。

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

[マシンをグループ化する](how-to-create-a-group.md)
