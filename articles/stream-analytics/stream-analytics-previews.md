---
title: Azure Stream Analytics のプレビュー機能
description: この記事では、現在プレビュー段階にある Azure Stream Analytics の機能を示します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 3f6b46425954d8befaef396c66b023565310ec36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741849"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics のプレビュー機能

この記事は、現在プレビュー段階にある Azure Stream Analytics のすべての機能をまとめたものです。 プレビュー機能を運用環境で使用することはお勧めしません。

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>マネージド ID を使用して SQL Database の出力に対する認証を行う (プレビュー)

Azure Stream Analytics では、Azure SQL Database 出力シンクに対する[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md)がサポートされています。 マネージド ID を使用すると、パスワードの変更による再認証の必要性など、ユーザー ベースの認証方法に伴う制限がなくなります。 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning によって管理されるカスタム ML モデルを使用したリアルタイムのハイ パフォーマンス スコアリング

Azure Stream Analytics は、事前トレーニング済みのカスタム機械学習モデルを活用し、コードの記述が不要なワークフローを使用することでハイパフォーマンスのリアルタイム スコアリングをサポートします。カスタム機械学習モデルは、Azure Machine Learning によって管理され、Azure Kubernetes Service (AKS) または Azure Container Instances (ACI) でホストされます。 プレビューへの[サインアップ](https://aka.ms/asapreview1)

## <a name="c-custom-de-serializers"></a>C# カスタム デシリアライザー
開発者は、Protobuf や XML のほか、あらゆるカスタム形式のデータを処理する目的に Azure Stream Analytics のパワーを活用できます。 [カスタム デシリアライザー](custom-deserializer-examples.md)を C# で実装し、それを使用して、Azure Stream Analytics で受信したイベントを逆シリアル化することができます。

## <a name="extensibility-with-c-custom-code"></a>C# のカスタム コードを使用した拡張性

クラウドまたは IoT Edge に Stream Analytics モジュールを作成する開発者は、カスタム C# 関数を作成または再利用し、[ユーザー定義関数](stream-analytics-edge-csharp-udf-methods.md)を通じて、クエリの中でそれらを直接呼び出すことができます。

## <a name="debug-queries-locally-using-job-diagram-in-visual-studio-code"></a>Visual Studio Code のジョブ ダイアグラムを使用してクエリをローカルでデバッグする

ジョブ ダイアグラムを使用しながらローカルでクエリをテストすることで、各ステップの中間結果セットとメトリックを調べることができます。

## <a name="explore-jobs-in-visual-studio-code"></a>Visual Studio Code でジョブを探す

Visual Studio Code の Stream Analytics Explorer 拡張機能は、Stream Analytics ジョブを管理するための軽量エクスペリエンスを開発者に提供します。 Stream Analytics Explorer では、ジョブの管理、ジョブ ダイアグラムの表示、およびジョブ モニターでのデバッグを簡単に行うことができます。

## <a name="debug-query-steps-in-visual-studio"></a>Visual Studio でのクエリ ステップのデバッグ

Visual Studio 用 Azure Stream Analytics ツールでローカル テストを行う際、中間行セットをデータ ダイアグラムで簡単にプレビューできます。 


## <a name="live-data-testing-in-visual-studio"></a>Visual Studio でのライブ データ テスト

Visual Studio Tools for Azure Stream Analytics ではローカル テスト機能が強化され、イベント ハブや IoT ハブなどのクラウド ソースからのライブ イベント ストリームに対してクエリをテストすることができます。 方法については、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)」をご覧ください。


