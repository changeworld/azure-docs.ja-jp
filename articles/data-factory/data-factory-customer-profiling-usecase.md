---
title: 使用事例 - 顧客プロファイル
description: Azure Data Factory を使用して、データ駆動型ワークフロー (パイプライン) を作成し、顧客のゲーム会社をプロファイリングする方法について説明します。
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: spelluru

---
# 使用事例 - 顧客プロファイル
Azure Data Factory は、ソリューション アクセラレータの Cortana Intelligence Suite の実装に使用されている数多くあるサービスの 1 つです。Cortana Intelligence の詳細については、[Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) を参照してください。このドキュメントでは、Azure Data Factory が一般的な分析の問題を解決する方法を理解するのに役立つ簡単な使用事例を説明します。

この簡単な使用事例にアクセスして試すのに必要なものは、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)だけです。「[サンプル](data-factory-samples.md)」記事で説明されている手順に従って、この使用事例を実装しているサンプルをデプロイできます。

## シナリオ
Contoso は、ゲーム機、携帯デバイス、パーソナル コンピューター (PC) など、複数のプラットフォーム向けにゲームを製作するゲーム会社です。プレーヤーがこれらのゲームをプレイすると、使用パターン、ゲームのスタイル、およびユーザーの基本設定を追跡する大量のログ データが生成されます。人口統計、地域、製品データと組み合わせて、Contoso は分析を実行し、プレーヤーのエクスペリエンスを拡張する方法をガイドし、ユーザーをアップグレードおよびゲーム内購入のターゲットにできます。

Contoso の目標は、プレーヤーのゲーム履歴プロファイルに基づいてアップセルおよびクロスセルの機会を識別し、ビジネスの成長を促進して、より優れたエクスペリエンスを顧客に提供する魅力的な機能を追加することです。この使用事例では、ビジネスの一例としてゲーム会社を取り上げています。その会社は、プレーヤーの行動に合わせてゲームを最適化したいと考えています。ここで説明する原則は、商品やサービスに対する顧客の関心を引き、顧客のエクスペリエンスを強化したいと考えているすべての企業に適用できます。

## 課題
## ソリューションの概要
この単純な使用事例は、Azure Data Factory を使用してデータを取得、準備、変換、分析、および発行する方法の例として使用できます。

![エンド ツー エンド ワークフロー](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

この図は、デプロイ後にデータ パイプラインが Azure Portal にどのように表示されるかを示しています。

1. **PartitionGameLogsPipeline** は、未処理のゲーム イベントを BLOB ストレージから読み取り、年、月、日に基づくパーティションを作成します。
2. **EnrichGameLogsPipeline** は、パーティション分割されたゲーム イベントを geo コードの参照データと結合し、IP アドレスを対応する地理的場所にマップすることによってデータを強化します。
3. **AnalyzeMarketingCampaignPipeline** パイプラインは、強化されたデータを使用し、そのデータを広告データと共に処理して、マーケティング キャンペーンの有効性を含む最終的な出力を作成します。

この事例では、Data Factory を使用して、入力データをコピーし、データを変換および処理し、Azure SQL Database に最終データを出力する、アクティビティを調整しています。また、UI を使用して、データ パイプラインのネットワークを視覚化し、管理し、ステータスを監視できます。

## メリット
ゲーム会社は、ユーザー プロファイルの分析を最適化してビジネス目標と一致させることで、迅速に使用状況パターンを収集し、マーケティング キャンペーンの効果を分析できます。

<!---HONumber=AcomDC_0907_2016-->