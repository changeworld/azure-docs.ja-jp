---
title: "Azure Migrate でマシンの依存関係を使用してマシンをグループ化する | Microsoft Docs"
description: "Azure Migrate サービスでマシンの依存関係を使用してアセスメントを作成する方法について説明します。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>マシンの依存関係マッピングを使用したマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) でのアセスメントのために、マシンの依存関係マッピングを使用してマシンのグループを作成する方法について説明します。 アセスメントを実行する前に、マシンの依存関係を照合して信頼度レベルの高い VM のグループを評価する場合、通常はこの方法を使用します。



## <a name="prepare-machines-for-dependency-mapping"></a>依存関係マッピング用のマシンの準備
依存関係マッピングにマシンを含めるには、評価するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。 また、インターネットに接続されていないマシンの場合、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)をダウンロードしてインストールする必要があります。

### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする
1. **[概要]** で **[管理]** > **[マシン]** をクリックし、必要なマシンを選択します。
2. **[依存関係]** 列で、**[エージェントのインストール]** をクリックします。 
3. **[依存関係]** ページで、評価する各 VM に Microsoft Monitoring Agent (MMA) と依存関係エージェントをダウンロードしてインストールします。
4. ワークスペース ID とキーをコピーします。 これらは、MMA をオンプレミスのマシンにインストールするときに必要になります。

### <a name="install-the-mma"></a>MMA をインストールする

Windows マシンにエージェントをインストールするには、次の手順に従います。

1. ダウンロードしたエージェントをダブルクリックします。
2. **[ようこそ]** ページで **[次へ]** をクリックします。 **[ライセンス条項]** ページで、**[同意する]** をクリックしてライセンスに同意します。
3. **[インストール先のフォルダー]** で、既定のインストール フォルダーをそのまま使用するか変更し、**[次へ]** をクリックします。 
4. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics (OMS)]** > **[次へ]** の順にクリックします。 
5. **[追加]** をクリックして、新しい OMS ワークスペースを追加します。 ポータルからコピーしたワークスペース ID とキーを貼り付けます。 **[次へ]** をクリックします。


Linux マシンにエージェントをインストールするには、次の手順に従います。

1. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
2. --install 引数を使用してバンドルをインストールします。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>依存関係エージェントをインストールする
1. Windows マシンに依存関係エージェントをインストールするには、セットアップ ファイルをダブルクリックし、ウィザードに従います。
2. Linux マシンに依存関係エージェントをインストールするには、次のコマンドを使用してルートとしてインストールします。

    ```sh InstallDependencyAgent-Linux64.bin```

依存関係エージェントでサポートされるオペレーティング システムの詳細については、[こちら](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)をご覧ください。 

## <a name="create-a-group"></a>グループの作成

1. エージェントをインストールしたら、ポータルに移動し、**[管理]** > **[マシン]** をクリックします。
2. **[依存関係]** 列に **[依存関係の表示]** と表示されます。 依存関係を表示する列をクリックします。
3. マシンごとに以下を確認できます。
    - MMA と依存関係エージェントがインストールされているかどうかと、そのマシンが検出済みかどうか。
    - マシンで実行されているゲスト オペレーティング システム。
    - 受信および送信 IP 接続とポート。
    - マシンで実行中のプロセス。
    - マシン間の依存関係。

4. さらに細かい依存関係を表示するには、時間の範囲をクリックして変更します。 既定では、範囲は 1 時間です。 時間の範囲を変更することも、開始日と終了日および期間を指定することもできます。
5. グループ化する依存関係のあるマシンを特定したら、マップ上でマシンを選択し、**[グループ マシン]** をクリックします。
6. グループ名を指定します。 Azure Migrate によってマシンが検出されていることを確認します。 検出されていない場合は、オンプレミスで検出プロセスをもう一度実行します。 必要に応じて、アセスメントをすぐに実行できます。
7. **[OK]** をクリックしてグループを保存します。

    ![マシンの依存関係を持つグループの作成](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>次のステップ

- グループの依存関係を確認してグループを絞り込む[方法を確認](how-to-create-group-dependencies.md)をします。
- アセスメントの計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
