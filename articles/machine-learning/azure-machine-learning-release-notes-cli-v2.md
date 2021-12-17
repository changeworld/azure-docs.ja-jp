---
title: CLI (v2) リリース ノート
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI (v2) の最新情報について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: 9619b98ad824de757273cbe38567b5f9418686a3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566144"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Azure Machine Learning CLI (v2) のリリース ノート

この記事では、Azure Machine Learning CLI (v2) の各リリースについて説明します。

__RSS フィード__:ご自身のフィード リーダーに次の URL をコピーして貼り付けることで、このページの更新時に通知を受け取ることができます。`https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v201a6"></a>Azure Machine Learning CLI (v2) v2.0.1a6

- `az ml workspace`
  - [ワークスペース YAML スキーマ](reference-yaml-workspace.md)が更新されました
- `az ml compute`
  - [AmlCompute](reference-yaml-compute-aml.md) と[コンピューティング インスタンス](reference-yaml-compute-instance.md)の YAML スキーマが更新されました
  - `az ml compute attach` を使用したレガシー AKS アタッチのサポートが削除されました。 Azure Arc 対応の Kubernetes アタッチが次のリリースでサポートされる予定です
- `az ml datastore`
  - [Azure blob](reference-yaml-datastore-blob.md)、[Azure ファイル](reference-yaml-datastore-files.md)、[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md)、および [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) データストアの YAML スキーマが更新されました
  - Azure Data Lake Storage Gen1 および Gen2 データストアを作成するためのサポートが追加されました
- `az ml job`
  - [コマンド ジョブ](reference-yaml-job-command.md)と[スイープ ジョブ](reference-yaml-job-sweep.md)の YAML スキーマが更新されました
  - パイプライン ジョブを実行するためのサポートが追加されました ([パイプライン ジョブ YAML スキーマ](reference-yaml-job-pipeline.md))
  - すべてのジョブの種類に対して、ジョブ入力リテラルと入力データ URI のサポートが追加されました
  - すべてのジョブの種類に対して、ジョブ出力のサポートが追加されました
  - `{ <expression> }` から `${{ <expression> }}` への式の構文が変更されました。 詳細については、[Azure ML ジョブを構成するための式の構文](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components)を参照してください
- `az ml environment`
  - [環境 YAML スキーマ](reference-yaml-environment.md)が更新されました
  - Docker ビルド コンテキストから環境を作成するためのサポートが追加されました
- `az ml model`
  - [モデル YAML スキーマ](reference-yaml-model.md)が更新されました
  - コードを使用しないデプロイ シナリオ用のモデルに新しい `model_format` プロパティが追加されました
- `az ml dataset`
  - `az ml data` サブグループの名前が `az ml dataset` に変更されました
  - [データセット YAML スキーマ](reference-yaml-dataset.md)が更新されました
- `az ml component`
  - Azure ML コンポーネントを管理するための `az ml component` コマンドが追加されました
  - コマンド コンポーネントに対するサポートが追加されました ([コマンド コンポーネント YAML スキーマ](reference-yaml-component-command.md))
- `az ml online-endpoint`
  - `az ml endpoint` サブグループが `az ml online-endpoint` および `az ml batch-endpoint` という 2 つの別個のグループに分割されました
  - [オンライン エンドポイント YAML スキーマ](reference-yaml-endpoint-managed-online.md)が更新されました
  - 開発/テスト シナリオ用のローカル エンドポイントのサポートが追加されました
  - ローカル エンドポイントの対話型 VSCode デバッグ サポートが追加されました (`az ml batch-endpoint create/update`に `--vscode-debug` フラグが追加されました)
- `az ml online-deployment`
  - `az ml deployment` サブグループが `az ml online-deployment` および `az ml batch-deployment` という 2 つの別個のグループに分割されました
  - [マネージド オンライン デプロイ YAML スキーマ](reference-yaml-endpoint-managed-online.md)が更新されました
  - Azure Monitor 自動スケーリングとの統合によって自動スケーリングのサポートが追加されました
  - 同じ更新操作で複数のオンライン デプロイ プロパティを更新するためのサポートが追加されました
  - 同じエンドポイントでデプロイに対する同時操作を実行するためのサポートが追加されました
- `az ml batch-endpoint`
  - `az ml endpoint` サブグループが `az ml online-endpoint` および `az ml batch-endpoint` という 2 つの別個のグループに分割されました
  - [バッチ エンドポイント YAML スキーマ](reference-yaml-endpoint-batch.md)が更新されました
  - `traffic` プロパティが削除され、構成可能な既定のデプロイ プロパティに置き換えられました
  - `az ml batch-endpoint invoke` の入力データ URI のサポートが追加されました
  - VNet イングレス (プライベート リンク) のサポートが追加されました
- `az ml batch-deployment`
  - `az ml deployment` サブグループが `az ml online-deployment` および `az ml batch-deployment` という 2 つの別個のグループに分割されました
  - [バッチ デプロイ YAML スキーマ](reference-yaml-deployment-batch.md)が更新されました

## <a name="2021-05-25"></a>2021-05-25

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Azure Machine Learning の CLI (v2) (プレビュー) の発表

Azure CLI に対する `ml` 拡張機能は、Azure Machine Learning の次世代インターフェイスです。 これにより、コマンド ラインからモデルをトレーニングおよびデプロイできます。また、モデルのライフサイクルを追跡しながらデータ サイエンスのスケールアップとスケールアウトを加速する機能もあります。 [インストールして開始する](how-to-configure-cli.md)。
