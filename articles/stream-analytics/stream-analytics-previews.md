---
title: Azure Stream Analytics のプレビュー機能
description: この記事では、現在プレビュー段階にある Azure Stream Analytics の機能を示します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: d07a2364cbd2b63ef9e38387b6e915f7e63676ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012157"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics のプレビュー機能

この記事は、現在プレビュー段階にある Azure Stream Analytics のすべての機能をまとめたものです。 プレビュー機能を運用環境で使用することはお勧めしません。

## <a name="public-previews"></a>パブリック プレビュー

以下の機能はパブリック プレビュー段階です。 これらの機能は現在でも利用できますが、運用環境では使用しないでください。

### <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>マネージド ID を使用して SQL Database の出力に対する認証を行う (プレビュー)

Azure Stream Analytics では、Azure SQL Database 出力シンクに対する[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md)がサポートされています。 マネージド ID を使用すると、パスワードの変更による再認証の必要性など、ユーザー ベースの認証方法に伴う制限がなくなります。 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning によって管理されるカスタム ML モデルを使用したリアルタイムのハイ パフォーマンス スコアリング

Azure Stream Analytics は、事前トレーニング済みのカスタム機械学習モデルを活用し、コードの記述が不要なワークフローを使用することでハイパフォーマンスのリアルタイム スコアリングをサポートします。カスタム機械学習モデルは、Azure Machine Learning によって管理され、Azure Kubernetes Service (AKS) または Azure Container Instances (ACI) でホストされます。 プレビューへの[サインアップ](https://aka.ms/asapreview1)

### <a name="c-custom-de-serializers"></a>C# カスタム デシリアライザー
開発者は、Protobuf や XML のほか、あらゆるカスタム形式のデータを処理する目的に Azure Stream Analytics のパワーを活用できます。 [カスタム デシリアライザー](custom-deserializer-examples.md)を C# で実装し、それを使用して、Azure Stream Analytics で受信したイベントを逆シリアル化することができます。

### <a name="extensibility-with-c-custom-code"></a>C# のカスタム コードを使用した拡張性

クラウドまたは IoT Edge に Stream Analytics モジュールを作成する開発者は、カスタム C# 関数を作成または再利用し、[ユーザー定義関数](stream-analytics-edge-csharp-udf-methods.md)を通じて、クエリの中でそれらを直接呼び出すことができます。

### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio でのクエリ ステップのデバッグ

Visual Studio 用 Azure Stream Analytics ツールでローカル テストを行う際、中間行セットをデータ ダイアグラムで簡単にプレビューできます。 


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio でのライブ データ テスト

Visual Studio Tools for Azure Stream Analytics ではローカル テスト機能が強化され、イベント ハブや IoT ハブなどのクラウド ソースからのライブ イベント ストリームに対してクエリをテストすることができます。 方法については、「[Visual Studio の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md)」をご覧ください。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics 用の Visual Studio Code

Visual Studio Code で Azure Stream Analytics のジョブを作成できます。 [VS Code の使用に関するチュートリアル](./quick-create-visual-studio-code.md)をご覧ください。

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code でのライブ データを使用したローカル テスト

Azure にジョブを送信する前に、ローカル コンピューター上でライブ データに対してクエリをテストできます。 それぞれのテストのイテレーションにかかる時間は平均して 2 秒から 3 秒なので、きわめて効率的な開発プロセスが実現します。

## <a name="other-previews"></a>その他のプレビュー

ご要望に応じて、次の機能もプレビューとして提供されています。

### <a name="support-for-azure-stack"></a>Azure Stack のサポート
この機能は Azure IoT Edge ランタイムで有効になり、Azure Stack 上で実行するローカルの入出力 (Event Hubs、IoT Hub、Blob Storage など) のネイティブ サポートなど、Azure Stack のカスタム機能を活用します。 この新しい統合により、データを生成場所の近くで分析できるハイブリッド アーキテクチャを構築し、待機時間を短縮して分析情報を最大限に活用することができます。
この機能により、データを生成場所の近くで分析できるハイブリッド アーキテクチャを構築し、待ち時間を短縮して分析情報を最大限に活用することができます。 このプレビューは[サインアップ](https://aka.ms/asapreview1)が必要です。