---
title: Azure SQL Database Edge とは | Microsoft Docs
description: Azure SQL Database Edge について説明します
keywords: sql database edge の紹介, sql database edge とは, sql database edge の概要
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246706"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Azure SQL Database Edge プレビューとは

Azure SQL Database Edge プレビューは、IoT および IoT Edge のデプロイに対応するよう最適化されたリレーショナル データベース エンジンです。 これは、IoT のアプリケーションおよびソリューションに向けて、パフォーマンスの高いデータ ストレージと処理レイヤーを作成する機能を提供します。 Azure SQL Database Edge は、JSON、グラフ、時系列データなどのリレーショナルおよび非リレーショナル データのストリーミング、処理、および分析を行う機能を提供します。これにより、さまざまな最新の IoT アプリケーションに適した選択肢となっています。

Azure SQL Database Edge は、業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、[Microsoft SQL Server データベース エンジン](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json)の最新バージョンに基づいて構築されています。 Azure SQL Database Edge は SQL Server および Azure SQL Database と同じエンジンに基づいて構築されているため、アプリケーションやソリューションの開発がより簡単かつ迅速になる、同一の T-SQL プログラミング サーフェス領域が提供されます。同時に、IoT Edge デバイス、データ センター、クラウドの間で、複雑でないアプリケーションの移植性が実現されています。

## <a name="deployment-models"></a>デプロイ モデル

Azure SQL Database Edge は Azure Marketplace で提供されており、[Azure IoT Edge](../iot-edge/about-iot-edge.md) のモジュールとしてデプロイできます。 詳細については、[Azure SQL Database Edge のデプロイ](deploy-portal.md)に関するページを参照してください。<br>

![SQL Database Edge の概要図](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>SQL Database Edge のエディション

SQL Database Edge は、3 つの異なるエディションまたはソフトウェア プランで提供されています。 これらのエディションの機能セットはまったく同じで、使用権限と、サポートされる CPU とメモリの量についてのみ違いがあります。

   |**プラン**  |**説明**  |
   |---------|---------|
   |Azure SQL Database Edge Developer  |  開発専用 SKU です。各 SQL Database Edge コンテナーは、最大で 4 コアおよび 32 GB メモリに制限されます  |
   |Azure SQL Database Edge    |  運用 SKU です。各 SQL Database Edge コンテナーは、最大で 8 コアおよび 64 GB メモリに制限されます。 |

## <a name="pricing-and-availability"></a>価格と可用性

Azure SQL Database は現在プレビューの段階です。 価格と可用性の詳細については、[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) に関するページを参照してください。

> [!IMPORTANT]
> Azure SQL Database Edge と SQL Server の機能面の違いや、Azure SQL Database Edge のさまざまなオプション間の違いについては、[SQL Database Edge データベースの機能](https://azure.microsoft.com/services/sql-database-edge/)に関するページを参照してください。

## <a name="streaming-capabilities"></a>ストリーミング機能  

Azure SQL Database Edge は、リアルタイム分析や複雑なイベント処理のための、組み込みのストリーミング機能を備えています。 ストリーミング機能は、[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) と同じ構成要素を使用して構築されており、[Azure Stream Analytics on IoT Edge](../stream-analytics/stream-analytics-edge.md) と同様の機能を備えています。

Azure SQL Database Edge のストリーミング エンジンは、低遅延、回復性、帯域幅の効率的な使用、およびコンプライアンスを目的として設計されています。

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>機械学習および人工知能の機能

Azure SQL Database Edge は、オープンな形式の ONNX (Open Neural Network Exchange) ランタイムを統合することによって、機械学習と分析の組み込み機能を提供します。これを使用すると、さまざまなフレームワーク間でディープ ラーニングとニューラル ネットワークのモデルを交換できます。 ONNX の詳細については、[こちら](https://onnx.ai/)をご覧ください。 ONNX ランタイムにより、選択した言語またはツールでモデルを開発する柔軟性が得られます。これらのモデルはその後、SQL Database Edge 内での実行のために、ONNX 形式に変換できます。 詳細については、[SQL Database Edge の ONNX を使用した機械学習と人工知能](onnx-overview.md)に関するページを参照してください。

## <a name="working-with-azure-sql-database-edge"></a>Azure SQL Database Edge の利用

Azure SQL Database Edge は、アプリケーションの開発と管理をより簡単で生産的にします。 ユーザーは、使い慣れたツールやスキルをすべて使用して、IoT Edge のニーズに応じた優れたアプリとソリューションを構築できます。 ユーザーは以下のようなツールを使用して、SQL Database Edge での開発を行えます。

- [Azure portal](https://portal.azure.com/): すべての Azure サービスを管理するための Web ベースのアプリケーションです。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/): SQL Server から SQL Database まで、あらゆる SQL インフラストラクチャを管理するための、無料でダウンロードできるクライアント アプリケーションです。
- [Visual Studio の SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/): SQL Server リレーショナル データベース、SQL データベース、Integration Services パッケージ、Analysis Services データ モデル、および Reporting Services レポートを開発するための、無料でダウンロードできるクライアント アプリケーションです。
- [Azure Data Studio](/sql/azure-data-studio/what-is/): Windows、MacOS、Linux 上で Microsoft ファミリのオンプレミスおよびクラウド データ プラットフォームを使用するデータ プロフェッショナルに向けた、無料でダウンロードできるクロス プラットフォーム データベース ツールです。
- [Visual Studio Code](https://code.visualstudio.com/docs): Windows、macOS、Linux 向けの無料でダウンロードできるオープンソースのコード エディターです。 Microsoft SQL Server、Azure SQL Database、および Azure SQL Data Warehouse のデータを照会するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。


## <a name="next-steps"></a>次のステップ

- 価格と可用性に関連する詳細については、[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) に関するページを参照してください。
- お使いのサブスクリプションに対して Azure SQL Database Edge を有効にするように要求します。
- 使用を開始するには、以下を参照してください。
  - [Azure portal を介して SQL Database Edge をデプロイする](deploy-portal.md)
  - [SQL Database Edge を使用した機械学習と人工知能](onnx-overview.md)
