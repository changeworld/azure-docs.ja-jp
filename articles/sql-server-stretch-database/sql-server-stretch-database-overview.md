<properties
	pageTitle="Stretch Database の概要 | Microsoft Azure"
	description="Stretch Database で、透過的かつ安全に履歴データを Microsoft Azure クラウドに移行する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Stretch Database の概要

Stretch Database は、透過的かつ安全に履歴データを Microsoft Azure クラウドに移行します。

## Stretch Database の利点
Stretch Database には次の利点があります。

**コールド データにもコスト効率がよい可用性を提供**: SQL Server Stretch Database を使用して、ウォーム トランザクション データとコールド トランザクション データを SQL Server から Microsoft Azure に動的に拡張します。通常のコールド データ ストレージとは異なり、データは常にオンラインであり、クエリに使用できます。顧客の注文履歴のように大きなテーブルでも、高いコストをかけずに、データ リテンション期間のタイムラインを長くすることができます。スケーリングのコストが高いオンプレミス ストレージよりも、低コストの Azure をお勧めします。価格レベルを選択し、Azure ポータルで設定を構成することで、価格とデータ アクセス速度を管理します。必要に応じてスケールアップまたはスケールダウンします。詳細については、[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)のページを参照してください。

**クエリやアプリケーションを変更する必要がない**: オンプレミスか、クラウドに拡張されているかにかかわらず、SQL Server データにシームレスにアクセスできます。管理者がデータを格納する場所を決定するポリシーを設定すると、SQL Server はバックグラウンドでデータ移行を処理します。テーブル全体が常にオンラインであり、クエリ可能です。また、Stretch Database は既存のクエリやアプリケーションを変更する必要がありません。データの場所は、アプリケーションに対して完全に透過的です。

**オンプレミス データの保守を効率化**: データのオンプレミスの保守と記憶域を減らします。データのクラウド部分のバックアップは自動的に実行されます。オンプレミス データのバックアップはより高速に実行され、保守期間内に完了します。オンプレミス記憶域のニーズは大幅に軽減されます。Azure Storage は、オンプレミスの SSD に追加するよりも 80% 低いコストになる可能性があります。

**移行中でもデータの保護を維持**: 重要度の高いアプリケーションでも、クラウドに安全に拡張できるので安心です。SQL Server の Always Encrypted 機能によって、移動中のデータも暗号化できます。また、行レベルのセキュリティ (RLS) と他の高度な SQL Server セキュリティ機能も Stretch Database と連携してデータを保護できます。

## Stretch Database の機能
SQL Server インスタンス、データベース、少なくとも 1 つのテーブルで Stretch Database を有効にすると、履歴データの Azure への移行が自動的に開始されます。

-   別のテーブルに履歴データを保存すると、テーブル全体を移行できます。

-   テーブルに過去と現在の両方のデータが含まれている場合、移行する行を選択するフィルター述語を指定できます。

Stretch Database は、移行中にエラーが発生しても、データの損失が発生しません。また、移行中に接続の問題が発生した場合でも、その問題に対応する再試行ロジックがあります。動的管理ビューには、移行の状態が表示されます。

ローカル サーバー上の問題を解決する場合や、使用可能なネットワーク帯域幅を最大限にする場合は、データ移行を一時停止することができます。

既存のクエリとクライアント アプリを変更する必要はありません。データの移行中も、ローカル データとリモート データの両方に対して、継続してシームレスにアクセスできます。リモート クエリの場合は短時間の遅延がありますが、履歴データにクエリするときにのみ、このような遅延が発生します。

![Stretch Database の概要][StretchOverviewImage1]

## Stretch Database が適しているかどうか
次のような意見がある場合、Stretch Database で要件を満たし、問題を解決できる可能性があります。

|意思決定者の場合|データベース管理者の場合|
|------------------------------|-------------------|
|トランザクション データを長期間保存する必要がある。|テーブルのサイズが大きくなり、管理が困難になっている。|
|ときには履歴データにクエリする必要がある。|ユーザーは履歴データへのアクセスを希望しているが、ほとんど使用することはない。|
|古いアプリケーションを含め、更新する予定がないアプリケーションがある。|次々と記憶域を購入し、追加し続ける必要がある。|
|記憶域にかかるコストを削減する方法を見つけたい。|SLA 内でこのように大きなテーブルをバックアップまたは復元することができない。|

## Stretch Database の候補となるデータベースとテーブルの種類
Stretch Database は、大量の履歴データがあるトランザクション データ、通常は少数のテーブルに格納されているデータを対象としています。たとえば、10 億行を超えるテーブルなどがあります。

