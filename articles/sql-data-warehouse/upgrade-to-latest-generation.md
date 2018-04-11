---
title: Azure SQL Data Warehouse の最新世代へのアップグレード | Microsoft Docs
description: Azure SQL Data Warehouse を最新世代の Azure ハードウェアとストレージ アーキテクチャにアップグレードする手順について説明します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Azure SQL Data Warehouse の最新世代へのアップグレード

Azure Portal を使用して、最新世代の Azure ハードウェアとストレージ アーキテクチャを使用するように Azure SQL Data Warehouse をアップグレードします。 アップグレードすることで、パフォーマンスの向上、スケーラビリティの向上、列ストア インデックスの無制限ストレージを利用できます。  

## <a name="applies-to"></a>適用対象
このアップグレードは、[エラスティック用に最適化] パフォーマンス レベルのデータ ウェアハウスに適用されています。  この手順では、データ ウェアハウスのパフォーマンス レベルを [エラスティック用に最適化] から [計算用に最適化] にアップグレードします。 

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に

1. アップグレード対象の [エラスティック用に最適化] データ ウェアハウスが一時停止されている場合は、[データ ウェアハウスを再開します](pause-and-resume-compute-portal.md)。
2. 数分間のダウンタイムに備えます。 
3. アップグレード プロセスでは、すべてのセッションが強制終了され、すべての接続が切断されます。 アップグレードする前に、クエリが完了していることを確認してください。 進行中のトランザクションがある状態でアップグレードを開始すると、ロールバック時間が大幅に長くなる可能性があります。 

## <a name="start-the-upgrade"></a>アップグレードを開始する

1. Azure Portal でデータ ウェアハウスを開き、**[計算用に最適化へのアップグレード]** をクリックします。
2. [計算用に最適化] パフォーマンス レベルの選択肢を確認します。 既定の選択は、アップグレード前の現在のレベルに相当します。
3. パフォーマンス レベルを選択します。 [計算用に最適化] パフォーマンス レベルの料金は、プレビュー期間中の現在は半額になっています。
4. **[アップグレード]** をクリックします。
5. Azure Portal で状態を確認します。
6. データ ウェアハウスがオンラインになるまで待ちます。

## <a name="rebuild-columnstore-indexes"></a>列ストア インデックスを再構築する

データ ウェアハウスがオンラインになると、データを読み込み、クエリを実行することができます。 ただし、バックグラウンド プロセスでデータが新しいハードウェアに移行しているため、最初のうちはパフォーマンスが低い可能性があります。 

データをできるだけ高速に移行するために、列ストア インデックスを再構築することをお勧めします。 インデックスの再構築については、[列ストア インデックスを再構築してセグメントの品質を向上する方法](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順
データ ウェアハウスはオンラインです。 新しいパフォーマンス機能を使用するには、「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」を参照してください。
 