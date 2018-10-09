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
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162748"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure Machine Learning サービスの既知の問題とトラブルシューティング
 
この記事は、Azure Machine Learning サービスの使用時に発生したエラーや障害を見つけて修正するのに役立ちます。 


## <a name="databricks"></a>Databricks

Databricks と Azure Machine Learning の問題。

1. Databricks クラスターの推奨事項:
   
   Python 3 を使用し、Azure Databricks クラスターを v4.x として作成します。 同時実行性の高いクラスターをお勧めします。
 
1. インストールするパッケージを増やすと、Databricks で AML SDK インストールのエラーが発生します。

   `psutil upgrade libs` のようなパッケージでは、競合が発生することがあります。 インストール エラーを回避するには、lib バージョンを止めてパッケージをインストールします。 この問題は Databricks に関連し、AML SDK には関連しません。 例:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>診断情報を収集する
サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 ここにログ ファイルが保存されます。

## <a name="resource-quotas"></a>リソース クォータ

Azure Machine Learning の使用時に扱うことがあるリソース クォータの詳細は[こちら](how-to-manage-quotas.md)にあります。

## <a name="get-more-support"></a>さらにサポートが必要な場合

サポート要求を送信し、テクニカル サポートやフォーラムなどからサポートを得ることができます。 [詳細情報](support-for-aml-services.md)
