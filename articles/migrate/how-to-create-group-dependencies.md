---
title: Azure Migrate のグループの依存関係マッピングを使用してアセスメント グループを絞り込む | Microsoft Docs
description: Azure Migrate サービスのグループの依存関係マッピングを使用して、アセスメントを絞り込む方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 9f01e94eb23083ab25dd2cbd41e8bad1297abb54
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255263"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>グループの依存関係マッピングを使用したグループの絞り込み

この記事では、グループ内のすべてのマシンの依存関係を視覚化して、グループを絞り込む方法を説明します。 アセスメントを実行する前に、グループの依存関係を照合して既存のグループのメンバーシップを調整する場合、通常はこの方法を使用します。 依存関係の視覚化を使用したグループの微調整により、Azure への移行を効率よく計画できます。 一緒に移行する必要がある、互いに依存するすべてのシステムを検出できます。 こうすることで、Azure に移行する際に、何も残すことがなく、突然障害が発生することもなくなります。


> [!NOTE]
> 依存関係を視覚化するグループには、10 個を超えるマシンを含めないでください。 グループ内に 10 個を超えるマシンがある場合、依存関係視覚化機能を利用するには小さいグループに分割することをお勧めします。


## <a name="prepare-for-dependency-visualization"></a>依存関係視覚化を準備する
Azure Migrate は、マシンの依存関係を視覚化できるように Log Analytics の Service Map ソリューションを活用します。

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

### <a name="associate-a-log-analytics-workspace"></a>Log Analytics ワークスペースを関連付ける
依存関係の視覚化を利用するために、新規または既存の Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付ける必要があります。 Migrate プロジェクトが作成されている同じサブスクリプションのみにワークステーションを作成する、またはアタッチすることができます。

- プロジェクトに Log Analytics ワークスペースをアタッチするには、**[概要]** からプロジェクトの **[基本]** セクションに移動し、**[構成が必要]** をクリックします

    ![Log Analytics ワークスペースを関連付ける](./media/concepts-dependency-visualization/associate-workspace.png)

- ワークスペースを関連付けるときに、新しいワークスペースを作成するか、既存のワークスペースをアタッチするかを選択できます。
    - 新しいワークスペースを作成する場合は、ワークスペースの名前を指定する必要があります。 移行プロジェクトと同様の [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)のリージョンでワークスペースが作成されます。
    - 既存のワークスペースをアタッチするときは、移行プロジェクトと同じサブスクリプション内の使用可能なすべてのワークスペースから選択できます。 表示されるのは、[Service Map がサポートされている](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)リージョンで作成されたワークスペースのみであることに注意してください。 ワークスペースをアタッチできるようにするには、ワークスペースへの "読み取り" アクセスがあることを確認してください。

> [!NOTE]
> Migration プロジェクトに関連付けられているワークスペースは変更できません。

### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする
グループの依存関係を表示するには、グループに含まれるオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。 また、インターネットに接続されていないマシンの場合、[Log Analytics ゲートウェイ](../azure-monitor/platform/gateway.md)をダウンロードしてインストールする必要があります。

1. **[概要]** で **[管理]** > **[グループ]** をクリックし、必要なグループに移動します。
2. マシンの一覧の **[依存関係エージェント]** 列で **[インストールする必要があります]** をクリックし、エージェントのダウンロードおよびインストールの方法に関する手順を表示します。
3. **[依存関係]** ページで、グループに含まれる各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

### <a name="install-the-mma"></a>MMA をインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、**[次へ]** をクリックします。
4. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics]** > **[次へ]** の順にクリックします。
5. **[追加]** をクリックして、新しい Log Analytics ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。


Linux マシンにエージェントをインストールするには、次の手順に従います。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする
1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

[Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) および [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) オペレーティング システムの依存関係エージェントのサポートに関する詳細を確認してください。

## <a name="refine-the-group-based-on-dependency-visualization"></a>依存関係視覚化に基づいてグループを絞り込む
グループのすべてのマシンにエージェントをインストールしたら、次の手順に従って、グループの依存関係を視覚化してグループを絞り込みます。

1. Azure Migrate プロジェクトで、**[管理]** の  **[グループ]** をクリックし、グループを選択します。
2. グループ ページで  **[依存関係の表示]** をクリックして、グループの依存関係マップを開きます。
3. グループの依存関係マップには次の詳細情報が表示されます。
    - グループに含まれるすべてのマシンに対する受信 (クライアント) と送信 (サーバー) の TCP 接続
        - MMA と依存関係エージェントがインストールされていない依存マシンは、ポート番号によってグループ化されます。
        - MMA と依存関係エージェントがインストールされている依存マシンは、別のボックスに表示されます。
    - マシンで実行しているプロセス。各マシンのボックスを展開してプロセスを表示できます。
    - 各マシンの完全修飾ドメイン名、オペレーティング システム、MAC アドレスなどのプロパティ。各マシンのボックスをクリックして、これらの詳細を表示できます。

     ![グループの依存関係の表示](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. さらに細かい依存関係を表示するには、時間の範囲をクリックして変更します。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。
4. 依存マシンと、各マシン内で実行しているプロセスを確認し、グループに対して追加または削除する必要があるマシンを特定します。
5. グループにマシンを追加またはグループから削除するには、Ctrl キーを押しながらクリックしてマップ上でマシンを選択します。
    - 追加できるのは、検出済みのマシンだけです。
    - グループのマシンを追加または削除すると、そのグループの過去のアセスメントが無効になります。
    - 必要に応じて、グループを変更したときに新しいアセスメントを作成することもできます。
5. **[OK]** をクリックしてグループを保存します。

    ![マシンの追加または削除](./media/how-to-create-group-dependencies/add-remove.png)

グループの依存関係マップに表示されている特定のマシンの依存関係を確認する場合は、[マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を設定します。


## <a name="next-steps"></a>次の手順
- [依存関係の可視化の詳細については](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)、よく寄せられる質問を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
