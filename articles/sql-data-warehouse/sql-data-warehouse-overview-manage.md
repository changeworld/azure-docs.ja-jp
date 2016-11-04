---
title: Azure SQL Data Warehouse でのデータベースの管理 | Microsoft Docs
description: SQL Data Warehouse のデータベース管理の概要について説明します。ここでは管理ツール、DWU およびパフォーマンスのスケールアウト、クエリ パフォーマンスのトラブルシューティング、適切なセキュリティ ポリシーの設定、データの破損や地域的障害からのデータベースの復旧について見ていきます。
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/16/2016
ms.author: barbkess;sonyama;

---
# Azure SQL Data Warehouse でのデータベースの管理
SQL Data Warehouse を利用すると、データベース管理の自動化を進めることができます。たとえば、パフォーマンスのスケーリングについては、調整後、最適なコンピューティング リソースの料金をお支払いいただけば、あとは SQL Data Warehouse がパフォーマンスの拡張/縮小をすべて行います。

ワークロードを監視してパフォーマンス ニーズを特定したり、実行時間の長いクエリのトラブルシューティングを行ったりしたいとお考えの方が多いと思います。また、セキュリティ タスクをいくつか実行して、ユーザーやログインのアクセス許可を管理したい方もいらっしゃるかもしれません。

この概要では、SQL Data Warehouse の管理に関する次の項目について説明します。

* 管理ツール
* コンピューティングのスケーリング
* 一時停止と再開
* パフォーマンスのベスト プラクティス
* クエリの監視
* セキュリティ
* バックアップと復元

## 管理ツール
SQL Data Warehouse では、さまざまなツールを使用してデータベースを管理することができます。データベースを管理するにつれて、実行するタスクのタイプに応じたお好みのツールがはっきりしてくることでしょう。

### Azure ポータル
[Azure ポータル][Azure ポータル]は、データベースの作成、更新、削除、およびデータベース リソースの監視を行うことができる Web ベースの管理ポータルです。このツールは、Azure を使い始めたばかりの場合や、管理対象のデータ ウェアハウス データベースの数が少ない場合、またはすばやく作業を行う必要がある場合に便利です。

Azure ポータルの使用については、[SQL Data Warehouse の作成 (Azure ポータル)][SQL Data Warehouse の作成 (Azure ポータル)] に関する記事をご覧ください。

### Visual Studio での SQL Server Data Tools
Visual Studio の [SQL Server Data Tools][SQL Server Data Tools] \(SSDT) を使用すると、データベースへの接続、管理、および開発を行うことができます。Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、Visual Studio の SSDT の使用をお勧めします。

SSDT には SQL Server Object Explorer が含まれており、これを使用して SQL Data Warehouse データベースに対する視覚化、接続、およびスクリプト実行を行うことができます。SQL Data Warehouse にすばやく接続するには、Azure クラシック ポータルにデータベース詳細が表示されているときに、コマンド バーで [**Visual Studio で開く**] ボタンをクリックするだけです。

Visual Studio の SSDT を使用する手順については、「[Azure SQL Data Warehouse に対するクエリ (Visual Studio)][Azure SQL Data Warehouse に対するクエリ (Visual Studio)]」をご覧ください。

### コマンド ライン ツール
コマンド ライン ツールは、ワークロードの自動化に最適なツールです。PowerShell と sqlcmd は、プロセスの自動化に最適です。多数の論理サーバーを管理したり、運用環境でリソースの変更をデプロイしたりする場合は、タスクをスクリプト化した後、自動化する必要があるため、これらのツールを使用することをお勧めします。

### 動的管理ビュー
SQL Data Warehouse の管理において最も重要なのが動的管理ビュー (DMV) です。ポータルに表示されるほぼすべての情報がDMV に依存しています。SQL Data Warehouse の動的管理ビュー (DMV) の一覧を確認するには、[SQL Data Warehouse のシステム ビュー][SQL Data Warehouse のシステム ビュー]に関する記事をご覧ください。

最初に、「[SQLCMD を使用した接続とクエリ][SQLCMD を使用した接続とクエリ]」と「[Create a database (PowerShell) (データベースの作成 (PowerShell))][Create a database (PowerShell) (データベースの作成 (PowerShell))]」をご覧ください。

