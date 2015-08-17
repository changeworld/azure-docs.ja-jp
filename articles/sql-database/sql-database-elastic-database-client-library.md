<properties
    pageTitle="Azure SQL Database - クライアント ライブラリ"
    description="スケーラブルな .NET データベース アプリを作成します"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="sidneyh"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2015"
    ms.author="sidneyh"/>

# Elastic Database クライアント ライブラリの概要

**Elastic Database クライアント ライブラリ**では、Microsoft Azure でホストされている何百、何千もの Azure SQL データベースを使用してシャード化したアプリケーションを構築することができます。このような設計は一般に、シングル テナント アーキテクチャを備えた SaaS (サービスとしてのソフトウェア) アプリケーションで使用されます (シングル テナント アーキテクチャとは、各テナントにデータベースが 1 つプロビジョニングされるアーキテクチャを指します)。そのようなアプリケーションの構築と管理が、このライブラリの目標です。Elastic Database クライアント ライブラリは .NET フレームワーク ライブラリであり、[Visual Studio](sql-database-elastic-scale-add-references-visual-studio.md) と [Nuget](http://go.microsoft.com/?linkid=9862605) を使用してどのアプリケーションにもインストールできます。クライアント ライブラリは Elastic Database ツールの一部で、具体的には [Elastic Database 機能](sql-database-elastic-scale-introduction.md) になります。

## クライアントの機能

*シャーディング* (詳細は下記) を使用したスケールアウト アプリケーションの開発、スケーリング、管理では、開発者と管理者の双方に課題が存在します。クライアント ライブラリを使えば、両者の作業負荷の軽減につなげることができます。下図中の番号は、Elastic Database クライアント ライブラリ と分割/マージ ツールでサポートされている主な機能の概略を示しています。図は多くのデータベースが存在する環境を示しており、各データベースはシャードに対応しています。ツールは、次の特定の機能を介して、シャード化された Azure SQL DB アプリケーションの開発を容易にします。

ここに使用されている用語の定義については、「[エラスティック データベース ツールの用語集](sql-database-elastic-scale-glossary.md)」を参照してください。

![エラスティック スケールの機能][1]

1.  **シャード マップの管理**: シャードのコレクションを管理するために、"シャード マップ マネージャー" という特別なデータベースが作成されます。シャード マップの管理は、アプリケーションでシャードに関するさまざまなメタデータを管理する機能です。開発者は、この機能を使用して、データベースをシャードとして登録したり、個々のシャーディング キーまたはキー範囲の当該データベースへのマッピングを記述したりできます。さらに、このメタデータを管理して、容量の変更に伴ってデータベースの数および構成を発展させることができます。Elastic Database クライアント ライブラリがないと、シャーディングを実装するにあたり管理コードの記述に多大な時間を費やすことになります。詳細については、「[シャード マップの管理](sql-database-elastic-scale-shard-map-management.md)」を参照してください。

* **データ依存ルーティング**: アプリケーションに要求が送信されるとします。アプリケーションは、要求のシャーディング キー値に基づいてこのキー値のデータを保持している正しいデータベースを識別し、そのデータベースへの接続を開いて要求を処理する必要があります。データ依存ルーティングでは、アプリケーションのシャード マップへの単一の簡単な呼び出しで接続を開くことができます。インフラストラクチャ コードのもう 1 つの領域であるデータ依存ルーティングは、現在、Elastic Database クライアント ライブラリの機能でカバーされるようになりました。詳細については、「[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)」を参照してください。

* **マルチシャード クエリ (MSQ)**: マルチシャード クエリ実行は、要求にいくつか (またはすべて) のシャードが含まれる場合に機能します。マルチシャード クエリは、同じ T-SQL コードをすべてのシャードまたはシャードのセットで実行します。参加しているシャードからの結果は、UNION ALL セマンティクスを使用して全体的な結果セットにマージされます。この機能は、クライアント ライブラリを介して公開され、接続管理、スレッド管理、エラー処理、中間結果処理などの多くのタスクを処理します。MSQ では、最大で数百個のシャードを照会できます。詳細については、「[マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md)」を参照してください。

一般に、独自のセマンティクスを持つシャード間操作とは異なり、Elastic Database ツールを使用するお客様は、シャードにローカルな操作を送信するときに完全な T-SQL 機能を取得することを期待できます。

## 次のステップ

[サンプル アプリ](sql-database-elastic-scale-get-started.md)でクライアントの機能をお試しください。

ライブラリをインストールする場合は、[Elastic Database クライアント ライブラリ](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)のページをご利用ください。

分割/マージ ツールの使用法の詳細については、[分割/マージ ツールの概要](sql-database-elastic-scale-overview-split-and-merge.md)に関するページをご覧ください。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=August15_HO6-->