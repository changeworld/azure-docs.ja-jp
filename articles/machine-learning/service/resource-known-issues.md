---
title: 既知の問題とトラブルシューティング
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service に関するの既知の問題、回避策、トラブルシューティングの一覧を示します。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c327d973170a4556471663c3bea9dcae9b5794fb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238613"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning サービスの既知の問題とトラブルシューティング

この記事は、Azure Machine Learning サービスの使用時に発生したエラーや障害を見つけて修正するのに役立ちます。

## <a name="sdk-installation-issues"></a>SDK のインストールに関する問題

**エラー メッセージ:"Cannot uninstall 'PyYAML'" ('PyYAML' をアンインストールできません)**

Azure Machine Learning SDK for Python:PyYAML は distutils によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。
```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Azure Machine Learning コンピューティングの作成に関する問題

まれにですが、GA リリースの前に Azure portal から Azure Machine Learning ワークスペースを作成したユーザーが、そのワークスペースに Azure Machine Learning コンピューティングを作成できないことがあります。 サービスにサポート要求を送るか、ポータルまたは SDK を使って新しいワークスペースを作成してすぐにブロックを解除することができます。

## <a name="image-building-failure"></a>イメージ ビルド エラー

Web サービスのデプロイ時のイメージ ビルド エラー。 回避策は、イメージ構成のために pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加することです。

## <a name="deployment-failure"></a>デプロイの失敗

<Signals.SIGKILL: 9> で 'DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' が失われたことを確認した場合は、デプロイで使用されている VM の SKU を、メモリがより多い SKU に変更してください。

## <a name="fpgas"></a>FPGA
FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。

## <a name="databricks"></a>Databricks

Databricks と Azure Machine Learning の問題。

1. インストールするパッケージを増やすと、Databricks で AML SDK インストールのエラーが発生します。

   `psutil` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、lib バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連しており、Azure Machine Learning service SDK には関係ありません。他のライブラリでも発生する可能性があります。 例:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   または、Python ライブラリでのインストールの問題が発生し続けている場合は、初期化スクリプトを使用できます。 このアプローチは、公式にサポートされているアプローチではありません。 [こちらのドキュメント](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)をご覧ください。

2. Automated Machine Learning を Databricks で使用しているときに、実行をキャンセルして新しい実験の実行を開始する場合は、Azure Databricks クラスターを再起動してください。

3. Automated Machine Learning の設定で、10 を超えるイテレーションがある場合は、実行を送信するときに、show_output を False に設定してください。


## <a name="azure-portal"></a>Azure ポータル
SDK またはポータルで共有リンクからワークスペースを直接表示した場合、拡張機能のサブスクリプション情報を含む通常の概要ページを表示できません。 また、別のワークスペースに切り替えることもできません。 別のワークスペースを表示する必要がある場合の回避策としては、[Azure portal](https://portal.azure.com) に直接移動し、ワークスペース名を検索してください。

## <a name="diagnostic-logs"></a>診断ログ
サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。
ここにログ ファイルが保存されます。

## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="get-more-support"></a>さらにサポートが必要な場合

サポート要求を送信し、テクニカル サポートやフォーラムなどからサポートを得ることができます。 [詳細情報](support-for-aml-services.md)
