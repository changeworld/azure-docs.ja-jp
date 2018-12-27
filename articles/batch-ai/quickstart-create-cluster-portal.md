---
title: Azure クイック スタート - Batch AI クラスターの作成 - ポータル | Microsoft Docs
description: クイック スタート - 機械学習および AI モデルのトレーニングに使用する Batch AI クラスターを作成する - Azure portal
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 78c743448a7f7439875d3598d6ba5d4eb6dc12fc
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408937"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Batch AI トレーニング ジョブ用のクラスターを作成する

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

このクイック スタートでは、Azure portal を使用して、AI および機械学習モデルのトレーニングに使用できる Batch AI クラスターを作成する方法について説明します。 Batch AI は、データ サイエンティストや AI 研究者が、Azure 仮想マシンのクラスター上で、AI および機械学習モデルを大規模にトレーニングするためのマネージド サービスです。

クラスターには、最初は単一の GPU ノードと接続されたファイル サーバーがあります。 このクイック スタートを完了すると、スケールアップしてディープ ラーニング モデルのトレーニングに使用できるクラスターが作成されます。 Batch AI、[Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ツール、または [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) を使用して、トレーニング ジョブをクラスターに送信します。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-ssh-key-pair"></a>SSH キー ペアの作成

このクイック スタートを完了するには、SSH キー ペアが必要です。 既存の SSH キー ペアがある場合は、この手順はスキップしてかまいません。

SSH キー ペアを作成するには、Bash シェルから次のコマンドを実行し、画面の指示に従います。 たとえば、[Azure Cloud Shell](../cloud-shell/overview.md) や、Windows 上の [Linux 用 Windows サブシステム](/windows/wsl/install-win10)を使用できます。 コマンド出力に公開キー ファイルの名前が表示されます。 公開キー ファイル (`cat ~/.ssh/id_rsa.pub`) の内容を、クリップボードまたは後の手順でアクセスできる別の場所にコピーします。

```bash
ssh-keygen -t rsa -b 2048
```

SSH キー ペアの作成方法の詳細については、[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../virtual-machines/linux/mac-create-ssh-keys.md)に関する記事をご覧ください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-batch-ai-workspace"></a>Batch AI ワークスペースの作成

まず、Batch AI リソースを整理するための Batch AI ワークスペースを作成します。 ワークスペースには、1 つ以上のクラスターまたは他の Batch AI リソースを含めることができます。

1. **[すべてのサービス]** を選択し、**[Batch AI]** をフィルター選択します。

2. **[ワークスペースの追加]** を選択します。

3. **[ワークスペース名]** と **[リソース グループ]** に値を入力します。 必要に応じて、ワークスペースの **[サブスクリプション]** と **[場所]** に別のオプションを選択します。 **[ワークスペースの作成]** を選択します。

  ![Batch AI ワークスペースの作成](./media/quickstart-create-cluster-portal/create-workspace.png)

**"デプロイメントに成功しました"** というメッセージが表示されたら、作成したリソースに移動し、ワークスペースを選択します。

## <a name="create-a-file-server"></a>ファイル サーバーの作成

Batch AI ファイル サーバーは単一ノード NFS であり、クラスター ノードに自動的にマウントできます。 これは、トレーニング ジョブの入力データと出力のストレージを提供する方法の 1 つです。

1. ワークスペースで、**[ファイル サーバー]** > **[Add batch ai file server]\(Batch AI ファイル サーバーの追加\)** を選択します。

2. **[ファイル サーバー名]** と **[VM サイズ]** に値を入力します。 このクイック スタートでは、ファイル サーバーの VM サイズとして *Standard D1_v2* が推奨されます。 トレーニング ジョブの大量の入力データまたは出力データを保存する必要がある場合は、別のサイズを選択します。

3. **管理者ユーザー名**を入力し、SSH 公開キー ファイルの内容を **[SSH キー]** にコピーします。 残りの値には既定値をそのまま使用し、**[ファイル サーバーの作成]** を選択します。

  ![Batch AI ファイル サーバーの作成](./media/quickstart-create-cluster-portal/create-file-server.png)

ファイル サーバーのデプロイには数分かかります。

サーバーが作成されたら、**[プロパティ]** をクリックし、**マウントの設定**を書き留めます。 サーバーのパブリック IP アドレスに SSH 接続して、指定されたディレクトリ (*/data*) にトレーニング データと出力ファイルをアップロードおよびダウンロードできます。

![ファイル サーバーのプロパティ](./media/quickstart-create-cluster-portal/file-server-properties.png)

## <a name="create-a-cluster"></a>クラスターの作成

次の手順では、単一の GPU ノードを含むクラスターを作成します。 クラスター ノードでは、コンテナー ベースのアプリケーションをホストするように設計された既定の Ubuntu Server イメージを実行します。これは、ほとんどのトレーニング ワークロードに使用できます。 クラスター ノードでは、マウント ポイントにファイル サーバーをマウントします。 

1. Batch AI ワークスペースで、**[クラスター]** > **[Add batch ai cluster]\(Batch AI クラスターの追加\)** を選択します。

2. **[クラスター名]** と次の設定に値を入力します。 推奨される VM サイズは、NVIDIA Tesla K80 GPU を 1 つ備えています。
  
   |Setting  |値  |
   |---------|---------|
   |**VM サイズ**     |Standard NC6|
   |**ターゲット ノード数**     |1|

3. **管理者ユーザー名**を入力し、SSH 公開キー ファイルの内容を **[SSH キー]** にコピーします。 このページのその他の値には既定値をそのまま使用し、**[次へ:ノードのセットアップ]** を選択します。

   ![クラスターの基本情報の入力](./media/quickstart-create-cluster-portal/create-cluster.png)

4. **[ボリュームのマウント]** で、**[File server references]\(ファイル サーバー参照\)** > **[追加]** を選択します。 以前に作成したファイル サーバーを選択します。 各クラスター ノードでファイル サーバーをマウントする**相対マウント パス**を入力します。 **[Save and continue]\(保存して続行\)** を選択します。

   ![ファイル サーバー参照の追加](./media/quickstart-create-cluster-portal/file-server-reference.png)

ノードの設定を保存し、**[クラスターの作成]** を選択します。

Batch AI によってノードが割り当てられるまで数分かかります。 この間は、クラスターの **[割り当ての状態]** が **[サイズ変更中]** になります。 数分後、クラスターの状態が **[安定]** になり、ノードが起動します。

![クラスターの起動](./media/quickstart-create-cluster-portal/cluster-resizing.png)

クラスター名を選択してノードの状態を確認します。 ノードの状態が **[アイドル]** であれば、トレーニング ジョブを実行する準備が整っています。

### <a name="list-cluster-nodes"></a>クラスター ノードの表示

アプリケーションをインストールしたり、メンテナンスを実行したりするために、クラスター ノード (この例では単一ノード) に接続する必要がある場合は、ポータルで接続情報を取得します。 クラスターが作成されたら、**[ノード]** をクリックし、SSH **接続**設定 (IP アドレスとポート番号) を書き留めます。

![クラスター ノード](./media/quickstart-create-cluster-portal/cluster-nodes.png)

この情報を使用して、ノードへの SSH 接続を作成します。 たとえば、次のコマンドでノードの正しい IP アドレスとポート番号に置き換えます。

```bash
ssh myusername@137.135.82.15 -p 50000
``` 

### <a name="resize-the-cluster"></a>クラスターのサイズ変更

クラスターを使用してモデルをトレーニングする場合、より多くのコンピューティング リソースが必要になることがあります。 たとえば、分散トレーニング ジョブ用にサイズを 2 ノードに増やすには、**[スケール]** を選択し、**[Target number of node]\(ノードのターゲット数\)** を 2 に設定します。 構成を保存します。

![クラスターのスケーリング](./media/quickstart-create-cluster-portal/scale-cluster.png)

クラスターのサイズが変更されるまで数分かかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Batch AI のチュートリアルとサンプルを続行する場合は、このクイック スタートで作成した Batch AI ワークスペース、ファイル サーバー、クラスターを使用します。

ジョブがスケジュールされていなくても、基になる仮想マシンの実行中は、Batch AI クラスターとファイル サーバーに対して課金されます。 実行するジョブがないときにクラスター構成を保持する場合は、クラスターのサイズを 0 ノードに変更します。 後でジョブを実行するときは、サイズを 1 ノード以上に変更します。 

クラスターとファイル サーバーを含む Batch AI ワークスペースが不要になったら削除します。 そのためには、Batch AI ワークスペースを選択し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure portal を使用して、Batch AI クラスターおよび接続されたファイル サーバーを作成する方法について説明しました。 Batch AI クラスターを使用してモデルをトレーニングする方法については、ディープ ラーニング モデルのトレーニングに関するクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [ディープ ラーニング モデルをトレーニングする](./quickstart-tensorflow-training-cli.md)
