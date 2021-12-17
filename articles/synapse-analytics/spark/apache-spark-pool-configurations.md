---
title: Apache Spark プールの概念
description: Azure Synapse Analytics での Apache Spark プールのサイズと構成について説明します。
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/19/2021
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: c674813aee7702e9887e909e4a8baf7ace074a2c
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597647"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Apache Spark プールの構成

Spark プールは、Spark インスタンスがインスタンス化されるときにコンピューティング リソースの要件および関連する動作特性を定義するメタデータのセットです。 これらの特性には、名前、ノード数、ノード サイズ、拡大縮小動作、有効期限が含まれますがこれらに限定されません。 Spark プール自体はリソースを使用しません。 Spark プールの作成に伴ってコストは発生しません。 料金は、Spark ジョブがターゲットの Spark プールで実行され、必要に応じて Spark インスタンスがインスタンス化された場合にのみ発生します。

Spark プールの作成方法とそのすべてのプロパティは、[Synapse Analytics の Spark プールの概要](../quickstart-create-apache-spark-pool-portal.md)に関するページでご覧いただけます。

## <a name="isolated-compute"></a>分離コンピューティング

分離コンピューティング オプションでは、物理的なコンピューティング リソースをひとりの顧客の専用にすることによって、信頼されていないサービスから Spark コンピューティング リソースへの接触にセキュリティを追加します。
分離コンピューティング オプションは、コンプライアンスや規制上の要件を満たすなどの理由で、他の顧客のワークロードからの高いレベルの分離を必要とするワークロードに最適です。  
分離コンピューティング オプションが利用できるのは、ノード サイズ XXXLarge (80 vCPU / 504 GB) と次のリージョンに限られます。  分離コンピューティング オプションはプールの作成後に有効または無効にできますが、インスタンスの再起動が必要になる場合があります。  将来、この機能を有効にする場合、分離コンピューティングがサポートされているリージョンで Synapse ワークスペースが確実に作成されるようにします。

* 米国東部
* 米国西部 2
* 米国中南部
* US Gov アリゾナ
* US Gov バージニア州

## <a name="nodes"></a>Nodes

Apache Spark プール インスタンスは、1 つのヘッド ノードと 2 つ以上のワーカー ノードで構成され、1 つの Spark インスタンスには最低 3 つのノードが必要となります。  ヘッド ノードは、Livy、Yarn Resource Manager、Zookeeper、Spark ドライバーなどの追加の管理サービスを実行します。  すべてのノードは、Node Agent や Yarn Node Manager などのサービスを実行します。 すべてのワーカー ノードは、Spark Executor サービスを実行します。

## <a name="node-sizes"></a>ノードのサイズ

Spark プールは、ノード当たり 8 つの仮想コアと 64 GB のメモリを備えた Small コンピューティング ノードから、64 の仮想コアと 432 GB のメモリを備えた XXLarge コンピューティング ノードまで、さまざまなノード サイズで定義できます。 ノードのサイズはプールの作成後に変更できますが、インスタンスの再起動が必要になる場合があります。

|サイズ | 仮想コア | メモリ|
|-----|------|-------|
|Small|4|32 GB|
|Medium|8|64 GB|
|Large|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|
|XXX Large (分離コンピューティング)|80|504 GB|

## <a name="autoscale"></a>自動スケール

Apache Spark プールには、アクティビティの量に基づいてコンピューティング リソースを自動的にスケールアップおよびスケールダウンする機能が用意されています。  自動スケーリング機能が有効になっている場合は、スケーリングするノードの最小数と最大数を設定できます。
自動スケーリング機能が無効になっている場合には、設定されているノード数は固定されたままになります。  この設定はプールの作成後に変更できますが、インスタンスの再起動が必要になる場合があります。

## <a name="automatic-pause"></a>自動一時停止

自動一時停止機能は、設定されたアイドル時間後にリソースを解放し、Apache Spark プールの全体的なコストを削減します。  この機能を有効にすると、アイドル時間を分単位で設定できます。  自動一時停止機能は、自動スケーリング機能とは独立しています。 自動スケーリングが有効か無効かにかかわらず、リソースを一時停止できます。  この設定はプールの作成後に変更できますが、インスタンスの再起動が必要になる場合があります。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark ドキュメント](https://spark.apache.org/docs/2.4.5/)