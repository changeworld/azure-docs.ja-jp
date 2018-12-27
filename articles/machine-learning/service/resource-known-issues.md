---
title: Azure Machine Learning サービスの既知の問題とトラブルシューティング
description: 既知の問題、回避策、トラブルシューティングの一覧を取得する
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249419"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning サービスの既知の問題とトラブルシューティング
 
この記事は、Azure Machine Learning サービスの使用時に発生したエラーや障害を見つけて修正するのに役立ちます。 

## <a name="sdk-installation-issues"></a>SDK のインストールに関する問題

**エラー メッセージ: [Cannot uninstall 'PyYAML']\('PyYAML' をアンインストールできません\)** 

PyYAML は distutils によってインストールされるプロジェクトです。 したがって、部分的なアンインストールが行われた場合、それに属しているファイルを正確に判別できません。 このエラーを無視して SDK のインストールを続行するには、次を使用します。
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="image-building-failure"></a>イメージ ビルド エラー

Web サービスのデプロイ時のイメージ ビルド エラー。 回避策は、イメージ構成のために pip の依存関係として "pynacl==1.2.1" を Conda ファイルに追加することです。  

## <a name="pipelines"></a>パイプライン
スクリプトまたはパラメーターを変更せずに PythonScriptStep を連続して呼び出すとエラーが発生します。 回避策としては PipelineData オブジェクトを再構築してください。

## <a name="fpgas"></a>FPGA
FPGA クォータを要求して承認されるまでは、FPGA にモデルをデプロイできません。 アクセスを要求するには、クォータ要求フォーム https://aka.ms/aml-real-time-ai に入力します。

## <a name="databricks"></a>Databricks

Databricks と Azure Machine Learning の問題。

1. Databricks クラスターの推奨事項:
   
   Python 3 を使用し、Azure Databricks クラスターを v4.x として作成します。 同時実行性の高いクラスターをお勧めします。
 
1. インストールするパッケージを増やすと、Databricks で AML SDK インストールのエラーが発生します。

   `psutil upgrade libs` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、lib バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連し、AML SDK には関連しません。 例:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="diagnostic-logs"></a>診断ログ
サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 ここにログ ファイルが保存されます。

## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="get-more-support"></a>さらにサポートが必要な場合

サポート要求を送信し、テクニカル サポートやフォーラムなどからサポートを得ることができます。 [詳細情報](support-for-aml-services.md)
