---
title: Azure Migrate でマシンの依存関係を使用してマシンをグループ化する | Microsoft Docs
description: Azure Migrate サービスでマシンの依存関係を使用してアセスメントを作成する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 4b83380558c10bc4f96d56f89a5cc2b7b53edc2e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621081"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>マシンの依存関係マッピングを使用したマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) でのアセスメントのために、マシンの依存関係を視覚化してマシンのグループを作成する方法について説明します。 アセスメントを実行する前に、マシンの依存関係を照合して信頼度レベルの高い VM のグループを評価する場合、通常はこの方法を使用します。 依存関係の視覚化により、Azure への移行を効率よく計画できます。 こうすることで、Azure に移行する際に、何も残すことがなく、突然障害が発生することもなくなります。 同時に移行する必要のある、相互依存しているシステムをすべて検出し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。


## <a name="prepare-machines-for-dependency-mapping"></a>依存関係マッピング用のマシンの準備
マシンの依存関係を表示するには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。 また、インターネットに接続されていないマシンの場合、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)をダウンロードしてインストールする必要があります。

### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする
1. **[概要]** で **[管理]** > **[マシン]** をクリックし、必要なマシンを選択します。
2. **[依存関係]** 列で、**[エージェントのインストール]** をクリックします。
3. **[依存関係]** ページで、評価する各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

> [!NOTE]
> エージェントのインストールを自動化するには、System Center Configuration Manager などの任意のデプロイ ツールを使用するか、または Azure Migrate 用のエージェント デプロイ ソリューションを持つマイクロソフト パートナー ツールの [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) を使用します。

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

[Windows](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems) および [Linux](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems) オペレーティング システムの依存関係エージェントのサポートに関する詳細を確認してください。

スクリプトを使用して依存関係エージェントをインストールする方法については、[こちら](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)をご覧ください。

## <a name="create-a-group"></a>グループの作成

1. エージェントをインストールしたら、ポータルに移動し、**[管理]** > **[マシン]** をクリックします。
2. エージェントをインストールしたマシンを検索します。
3. マシンの **[依存関係]** 列に **[依存関係の表示]** と表示されます。 この列をクリックすると、マシンの依存関係が表示されます。
4. マシンの依存関係マップには次の詳細情報が表示されます。
    - マシンに対する受信 (クライアント) と送信 (サーバー) の TCP 接続
        - MMA と依存関係エージェントがインストールされていない依存マシンは、ポート番号によってグループ化されます。
        - MMA と依存関係エージェントがインストールされている依存マシンは、別のボックスに表示されます。
    - マシンで実行しているプロセス。各マシンのボックスを展開してプロセスを表示できます。
    - 各マシンの完全修飾ドメイン名、オペレーティング システム、MAC アドレスなどのプロパティ。各マシンのボックスをクリックして、これらの詳細を表示できます。

 ![マシンの依存関係の表示](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 時間範囲のラベルで期間をクリックすると、さまざまな期間の依存関係を表示できます。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。
5. グループ化する依存関係のあるマシンを特定したら、マップ上で Ctrl キーを押しながらクリックして複数のマシンを選択し、**[グループ マシン]** をクリックします。
6. グループ名を指定します。 依存するマシンが Azure Migrate によって検出されていることを確認します。

    > [!NOTE]
    > 依存するマシンが Azure Migrate によって検出されない場合は、マシンをグループに追加できません。 そのようなマシンをグループに追加するには、vCenter Server で正しい範囲に対して検出プロセスを再び実行して、マシンが Azure Migrate によって検出されることを確認する必要があります。  

7. このグループの評価を作成する場合は、グループの新しい評価を作成するためのチェックボックスをオンにします。
8. **[OK]** をクリックしてグループを保存します。

グループが作成されたら、グループのすべてのマシンにエージェントをインストールし、グループ全体の依存関係を視覚化してグループを絞り込むことをお勧めします。

## <a name="next-steps"></a>次の手順

- グループの依存関係を視覚化してグループを絞り込む[方法を確認](how-to-create-group-dependencies.md)をします。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
