---
title: Visual Studio App Center と Azure サービスを使用したモバイル アプリケーションの利用状況とユーザーの動作について
description: ユーザーによるモバイル アプリケーションの利用状況を把握することでビジネス上の適切な意思決定を行うのに役立つ、App Center などのサービスについて学習します。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241065"
---
# <a name="analyze-and-understand-mobile-application-use"></a>モバイル アプリケーションの使用を分析して把握する
アプリケーションをユーザーがどのように使用しているかをどの程度把握していますか。 アプリケーションのアクティブ ユーザーはどのくらいいて、その利用状況は時間の経過と共にどのように変化しているでしょう。 どのような機能が使用されており、また、最もよく使用される機能は何でしょう。 これらのユーザーは何をベースにしていますか。 最新バージョンのアプリケーションを使用しているユーザーはどのくらいいますか。 アプリからビジネス上の成功を得るには、これらのすべての質問を理解しておくことが重要です。 このような利用状況分析に関する質問に答えるには、アプリから利用状況データを収集する必要があります。

データを詳しく調べるほど、アプリケーションを改善してユーザーの満足度を維持する方法を見つけられる可能性は高くなるでしょう。 データを使用して実用的な分析情報を見つけ、ユーザーの満足度を維持することが重要です。

## <a name="importance-of-analytics"></a>分析の重要性
- ユーザー、ユーザーがアプリケーションをどのように操作しているか、また、ビジネスを拡大するためにアプリケーションを微調整して優れたエクスペリエンスを提供するのに使用できる情報を把握する。
- アプリケーションを市場に投入して顧客により良いサービスを提供する方法について、十分な情報に基づいた意思決定を行うために、利用状況メトリックを追跡する。
- アプリケーションのパフォーマンスを測定する。
- アプリケーションのどの部分によって価値とパフォーマンスが促進されるかを把握する。
- チャーンとリテンション期間の問題に対するデータに基づく分析情報を得る。

モバイル アプリケーション分析を有効にするには、次のサービスを使用します。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 分析](/appcenter/analytics/)を使用すると、重要事項にフォーカスしてユーザーを増やすことができます。 この分析は、ユーザー セッション、トップ デバイス、OS バージョン、行動分析に関する詳細なレポートや分析情報を提供します。 広範なアプリケーション分析により、すべてを追跡するカスタム イベントを簡単に作成できます。

   **主な機能**
   - 使用パターン、ユーザーによる導入、その他のエンゲージメント メトリックを無料で追跡する。
   - カスタム イベントを使用して、傾向、ユーザーの行動、およびエンゲージメントを識別する。
   - アプリケーションの利用状況 (日単位、週単位、月単位)、セッション、デバイスのプロパティ、およびユーザーの人口統計データに関する、単一のダッシュボードでのそのまま使用できるメトリックと詳細な分析情報を入手する。
   - 無制限のリテンション期間中、すべての App Center 分析データを Azure に継続的にエクスポートする。 App Center 分析では、Azure Blob Storage および Azure Application Insights へのエクスポートがサポートされます。
   - リテンション期間、じょうご分析、コーホートなど、さらに深い分析情報を得るために Azure Application Insights と統合する。
   - 1 行の SDK 統合を使って、数分以内に使用を開始する。
   - iOS、Android、macOS、tvOS、Xamarin、React Native、Unity、および Cordova のプラットフォーム サポートを得る。

   **参照**
   - [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center 分析を使ってみる](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor には [Application Insights](/azure/azure-monitor/app/app-insights-overview) が含まれ、テレメトリを収集して分析するツールが用意されています。これによりパフォーマンスを最大化し、モバイル アプリケーションを監視できます。 App Center 分析を使用して Application Insights を活用し、Application Insights へのエクスポートを設定できます。 Application Insights では、App Center で提供されている分析ツールよりも高度な、アプリケーションからのカスタム イベント テレメトリのクエリ、セグメント化、フィルター、分析を行うことができます。

**主な機能**
   - カスタム イベント テレメトリのクエリを行います。
   - 強力なセグメント化機能を使用してイベント テレメトリをフィルター処理します。
   - アプリケーションのコンバージョン、リテンション、ナビゲーションのパターンを分析します。 使用できるもの:
     - じょうご : コンバージョン レートを分析および監視します。
     - リテンション期間: アプリケーションで時間の経過と共にどの程度ユーザーを保持できているかを分析します。
     - Workbooks: 視覚化とテキストを共有可能なレポートに結合します。
     - コーホート: 特定のユーザーまたはイベントのグループに名前を付けて保存し、他の分析ツールから簡単に参照できるようにします。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [App Center と Application Insights によるモバイル アプリケーションの分析](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Application Insights で App Center 分析を使用する](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) には、世界クラスのクラウド接続ゲームを作成するために必要なゲーム サービス、リアルタイム分析、および LiveOps を備えた完全なバックエンド プラットフォームが用意されています。 これらのサービスにより、ゲーム開発者の開始時における障壁が軽減され、 ゲームに合わせてスケーリング可能なコスト効率の高い開発ソリューションが、大小のスタジオに提供されます。 これらのサービスは、スタジオにおけるプレーヤーのエンゲージメント、保持、および収益化に役立ちます。 PlayFab を使用すると、開発者がインテリジェント クラウドを使用して、ゲームのビルドと操作、ゲーム データの分析、およびゲーム全体のエクスペリエンスの向上を実現できます。

**主な機能**
   - リアルタイム ダッシュボードを監視します。
   - 最高のメトリックによってゲームのパフォーマンスを評価します。
   - 自動生成レポートにより、ゲームの日単位および月単位のパフォーマンスの概要を確認します。 ゲーム マネージャーでレポートを表示し、受信トレイに毎日ダウンロードまたは配信できます。
   - A/B テストを使用して実験を実行し、特定の変数に対する最適な設定を決定します。
   - プレーヤーに対してセグメント化を使用し、プレーヤーの自動グループ化を定義します。
    
**参照**
- [PlayFab ポータル](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [クイック スタート](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
