---
title: Visual Studio App Center と Azure サービスを使用したモバイル アプリケーションの利用状況とユーザーの動作について
description: ユーザーによるモバイル アプリケーションの利用状況を把握することでビジネス上の適切な意思決定を行うのに役立つ、App Center などのサービスについて学習します。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795179"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>モバイル アプリケーションの利用状況を分析して把握する
アプリケーションをユーザーがどのように使用しているかをどの程度把握していますか。 アプリケーションのアクティブ ユーザーはどのくらいいて、その利用状況は時間の経過と共にどのように変化しているでしょう。 どのような機能が使用されており、また、最もよく使用される機能は何でしょう。 これらのユーザーは何をベースにしていますか。 最新バージョンのアプリケーションを使用しているユーザーはどのくらいいますか。 アプリからビジネス上の成功を得るには、これらのすべての質問を理解しておくことが重要です。 このような利用状況分析に関する質問に答えるには、アプリから利用状況データを収集する必要があります。

データを詳しく調べるほど、アプリケーションを改善してユーザーの満足度を維持する方法をより的確に見つけることができます。 データを使用して実用的な分析情報を見つけ、ユーザーの満足度を維持することが重要です。

## <a name="importance-of-analytics"></a>分析の重要性
- ユーザー、ユーザーがアプリケーションをどのように操作しているか、また、ビジネスを拡大するためにアプリケーションを微調整して優れたエクスペリエンスを提供するのに使用できる情報を**把握する**。
- アプリケーションを市場に投入して顧客により良いサービスを提供する方法について、十分な情報に基づいた意思決定を行うために、利用状況メトリックを**追跡する**。
- アプリケーションのパフォーマンスを**測定する**。
- アプリケーションのどの部分によって価値とパフォーマンスが促進されるかを**把握する**。
- チャーンとリテンション期間の問題に対する**データに基づく分析情報**。

モバイル アプリケーション分析を有効にするには、次のサービスを使用します。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 分析](/appcenter/analytics/)を使用すると、ユーザー セッション、トップ デバイス、OS バージョン、行動分析に関する詳細なレポートや分析情報を使用して重要事項にフォーカスすることで、ユーザーを増やすことができます。 広範なアプリケーション分析により、すべてを追跡するカスタム イベントを簡単に作成できます。

   **主な機能**
   - 使用パターン、ユーザーによる導入、その他のエンゲージメント メトリックを無料で**追跡する**。
   - カスタム イベントを使用して、傾向、ユーザーの行動、およびエンゲージメントを**識別する**。
   - アプリケーションの利用状況 (日単位、週単位、月単位)、セッション、デバイスのプロパティ、およびユーザーの人口統計データに関する、**単一のダッシュボード**での**そのまま使用できるメトリック**と**詳細な分析情報**。
   - 無制限のリテンション期間中、**すべての分析データを Azure に継続的にエクスポートする**。 Azure Blob Storage および Azure Application Insights へのエクスポートがサポートされます。
   - リテンション期間、じょうご分析、コーホートなど、さらに深い分析情報を得るために **Azure Application Insights と統合する**。
   - **1 行の SDK 統合**により、数分以内に使用を開始する。
   - **プラットフォーム サポート** - iOS、Android、macOS、tvOS、Xamarin、React Native、Unity、Cordova。

   **参照**
   - [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center 分析を使ってみる](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor には [Application Insights](/azure/azure-monitor/app/app-insights-overview) が含まれており、これにより、パフォーマンスを最大化してモバイル アプリケーションを監視するためにテレメトリを収集して分析するツールが提供されます。 App Center 分析を使用して Application Insights を活用し、Application Insights へのエクスポートを設定することができます。 Application Insights では、App Center で提供されている分析ツールよりも高度な、アプリケーションからのカスタム イベント テレメトリのクエリ、セグメント化、フィルター、分析を行うことができます。

**主な機能**
   - **[クエリ]** : カスタム イベント テレメトリのクエリを行います。
   - **[フィルター]** : 強力なセグメント化機能を使用してイベント テレメトリをフィルター処理します。
   - **[分析]** : アプリケーションのコンバージョン、リテンション、ナビゲーションのパターンを分析します。
     - **[じょうご]** : コンバージョン レートを分析および監視します。
     - **[リテンション期間]** : アプリケーションで時間の経過と共にどの程度ユーザーを保持できているかを分析します。
     - **[Workbooks]** : 視覚化とテキストを共有可能なレポートに結合します。
     - **[コーホート]** : 特定のユーザーまたはイベントのグループに名前を付けて保存し、他の分析ツールから簡単に参照できるようにします。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [App Center と Application Insights によるモバイル アプリケーションの分析](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Application Insights による利用状況分析](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) には、世界クラスのクラウド接続ゲームを作成するために必要なゲーム サービス、リアルタイム分析、および LiveOps を備えた完全なバックエンド プラットフォームが用意されています。 これらのサービスにより、プレーヤーのエンゲージメント、保持、および収益化に役立つ、ゲームに合わせてスケーリング可能なコスト効率の高い開発ソリューションが、大小のスタジオに提供されます。これにより、ゲーム開発者の開始時における障壁が軽減されます。 PlayFab を使用すると、開発者はインテリジェント クラウドを使用して、ゲームのビルドと操作、ゲーム データの分析、およびゲーム全体のエクスペリエンスの向上を実現できます。

**主な機能**
   - **[監視]** : リアルタイム ダッシュボードを監視します。
   - **[評価]** : 最高のメトリックによってゲームのパフォーマンスを評価します。
   - **[レポート]** : ゲーム マネージャーで表示し、受信トレイに毎日ダウンロードまたは配信できる自動生成レポートにより、ゲームの日単位および月単位のパフォーマンスの概要を確認します。
   - **[A/B テスト]** : 実験を実行し、特定の変数に対する最適な設定を決定します。
   - **[セグメント化]** : プレーヤーの自動グループ化を定義できます。
    
**参照**
- [PlayFab ポータル](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [クイック スタート](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
