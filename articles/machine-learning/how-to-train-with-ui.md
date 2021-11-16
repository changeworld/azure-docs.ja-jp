---
title: ジョブ作成 UI を使用してトレーニング ジョブを作成する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Studio でジョブ作成 UI を使用してトレーニング ジョブを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a626c789737b8af53c18946450c9be7ee5f91176
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562012"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>ジョブ作成 UI (プレビュー) を使用してトレーニング ジョブを作成する

Azure Machine Learning でトレーニング ジョブを作成するには、さまざまな方法があります。 CLI を使用する (「[CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する) (プレビュー)](how-to-train-cli.md)」を参照)、REST API を使用する (「[REST を使用してモデルをトレーニングする (プレビュー)](how-to-train-with-rest.md)」を参照)、または UI を使用して、トレーニング ジョブを直接作成できます。 この記事では、独自のデータとコードを使用して、Azure Machine Learning Studio のジョブ作成 UI で機械学習モデルをトレーニングする方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。 

* Azure Machine Learning 内でのジョブの意味について理解します。 [CLI (v2) を使用したモデルのトレーニング方法](how-to-train-cli.md)に関するページを参照してください。

## <a name="get-started"></a>はじめに

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。 

1. サブスクリプションとワークスペースを選択します。
 
* ホームページからジョブ作成 UI を開始できます。 **[新規作成]** をクリックし、 **[ジョブ]** を選択します。 
[![Azure Machine Learning スタジオ ホームページ](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* または、左側のウィンドウからジョブ作成を開始することもできます。 **[+ 新規]** をクリックし、 **[ジョブ]** を選択します。 
[![Azure Machine Learning スタジオの左側のナビゲーション](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* または、実験ページが表示されている場合は、 **[すべての実行]** タブにアクセスし、 **[ジョブの作成]** をクリックします。 
[![ジョブ作成 UI の実験ページ エントリ](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

これらのどのオプションを選択しても、ジョブ作成パネルに移動します。このパネルには、トレーニング ジョブを構成して作成するためのウィザードがあります。 

## <a name="select-compute-resources"></a>コンピューティング リソースを選択する

ジョブ作成 UI の最初の手順では、ジョブを実行するコンピューティング ターゲットを選択します。 ジョブ作成 UI では、いくつかのコンピューティングの種類がサポートされています。

| コンピューティングの種類 | はじめに | 
| --- | --- | 
| コンピューティング インスタンス | [Azure Machine Learning コンピューティング インスタンスとは](concept-compute-instance.md) | 
| コンピューティング クラスター | [コンピューティング クラスターとは](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| アタッチされた Kubernetes クラスター | [Azure Arc 対応機械学習 (プレビュー) を構成します](how-to-attach-arc-kubernetes.md)。 | 

1. コンピューティングの種類を選択する
1. 既存のコンピューティング リソースを選択します。 ドロップダウンには、選択に役立つノード情報と SKU の種類が表示されます。
1. コンピューティング クラスターまたは Kubernetes クラスターの場合は、 **[インスタンス数]** にジョブのノード数を指定することもできます。 既定のインスタンス数は 1 です。 
1. 選択が完了したら、 **[次へ]** を選択します。 
 [![コンピューティング クラスターを選択する](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

初めて Azure Machine Learning を使用している場合は、空のリストと新しいコンピューティングを作成するためのリンクが表示されます。 

 [![新しいコンピューティング インスタンスの作成](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

さまざまな種類の作成の詳細については、以下を参照してください。

| コンピューティングの種類 | 方法 | 
| --- | --- | 
| コンピューティング インスタンス | [Azure Machine Learning コンピューティング インスタンスを作成して管理する](how-to-create-manage-compute-instance.md) | 
| コンピューティング クラスター | [Azure Machine Learning コンピューティング クラスターの作成](how-to-create-attach-compute-cluster.md) | 
| アタッチされた Kubernetes クラスター | [Azure Arc 対応 Kubernetes クラスターをアタッチする](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>必要な環境変数を指定する

コンピューティング ターゲットを選択したら、ジョブのランタイム環境を指定する必要があります。 ジョブ作成 UI では、次の 3 種類の環境がサポートされています。

* キュレーションされた環境
* カスタム環境
* コンテナー レジストリ イメージ 

### <a name="curated-environments"></a>キュレーションされた環境

キュレーションされた環境は、共通の ML ワークロードで使用される、Azure で定義された Python パッケージのコレクションです。 キュレーションされた環境は、ワークスペース内で既定で使用できます。 これらの環境は、キャッシュされた Docker イメージでバックアップされ、実行の準備オーバーヘッドを低減します。 "キュレーションされた環境" ページに表示されるカードには、各環境の詳細が表示されます。 詳細については、「[Azure Machine Learning のキュレーションされた環境](resource-curated-environments.md)」を参照してください。

 [![キュレーションされた環境](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>カスタム環境

カスタム環境は、自分で指定した環境です。 環境を指定するか、既に作成した環境を再利用することができます。 詳細については、[Azure Machine Learning スタジオ (プレビュー) でソフトウェア環境を管理する](how-to-manage-environments-in-studio.md#create-an-environment)方法に関する記事を参照してください。 

### <a name="container-registry-image"></a>コンテナー レジストリ イメージ

Azure Machine Learning のキュレーションされた環境を使用しない場合、または独自のカスタム環境を指定する場合は、[Docker Hub](https://hub.docker.com/) などのパブリック コンテナー レジストリからの Docker イメージを使用できます。 そのイメージがプライベート コンテナー内にある場合は、 **[This is a private container registry]\(これはプライベート コンテナー レジストリです\)** に切り替えます。 プライベート レジストリの場合、Azure がイメージを取得できるように、有効なユーザー名とパスワードを入力する必要があります。 
[![コンテナー レジストリ イメージ](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>ジョブを構成する

環境を指定したら、より多くの設定でジョブを構成できます。 

|フィールド| 説明|
|------| ------|
|ジョブ名| ジョブ名フィールドは、ジョブを一意に識別するために使用されます。 また、ジョブの表示名としても使用されます。 このフィールドの設定は省略可能です。何も入力しない場合、Azure によってジョブの GUID 名が生成されます。 注意: 各ジョブ名は一意である必要があります。|
|実験名| これは Azure Machine Learning スタジオでジョブを整理するのに役立ちます。 各ジョブの実行レコードは、Studio の [実験] タブの対応する実験の下に編成されます。既定では、Azure によって **[既定]** の実験にジョブが配置されます。|
|コード| コンピューターからコード ファイルまたはフォルダーをアップロードでき、ワークスペースの既定の BLOB ストレージからコード ファイルをアップロードすることもできます。 選択を行った後、アップロードされるファイルが Azure に表示されます。 |
|command| 実行するコマンドです。 コマンドライン引数は、コマンドに明示的に書き込むことも、次のセクションで説明するように、中かっこ表記を使用して他のセクション (具体的には **inputs**) から推論することもできます。|
|入力| 入力バインドを指定します。 次の 3 種類の入力をサポートしています。1) Azure Machine Learning の登録されたデータセット、2) ワークスペースの既定の BLOB ストレージ、3) ローカル ファイルのアップロード。 複数の入力を追加できます。 |
|環境変数| 環境変数を設定すると、ジョブの動的な構成を提供できます。 ここで変数と値を追加できます。|
|タグ| 組織に役立つタグをジョブに追加します。|

### <a name="specify-code-and-inputs-in-the-command-box"></a>コマンド ボックスでコードと入力を指定する

#### <a name="code"></a>コード

コマンドは、アップロードされたコード フォルダーのルート ディレクトリから実行されます。 コード ファイルまたはフォルダーを選択すると、アップロードするファイルを確認できます。 エントリ ポイントを含むコードへの相対パスをコピーし、 **[Enter the command to start the job]\(ジョブを開始するコマンドを入力します\)** というラベルのボックスに貼り付けます。 

コードがルート ディレクトリにある場合は、そのコードをコマンド内で直接参照できます。 たとえば、`python main.py` です。

コードがルート ディレクトリに存在しない場合は、相対パスを使用する必要があります。 たとえば、[単語言語モデル](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model)の構造は次のようになります。

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
ここでは、ソース コードは `src` サブディレクトリ内に配置されます。 コマンドは `python ./src/main.py` (これにその他のコマンドライン引数を加えたもの) になります。

[![コマンドでコードを参照する](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>入力

入力バインディングには次の 2 つの方法があります。 

* 入力名: コマンドで入力を使用する場合は、入力名を指定する必要があります。 入力変数を指定するには、`{inputs.input_name}` の形式を使用します。 たとえば、`{inputs.wiki}` です。 その後、`--data {inputs.wiki}` のように、コマンド内で参照できます。
[![コマンドで入力名を参照する](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* パス: クラウドの場所を指定するには、`--data .path` を使用できます。 パスは、 **[Path on compute]\(コンピューティングのパス\)** フィールドに入力するパスです。
[![コマンドで入力パスを参照する](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>**ジョブを開始するコマンド** で、 **[Path on compute]\(コンピューティングのパス\)** 値にピリオドを追加する必要があります。 たとえば、`/data/wikitext-2` は `./data/wikitext-2` になります。

## <a name="review-and-create"></a>確認と作成 

ジョブを構成したら、 **[次へ]** を選択し、 **[レビュー]** ページに移動します。 設定を変更するには、鉛筆アイコンを選択して変更を加えます。 

**YAML 仕様を表示** して、このジョブ構成によって生成された yaml ファイルを確認してダウンロードすることができます。 このジョブ yaml ファイルを使用して、CLI (v2) からジョブを送信できます。 (「[CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する) (プレビュー)](how-to-train-cli.md)」を参照。)[![yaml 仕様の表示](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![Yaml 仕様](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

ジョブを起動するには、 **[作成]** を選択します。 ジョブが作成されると、Azure に実行の詳細ページが表示されます。このページでは、トレーニング ジョブを監視および管理できます。 

## <a name="next-steps"></a>次の手順

* [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)。

* [CLI (v2) を使用してモデルをトレーニングする (ジョブを作成する) (プレビュー)](how-to-train-cli.md)
