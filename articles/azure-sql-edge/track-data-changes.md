---
title: Azure SQL Edge (プレビュー) でのデータ変更の追跡
description: Azure SQL Edge (プレビュー) での変更の追跡と変更データ キャプチャについて説明します
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3e3a6c3e171383d1812f63b945735d1b82f70b9c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235096"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) でのデータ変更の追跡

Azure SQL Edge では、データベースのデータに対する変更を追跡する 2 つの SQL Server 機能: [Change Tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) と[変更データ キャプチャ](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)がサポートされています。 これらの機能では、データベース内のユーザー テーブルに対して行われた DML の変更 (挿入操作、更新操作、および削除操作) をアプリケーションで特定できます。 変更データ キャプチャと変更の追跡は、同じデータベースに対して有効にすることができます。特別な配慮は必要ありません。

データベースで変更されたデータをクエリする機能は、一部のアプリケーションの効率を高めるための重要な要件です。 一般に、データ変更を確認するには、アプリケーション開発者がトリガー、timestamp 列、および追加のテーブルを組み合わせて使用することで、カスタムの追跡方法をアプリケーションに実装する必要があります。 通常、このようなアプリケーションを作成するには実装に非常に手間がかかり、スキーマの更新も必要になり、多くの場合、パフォーマンスのオーバーヘッドが増加します。 IoT ソリューションのケースにおいて、データをエッジからクラウドまたはデータセンターに定期的に移動する必要がある場合は、変更の追跡が非常に役立ちます。 これを使用することで、最後の同期からの差分変更のみを迅速かつ効果的に照会し、それらの変更をクラウドまたはデータセンター ターゲットにアップロードできます。 Change Tracking と変更データ キャプチャを使用する利点の詳細については、「[変更データ キャプチャまたは変更の追跡を使用する利点](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)」を参照してください。 

Change Tracking と変更データ キャプチャの機能の違いを理解するには、「[変更データ キャプチャと変更の追跡の機能の違い](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)」を参照してください

## <a name="change-data-capture"></a>変更データ キャプチャ

変更データ キャプチャの動作のしくみについて詳しくは、[変更データ キャプチャの概要](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)に関する記事を参照してください。

変更データ キャプチャを有効または無効にする方法については、[変更データ キャプチャの有効化と無効化](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)に関する記事を参照してください

変更データ キャプチャを管理および監視する方法については、[変更データ キャプチャの管理と監視](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)に関する記事を参照してください

変更されたデータを照会および処理する方法については、[変更データの処理](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)に関する記事を参照してください

## <a name="change-tracking"></a>変更の追跡

Change Tracking の動作のしくみについて詳しくは、[Change Tracking の概要](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)に関する記事を参照してください。

Change Tracking を有効または無効にする方法については、[Change Tracking の有効化と無効化](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)に関する記事を参照してください

Change Tracking を管理および監視する方法については、[Change Tracking の管理と監視](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)に関する記事を参照してください

変更されたデータを照会および処理する方法については、[変更データの処理](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)に関する記事を参照してください

## <a name="temporal-tables"></a>テンポラル テーブル

SQL Server の Change Tracking 機能および変更データ キャプチャ機能のサポートに加えて、Azure SQL Edge では SQL Server のテンポラル テーブル機能もサポートされています。 テンポラル テーブル (システム バージョン管理されたテンポラル テーブルとも呼ばれます) は、現時点の正しいデータのみではなく、テーブルに保存されていた任意の時点のデータに関する情報の提供が組み込みでサポートされたデータベース機能です。

システム バージョン管理されたテンポラル テーブルは、データ変更の履歴を完全に保持し、特定の時点の分析を簡単に実行できるよう設計されたユーザー テーブルの一種です。 各行の有効期間はシステム (つまりデータベース エンジン) によって管理されているため、この種類のテンポラル テーブルは、システム バージョン管理されたテンポラル テーブルと呼ばれます。

すべてのテンポラル テーブルには、それぞれに datetime2 データ型が明示的に定義されている 2 つの列があります。 これらの列は、期間列と呼ばれます。 これら期間列は、行が変更されるたびに各行の有効期間を記録するためにシステムのみに使用されます。

テンポラル テーブルには、これらの期間列に加え、ミラー化されたスキーマを使用する別のテーブルへの参照も含まれています。 システムでは、このテーブルを使用して、テンポラル テーブルの行が更新または削除されるたびに、行の以前のバージョンを自動的に保存します。 現在 (実際) の行バージョンを保存するメインのテーブルが現行テーブルまたはテンポラル テーブルと簡単に呼ばれているのに対し、この追加のテーブルは、履歴テーブルと呼ばれています。 テンポラル テーブルの作成時、ユーザーは既存の履歴テーブルを指定するか (スキーマ準拠である必要があります)、システムに既定の履歴テーブルを作成させます。

テンポラル テーブルの詳細については、「[テンポラル テーブル](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)」をご覧ください

## <a name="see-also"></a>参照

- [Azure SQL Edge (プレビュー) でのデータ ストリーミング](stream-data.md)
- [Azure SQL Edge (プレビュー) での ONNX を使用した機械学習と AI](onnx-overview.md)
- [Azure SQL Edge (プレビュー) へのレプリケーションを構成する](configure-replication.md)
- [Azure SQL Edge (プレビュー) でのデータベースのバックアップと復元](backup-restore.md)



