<properties
   pageTitle="SQL Data Warehouse の管理ツール | Microsoft Azure"
   description="SQL Data Warehouse の管理ツールの紹介。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/24/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# SQL Data Warehouse の管理ツール
このトピックでは、SQL Data Warehouse を管理するためのツールやオプションを説明して比較し、ニーズに合ったツールを選択できるようにします。適切なツールを選択するには、管理対象のデータベースの数、タスク、タスクの実行頻度を考慮します。

## Azure ポータル
[Azure ポータル][]は、データベースの作成、更新、削除、およびデータベース リソースの監視を行うことができる Web ベースの管理ポータルです。このツールは、Azure を使い始めたばかりの場合や、管理対象のデータ ウェアハウス データベースの数が少ない場合、またはすばやく作業を行う必要がある場合に便利です。

ポータルには、現在と過去のパフォーマンス DWU 設定、使用中のストレージ量、成功および失敗した SQL 接続、およびインスタンスで実行されているクエリやそれらの詳細を把握できる視覚化情報とデータのセットが網羅されたメトリックが含まれています。

## Visual Studio での SQL Server Data Tools	
Visual Studio の [SQL Server Data Tools][] (SSDT) は、コンピューター上で実行し、クラウドでデータベースに対する接続、管理、および開発を行うことができるクライアント ツールです。Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、Visual Studio の SSDT の使用をお勧めします。

SSDT には SQL Server Explorer が含まれており、これを使用して SQL Data Warehouse データベースに対する視覚化、接続、およびスクリプトを実行できます。SQL Data Warehouse にすばやく接続するには、Azure ポータルにデータベース詳細が表示されているときに、コマンド バーで [**Visual Studio で開く**] ボタンをクリックするだけです。

SQL Data Warehouse に対するサポートが含まれた [SQL Server Data Tools][] (SSDT) の最新バージョンをダウンロードできます。

## コマンド ライン ツール
オプションとして、PowerShell または sqlcmd コマンド ライン ツールを使用して SQL Data Warehouse を管理したり、Azure リソース開発を自動化したりすることができます。多数の論理サーバーを管理したり、運用環境でリソースの変更をデプロイしたりする場合は、タスクをスクリプト化した後、自動化する必要があるため、これらのツールを使用することをお勧めします。

## 次のステップ
これらのツールを使用し始めるには、[接続][]に関するトピックに進んでください。

<!--Image references-->

<!--Article references-->
[接続]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/ja-jp/library/mt204009.aspx

<!--Other web references-->
[Azure ポータル]: http://portal.azure.com/

<!---HONumber=July15_HO3-->