## コンピューティングのスケーリング
SQL Data Warehouse では、CPU、メモリ、および I/O 帯域幅のコンピューティング リソースを増減させることにより、パフォーマンスの拡張と縮小をすばやく行うことができます。パフォーマンスのスケーリングに必要なことは、SQL Data Warehouse からデータベースに割り当てられている Data Warehouse ユニット (DWU) の数を調整するだけです。SQL Data Warehouse がすばやく変更を行い、ハードウェアやソフトウェアに対する基本的な変更をすべて処理します。

Data Warehouse ユニット (DWU) のスケーリングの詳細については、[パフォーマンスのスケーリング][パフォーマンスのスケーリング]に関する記事をご覧ください。

## 一時停止と再開
コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。データベースが一時停止されている間は、DWU に対して課金されません。

詳細については、[コンピューティングの一時停止][コンピューティングの一時停止]に関するセクションと[コンピューティングの再開][コンピューティングの再開]に関するセクションをご覧ください。

## パフォーマンスのベスト プラクティス
新しいテクノロジーを初めて利用する際には、使い始めから役立つヒントやコツを知っておくことで、時間を大幅に節約できます。ここではさまざまなトピックを通じて、ベスト プラクティスをご紹介します。

ワークロードの開発において最も重要な検討事項をまとめたものについては、「[SQL Data Warehouse Best Practices (SQL Data Warehouse のベスト プラクティス)][SQL Data Warehouse Best Practices (SQL Data Warehouse のベスト プラクティス)]」をご覧ください。

## クエリの監視
実行時間が長いクエリの特定でお困りの方はいませんか。SQL Data Warehouse なら、動的管理ビュー(DMV) を使用して該当するクエリを特定することができます。

実行時間が長いクエリの特定については、「[DMV を利用してワークロードを監視する][DMV を利用してワークロードを監視する]」をご覧ください。

## セキュリティ
システムのセキュリティを維持するには、すべてのタイプの未承認のアクセスに対する警戒を強め、システムを保護していくことが重要です。セキュリティ システムでは、承認済みの IP アドレスだけが接続できるようにファイアウォールのルールを適用することが重要です。そのためには、ユーザーの資格情報を適切に認証する必要があります。データベースに接続したユーザーに実行が許可されるアクションは、最小限にとどめておくべきです。データの保護には暗号化がお勧めです。疑わしいアクティビティが発生した場合にイベントをさかのぼれるように、監査と追跡を行うことも重要です。

セキュリティの管理については、[セキュリティの概要][セキュリティの概要]に関する記事をご覧ください。

## バックアップと復元
信頼性の高いデータのバックアップは、あらゆる実稼働データベースで必要不可欠です。SQL Data Warehouse では、一定の間隔でアクティブなデータベースを自動的にバックアップして、データを安全に保ちます。データが破損したときや、データまたはデータベースを誤って削除したときは、これらのバックアップを使用して復旧することができます。データのバックアップ スケジュール、保持ポリシー、データベースの復元方法については、[スナップショットからの復元][スナップショットからの復元]に関するページを参照してください。

## 次のステップ
優れたデータベース設計方針を使用することにより、SQL Data Warehouse でのデータベース管理が容易になります。詳細については、[開発の概要][開発の概要]に関する記事を参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse の作成 (Azure ポータル)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell) (データベースの作成 (PowerShell))]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Azure SQL Data Warehouse に対するクエリ (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[SQLCMD を使用した接続とクエリ]: sql-data-warehouse-get-started-connect-sqlcmd.md
[開発の概要]: sql-data-warehouse-overview-develop.md
[DMV を利用してワークロードを監視する]: sql-data-warehouse-manage-monitor.md
[コンピューティングの一時停止]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[スナップショットからの復元]: sql-data-warehouse-restore-database-overview.md
[コンピューティングの再開]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[パフォーマンスのスケーリング]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[セキュリティの概要]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices (SQL Data Warehouse のベスト プラクティス)]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse のシステム ビュー]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure ポータル]: http://portal.azure.com/

<!---HONumber=AcomDC_0817_2016-->