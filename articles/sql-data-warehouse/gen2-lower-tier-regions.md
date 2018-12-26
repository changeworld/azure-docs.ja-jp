---
title: Azure SQL Data Warehouse Gen2 の下位コンピューティング レベルのサポート | Microsoft Docs
description: Azure SQL Data Warehouse Gen2 で下位コンピューティング レベルがサポートされるようになりました
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 12/03/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: d3c42c13e58156013167b8570a8845ce9de08b3b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885457"
---
# <a name="azure-sql-data-warehouse-gen2-support-for-lower-compute-tiers"></a>Azure SQL Data Warehouse Gen2 の下位コンピューティング レベルのサポート

Microsoft は、非常に高速な Azure SQL Data Warehouse Gen2 用に下位コンピューティング レベルを追加することで、要求の厳しいクエリを処理できるデータ ウェアハウスの実行に関するエントリレベルのコスト削減に取り組んでいます。 お客様は、Azure SQL Data Warehouse の優れたパフォーマンス、柔軟性、およびセキュリティ機能を 100 cDWU (Data Warehouse ユニット) から数分で 30,000 cDWU まで拡張できます。 Gen2 のパフォーマンスと柔軟性を下位のコンピューティング レベルで利用できます。 

Microsoft は次世代データ ウェアハウスのエントリ ポイントを下げ、セキュリティで保護された高パフォーマンスなデータ ウェアハウスの利点をすべて評価する必要のある価値重視型のお客様に門戸を開いています。お客様が最適な試用環境を推測する必要はありません。  現在の 500 cDWU のエントリ ポイントではなく、100 cDWU から始められるようになります。  SQL Data Warehouse Gen2 は、一時停止操作と再開操作を引き続きサポートします。また、単なるコンピューティングの柔軟性以外の利点もあります。  Gen2 は、無制限の列ストア ストレージ容量、1 クエリあたり 2.5 倍以上のメモリ、最大 128 の同時クエリ、およびアダプティブ キャッシング機能をサポートしており、同じ料金の Gen1 の同じ Data Warehouse ユニットと比較して、平均 5 倍以上のパフォーマンスを実現しています。  Gen2 では地理的冗長バックアップが標準であり、保証付きデータ保護が組み込まれています。 Azure SQL Data Warehouse Gen2 は必要に応じてスケールすることができます。

現在、ポータルは下位レベルの Gen2 インスタンスのデプロイまたはスケールをサポートしていません。 Microsoft はこの機能を有効にするために努力しています。この新しいレベルを利用したい場合は、暫定的に[チケットを送信](sql-data-warehouse-get-started-create-support-ticket.md)してください。

## <a name="getting-started-with-azure-sql-data-warehouse-gen2"></a>Azure SQL Data Warehouse Gen2 の概要 

次世代のデータ ウェアハウスの柔軟性とパフォーマンスを体験する場合、新しい Gen2 インスタンスをデプロイするか、既存の Gen1 データ ウェアハウス インスタンスをアップグレードするかを選択できます。 

[Azure SQL Data Warehouse Compute Optimized Gen2 レベル](https://azure.microsoft.com/services/sql-data-warehouse/?v=17.44)をお試しください。
[Azure SQL Warehouse Compute Optimized Gen1 を Gen2 にアップグレードするにはこちら](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)を参照してください。動作している Azure SQL Data Warehouse Gen2 については、こちらの [Microsoft Mechanics ビデオ](https://www.youtube.com/watch?v=Ap8I3UZonzI&feature=youtu.be)をご覧ください。


## <a name="supported-regions-for-lower-compute-tiers"></a>下位コンピューティング レベルのサポートされているリージョン

- 米国東部 1 
- 米国東部 2
- 西ヨーロッパ
- 北ヨーロッパ
- 米国西部 2
- 東南アジア
- 米国中南部
- 米国中央部 
- 東アジア
- 東日本
- インド中部
- オーストラリア東部
- カナダ中部
- 西日本 
- カナダ中部

## <a name="next-steps"></a>次の手順

- [詳細情報](upgrade-to-latest-generation.md): SQL Data Warehouse をアップグレードしてパフォーマンスを最適化する方法 