SQL Server 2016 のテンポラル テーブルを使用している場合、Stretch Database を使用して、関連する履歴テーブルのすべてまたは一部をコスト効率のよい Azure の記憶域に移行します。詳細については、[システム バージョン管理されたテンポラル テーブルで履歴データのリテンション期間を管理する方法](https://msdn.microsoft.com/library/mt637341.aspx)のページを参照してください。

SQL Server 2016 Upgrade Advisor の 1 機能である Stretch Database Advisor を使用して、Stretch Database のデータベースとテーブルを特定します。詳細については、「[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。潜在的なブロックの問題の詳細については、「[Stretch Database のセキュリティ制約とブロック問題](sql-server-stretch-database-limitations.md)」を参照してください。

## <a name="FAQ"></a>Stretch Database についてよく寄せられる質問
**Stretch Database は &lt;SQL Server の機能名&gt; と連携しますか?**
-   Stretch に適していないテーブルになる SQL Server 機能の一覧については、「[Stretch Database のセキュリティ制約とブロック問題](sql-server-stretch-database-limitations.md)」を参照してください。

-   必要に応じて、SQL Server 2016 Upgrade Advisor をダウンロードし、Stretch Database Advisor を実行して、Stretch Database の候補となるデータベースとテーブルを特定します。詳細については、「[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。

**Stretch Database の別のローカル SQL Server インスタンスを対象にすることはできますか?** いいえ。Stretch Database は、リモート エンドポイントとは別のローカル SQL Server インスタンスをサポートしません。

**Stretch を無効にして、移行済みのデータをローカル テーブルに戻すことはできますか?** はい。詳細については、「[Stretch Database を無効にしてリモート データを戻す](sql-server-stretch-database-disable.md)」を参照してください。

## 用語
**ローカル データベース**。オンプレミス SQL Server データベース。

**リモート エンドポイント**。データベースのリモート データを含む Microsoft Azure の場所。

**ローカル データ**。Stretch Database を有効にしたデータベースのデータのうち、データベースのテーブルの Stretch Database 構成に基づいて Azure に移行されないデータ。

**対象データ**。Stretch Database を有効にしたデータベースのデータのうち、まだ Azure に移行されていないが、データベースのテーブルの Stretch Database 構成に基づいて移行される予定のデータ。

**リモート データ**。Stretch Database を有効にしたデータベースのデータのうち、既に Azure に移行されたデータ。

## アーキテクチャ
Stretch Database は、Microsoft Azure のリソースを利用して、アーカイブ データの記憶域とクエリの処理を軽減します。

データベースで Stretch Database を有効にすると、オンプレミスの SQL Server にセキュリティで保護されたリンクのサーバー定義が作成されます。このリンクされたサーバー定義には、対象としてリモート エンドポイントがあります。データベースのテーブルで Stretch Database を有効にすると、リモート リソースがプロビジョニングされ、移行が有効に場合は対象データの移行が開始されます。

Stretch Database が有効なテーブルに対するクエリは、ローカル データベースとリモート エンドポイントの両方に対して自動的に実行されます。Stretch Database は、Azure の処理能力を利用し、クエリを書き換えてリモート データに対してクエリを実行します。この書き換えは、新しいクエリ プランの "remote query" 演算子で確認できます。

![Stretch Database のアーキテクチャ][StretchOverviewImage2]

## セキュリティとアクセス許可

### SQL Server インスタンスの Stretch Database を有効または無効にする
Stretch Database のデータベースの構成を開始するには、まず sp\_configure を使用して "remote data archive" インスタンスレベルの構成を変更する必要があります。この操作には、sysadmin または serveradmin の特権が必要です。このオプションを有効にすると、Stretch Database のデータベースの構成、データの移行、リモート エンドポイントのデータのクエリを行うことができます。

### データベースまたはテーブルの Stretch Database を無効または有効にする
Stretch Database 用にデータベースを構成するには、CONTROL DATABASE アクセス許可が必要です。さらに、リモート エンドポイントの管理者の資格情報を入力する必要があります。

Stretch Database のテーブルを構成するには、テーブルに対する ALTER 特権が必要です。また、データベースが Stretch Database 用に既に構成されている必要があります。

### データ アクセス
Stretch Database の背後にあるリンクされたサーバー定義にアクセスできるのは、システム プロセスのみです。ユーザーのログインでは、リンクされたサーバー定義を介してリモート エンドポイントにクエリを発行することはできません。

Stretch Database は、既存のデータベースのアクセス許可モデルを変更しません。ユーザーのログインでは、ローカル データベースを介して、Stretch Database が有効なテーブルのデータをクエリすることができます。ローカル データベースは、他のオブジェクトの場合と同様の方法で、ユーザーが開始したアクションのアクセス許可チェックを実行します。Stretch Database が有効なテーブルにアクセスする権限を持っている場合、データが保存されている物理的な場所にかかわらず、権限を持つすべてのコンテンツにアクセスできます。

![Stretch Database のデータ アクセス モデル][StretchOverviewImage3]

## Stretch Database を試用する
**AdventureWorks サンプル データベースで Stretch Database を試用することができます。** AdventureWorks サンプル データベースを入手するには、[こちら](https://www.microsoft.com/download/details.aspx?id=49502)から、少なくともデータベース ファイル、サンプル ファイル、スクリプト ファイルをダウンロードします。サンプル データベースを SQL Server 2016 のインスタンスに復元した後に、サンプル ファイルを解凍し、Stretch DB フォルダーの Stretch DB Samples ファイルを開きます。このファイルのスクリプトを実行して、Stretch Database を有効にする前後でデータに使用される空間を確認します。また、データ移行の進行状況を追跡し、データの移行中も移行後も、継続して既存のデータをクエリできること、新しいデータを挿入できることを確認します。

## 次のステップ
**Stretch Database の候補となるデータベースとテーブルを特定します。** SQL Server 2016 Upgrade Advisor をダウンロードし、Stretch Database Advisor を実行して、Stretch Database の候補となるデータベースとテーブルを特定します。Stretch Database Advisor はブロック問題も特定します。詳細については、「[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0302_2016-->