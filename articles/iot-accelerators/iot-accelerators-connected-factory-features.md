---
title: コネクテッド ファクトリ ソリューションの機能 - Azure | Microsoft Docs
description: コネクテッド ファクトリ事前構成済みソリューションの機能の概要。
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: dobett
ms.openlocfilehash: 7ee86a59e7c47406ea1a890f1a9d1eb2ed4ad772
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
---
# <a name="what-is-azure-iot-suite-connected-factory"></a>Azure IoT Suite コネクテッド ファクトリとは

コネクテッド ファクトリは Microsoft の Azure Industrial IoT 参照アーキテクチャの実装で、オープンソース ソリューションとしてパッケージ化されています。 商用製品の開始点として使用できます。 コネクテッド ファクトリ ソリューションの事前構築済みバージョンを [Azure IoT Suite](https://www.azureiotsuite.com/#solutions/types/CF) から Azure サブスクリプションにデプロイできます。

![コネクテッド ファクトリ ソリューションのダッシュボード](./media/iot-accelerators-connected-factory-features/dashboard.png)

コネクテッド ファクトリには、次のような機能があります。

## <a name="industrial-device-interoperability"></a>産業用デバイスの相互運用性

- OPC UA インターフェイスを使用して産業用資産に接続します。
- (Docker コンテナーで OPC UA サーバーを実行している) シミュレートされた生産ラインを使用して、そこからライブ テレメトリを表示します。
- クラウド ダッシュボードから OPC UA サーバーの OPC UA 情報モデルを参照します。

## <a name="remote-management"></a>リモート管理

- クラウド ダッシュボードから OPC UA 資産を構成します (メソッドの呼び出し、データの読み取り、書き込み)。
- クラウド ダッシュボードで、OPC UA アセットからの利用統計情報の発行と発行の取り消しを行います。

## <a name="cloud-dashboard"></a>クラウド ダッシュボード

- クラウド ダッシュボードでテレメトリ プレビューを直接表示します。
- Time Series Insights エクスプローラー ダッシュボードを使用して、利用統計情報の傾向を表示し、相関関係を作成します。
- クラウド ダッシュボードから、計算した総合設備効率 (OEE) と主要業績評価指標 (KPI) を参照します。
- 産業用資産階層をツリー トポロジと対話型マップに表示します。
- クラウド ダッシュボードからアラートを表示して確認し、閉じます。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) は、大量の時系列データの保存、視覚化、クエリを目的として構築されています。 コネクテッド ファクトリはこのサービスを活用します。
- コネクテッド ファクトリはこのサービスと統合されているので、デバイス データの詳細なリアルタイム分析を実行できます。

## <a name="rules-and-alerts"></a>ルールとアラート

[しきい値に基づくアラート ルールを構成](iot-accelerators-connected-factory-configure.md)します。

## <a name="end-to-end-security"></a>エンドツーエンドのセキュリティ

- ロールベースのアクセス制御 (RBAC) を使用してユーザーのセキュリティ アクセス許可を構成します。
- OPC UA 認証 (X.509 証明書を使用) とセキュリティ トークンを使用してエンドツーエンドの暗号化が実装されます。

## <a name="customizability"></a>カスタマイズ性

- ソリューションを[カスタマイズ](../iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md)して、独自のビジネス要件を満たします。
- 完全なソリューション ソース コードは GitHub にあります。 [コネクテッド ファクトリ事前構成済みソリューション](https://github.com/Azure/azure-iot-connected-factory)のリポジトリをご覧ください。

## <a name="next-steps"></a>次の手順

コネクテッド ファクトリ事前構成済みソリューションの詳細については、次の記事をご覧ください。

* [コネクテッド ファクトリ事前構成済みソリューションのチュートリアル](iot-accelerators-connected-factory-sample-walkthrough.md)
* [コネクテッド ファクトリのゲートウェイをデプロイする]( iot-accelerators-connected-factory-gateway-deployment.md)
