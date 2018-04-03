---
title: 'Azure ML Workbench リリース ノート: スプリント 2 - 2017 年 12 月'
description: このドキュメントでは、Azure ML のスプリント 2 リリースの更新について詳細に説明します
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-2---december-2017"></a>スプリント 2 - 2017 年 12 月 

#### <a name="version-number-01171115263"></a>バージョン番号: 0.1.1711.15263

>バージョン番号を調べる方法については、[こちら](known-issues-and-troubleshooting-guide.md)をご覧ください。

Azure Machine Learning Workbench の 3 回目の更新へようこそ。 この更新には、Workbench アプリ、コマンド ライン インターフェイス (CLI)、およびバックエンド サービスの改良が含まれます。 当社に長所と欠点を送信していただき誠にありがとうございます。 以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 

## <a name="notable-new-features"></a>注目すべき新機能
- [データ ソースとしての SQL Server および Azure SQL DB のサポート](data-prep-appendix2-supported-data-sources.md#types) 
- [MMLSpark を使用した GPU サポートのある Spark でのディープ ラーニング](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [デプロイの時点で Azure IoT Edge デバイスと互換性のあるすべての AML コンテナー (余分な手順は不要)](http://aka.ms/aml-iot-edge-blog)
- Azure Portal で利用可能な登録済みモデル リストと詳細ビュー
- ユーザー名/パスワード ベースのアクセスに加えて、SSH キーに基づく認証を使用したコンピューティング ターゲットへのアクセス。 
- データ準備エクスペリエンスでの新しいパターン頻度インスペクター。 

## <a name="detailed-updates"></a>詳細な更新
以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

### <a name="installer"></a>インストーラー
- インストーラーは自動的に更新するので、ユーザーが再インストールしなくてもバグ修正と新機能をサポートできます

### <a name="workbench-authentication"></a>Workbench の認証
- 認証システムに関して複数の修正が行われました。 ログインでまだ問題が発生する場合はお知らせください。
- UI が変更されて、Proxy Manager の設定を見つけやすくなりました。

### <a name="workbench"></a>ワークベンチ
- 読み取り専用のファイル ビューの背景が明るい青色になりました
- [編集] ボタンが右側に移動されてわかりやすくなりました。
- "dsource"、"dprep"、"ipynb" のファイル形式を、生テキスト形式でレンダリングできるようになりました
- Workbench の編集エクスペリエンスが新しくなり、スクリプトの編集には外部の IDE を使い、Workbench はリッチな編集エクスペリエンスを持つファイル形式 (Notebook、データ ソース、データ準備パッケージなど) の編集だけに使うように案内されるようになりました
- ユーザーがアクセス権を持つワークスペースとプロジェクトの一覧の読み込みが、大幅に高速化されました

### <a name="data-preparation"></a>データの準備 
- パターン頻度インスペクターに、列の文字列パターンが表示されます。 これらのパターンを使って、データをフィルター処理することもできます。 値のカウント インスペクターと似たビューが表示されます。 違いは、パターン頻度では、一意のデータの数ではなく、データの一意のパターンの数が示されることです。 また、特定のパターンに一致するすべての行を含めたり、除外したりすることもできます。

![製品数でのパターン頻度インスペクターの画像](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- "例による列の派生" 変換でのレビューのためのエッジ ケース推奨の間のパフォーマンスの向上

- [データ ソースとしての SQL Server および Azure SQL DB のサポート](data-prep-appendix2-supported-data-sources.md#types) 

![新しい SQL Server データ ソースの作成の画像](media/release-notes-sprint-2/sql-server-data-source.png)

- 行と列の数の "概略" ビューが有効になりました

![行と列の数の概略の画像](media/release-notes-sprint-2/row-col-count.png)

- データの準備がすべてのコンピューティング コンテキストで有効になりました
- SQL Server データベースを使うデータ ソースが、すべてのコンピューティング コンテキストで 有効になりました
- データ準備のグリッド列をデータ型でフィルター処理できます
- 複数の列を日付に変換するときの問題を修正しました
- ユーザーが詳細モードで出力列の名前を変更した場合、ユーザーが例による列の派生でソースとして出力列を選択できた問題を修正しました。

### <a name="job-execution"></a>ジョブの実行
次の手順により、SSH キー ベースの認証を使って remotedocker 型またはクラスター型のコンピューティング ターゲットを作成してアクセスできるようになりました。
- CLI で次のコマンドを使ってコンピューティング ターゲットをアタッチします

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] コマンドの -k (または --use-azureml-ssh-key) オプションは、SSH キーを生成して使うことを指定します。

- Azure ML Workbench が公開キーを生成してコンソールに出力します。 同じユーザー名を使ってコンピューティング ターゲットにログインし、この公開キーで ~/.ssh/authorized_keys ファイルを追加します。

- このコンピューティング ターゲットを準備して実行に使うことができ、Azure ML Workbench は認証にこのキーを使います。  

コンピューティング ターゲットの作成について詳しくは、「[Azure Machine Learning 実験サービスの構成](experimentation-service-configuration.md)」をご覧ください。

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017) のサポートが追加されました。 

### <a name="command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI)
- 追加された `az ml datasource create` コマンドを使って、コマンド ラインからデータ ソース ファイルを作成できます

### <a name="model-management-and-operationalization"></a>モデルの管理と運用化
- [すべての AML コンテナーは、運用化の時点で Azure IoT Edge デバイスと互換性があります (余分な手順は不要)](http://aka.ms/aml-iot-edge-blog) 
- O16n CLI でエラー メッセージの改良
- モデル管理ポータル UX のバグ修正  
- 詳細ページのモデル管理属性での一貫性のある大文字と小文字の使い分け
- 60 秒に設定されたリアルタイム スコアリング呼び出しのタイムアウト
- Azure Portal で利用可能な登録済みモデル リストと詳細ビュー

![ポータルでのモデルの詳細](media/release-notes-sprint-2/model-list.jpg)

![ポータルでのモデルの概要](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- [GPU がサポート](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)された Spark でのディープ ラーニング
- 容易なリソース デプロイのための Resource Manager テンプレートのサポート
- SparklyR エコシステムのサポート
- [AZTK の統合](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>サンプル プロジェクト
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) および [MMLSpark](https://github.com/Azure/mmlspark) サンプルが、新しい Azure ML SDK バージョンで更新されました

## <a name="breaking-changes"></a>重大な変更
- `az ml computetarget attach` の `--type` スイッチがサブコマンドにレベル上げされました。 

    - `az ml computetarget attach --type remotedocker` は `az ml computetarget attach remotedocker` になりました
    - `az ml computetarget attach --type cluster` は `az ml computetarget attach cluster` になりました
