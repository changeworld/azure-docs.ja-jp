<properties
	pageTitle="SQL Database V12 の新機能 |Microsoft Azure"
	description="クラウドで Azure SQL Database を使用しているビジネス システムが今すぐ V12 にアップグレードでメリットを得られる理由を説明します。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="genemi"/>


# SQL Database V12 の新機能


このトピックでは、Azure SQL Database の新しい V12 バージョンを V11 バージョンと比べたときの多くのメリットについて説明します。


V12 には継続的に機能が追加されます。したがって、Azure のサービス更新情報に関する Web ページにアクセスし、次のフィルターを使用することをお勧めします。


- サービスを [[SQL Database]](https://azure.microsoft.com/updates/?service=sql-database) でフィルター処理します。
- SQL Database の機能について、[General Availability] [(GA) のアナウンス](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)でフィルター処理します。


Azure SQL Database のリソース制限に関する最新情報は「<br/>[Azure SQL Database のリソース制限](sql-database-resource-limits.md)」にあります。


## SQL Server との強化されたアプリケーションの互換性


SQL Database V12 の主な目標は、Microsoft SQL Server 2014 との互換性を改善し、新しいバージョンの SQL Server がリリースされた場合にも互換性を維持することです。その他の領域では、V12 はプログラミングの重要な領域で SQL Server との対応を実現しました。次に例を示します。

- [組み込みの JSON のサポート](https://msdn.microsoft.com/library/dn921897.aspx)

- [ウィンドウ関数](http://msdn.microsoft.com/library/ms189798.aspx)と [OVER](http://msdn.microsoft.com/library/ms189461.aspx)

- [XML インデックス](http://msdn.microsoft.com/library/bb934097.aspx)と[選択的 XML インデックス](http://msdn.microsoft.com/library/jj670104.aspx)

- [変更の追跡](http://msdn.microsoft.com/library/bb933875.aspx)

- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)

- [フルテキスト検索](http://msdn.microsoft.com/library/ms142571.aspx)

- [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

SQL Database でまだサポートされていない一部の機能については、[ここ](sql-database-transact-sql-information.md)を参照してください。


### 互換性レベル 130


> [AZURE.IMPORTANT] **2016 年 6 月**以降、Azure SQL Database V12 で*新しく*作成されるデータベースでは、その互換性レベルが 130 で始まります。これは Microsoft SQL Server 2016 GA と一致します。
> 
> もちろん、必要に応じて `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` を使用することもできます。
> 
> この既定の設定の変更によって、2016 年 6 月より前に作成されたデータベースの互換性レベルが変更されることはありません。V11 から V12 へのアップグレードによってデータベースのレベルが変更されることもありません。



最新の互換性レベルと前の互換性レベル間で重要なクエリを比較する方法については、

- 「[SQL Database での互換性レベル 130 によるクエリ パフォーマンスの向上](sql-database-compatibility-level-query-performance-130.md)」を参照してください。



## Premium のパフォーマンスの向上、新しいパフォーマンス レベル


V12 では、すべての Premium パフォーマンス レベルに割り当てられているデータベース トランザクション ユニット (DTU) を追加料金なしで 25% 引き上げました。さらに大きなパフォーマンスの向上は、次のような新機能で実現できます。


- メモリ内[列ストア インデックス](http://msdn.microsoft.com/library/gg492153.aspx)のサポート
- [行によるテーブル パーティション](http://msdn.microsoft.com/library/ms187802.aspx)と、関連する [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx) の機能強化。
- パフォーマンスの監視と調整に役立つ動的管理ビュー [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) の利用。


### 信頼性の高いパフォーマンス


クライアントが Azure 仮想マシン (VM) で実行されるとき、クライアント プログラムが SQL Database V12 に接続する場合、VM で次のポート範囲を開く必要があります。

- 11000 ～ 11999
- 14000 ～ 14999


SQL Database V12 のポートの詳細については、[こちら](sql-database-develop-direct-route-ports-adonet-v12.md)を参照してください。これらのポートは、SQL Database V12 のパフォーマンスの強化に必要です。


## クラウド SaaS ベンダーのサポートの充実


V12 でのみ、新しい Standard パフォーマンス レベルの S3 と、[エラスティック データベース プール](sql-database-elastic-pool.md)のパブリック プレビューをリリースしました。これは、クラウド SaaS ベンダー向けに設計されたソリューションです。エラスティック データベース プールには次のメリットがあります。


- DTU をデータベース間で共有して、多数のデータベースのコストを削減できます。
- [エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)を実行して、大規模にデータベースを管理できます。


## セキュリティの強化


セキュリティは、ビジネスをクラウドで遂行しているユーザーにとっての主要な懸案事項です。V12 でリリースされた最新のセキュリティ機能は、次のとおりです。


- [行レベル セキュリティ](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [動的データ マスク](sql-database-dynamic-data-masking-get-started.md)
- [包含データベース](http://msdn.microsoft.com/library/ff929188.aspx)
- GRANT、DENY、REVOKE を使用して管理される[アプリケーション ロール](http://msdn.microsoft.com/library/ms190998.aspx)
- [透過的なデータ暗号化](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)
 - SQL Database は、Azure Active Directory の認証をサポートするようになりました。この認証は、Azure Active Directory (Azure AD) の ID を使用して SQL Database に接続するメカニズムです。Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。
- [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) (プレビュー) は、暗号化をアプリケーションに対して透過的にし、クライアントが SQL Database と暗号化キーを共有することなくクライアント アプリケーション内で機密データを暗号化できるようにします。


## 復旧が必要なときのビジネス継続性の向上


V12 では、目標復旧時点 (PRO) と推定復旧時間 (ERT) が大幅に向上しています。


| ビジネス継続性に関係する機能 | 以前のバージョン | V12 |
| :-- | :-- | :-- |
| geo リストア | • RPO は 24 時間未満。<br/>• ERT は 12 時間未満。 | • RPO は 1 時間未満。<br/>• ERT は 12 時間未満。 |
| アクティブ geo レプリケーション | • RPO は 5 分未満。<br/>• ERT は 1 時間未満。 | • RPO は 5 秒未満。<br/>• ERT は 30 秒未満。 |


詳細については、[SQL Database のビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。


## 今すぐアップグレードすることをお勧めするその他の理由


Azure SQL Database を今すぐ V11 から V12 にアップグレードすることをお勧めする理由は多数あります。


- SQL Database V12 には、V11 にない機能が多数あります。
- V12 には新しい機能を追加していくものの、V11 に新機能は追加されません。
- ほとんどの新機能は、Microsoft SQL Server 向けにリリースされる前に、SQL Database V12 でリリースされます。


## V12 を既に使っている場合


以前のバージョンの SQL Database サービスでデータベースまたは論理サーバーが実行されているかどうかは、次の方法で簡単に確認できます。


1. [Azure ポータル](https://portal.azure.com/)にアクセスします。
2. **[参照]** をクリックします。
3. **[SQL Server]** をクリックします。
4. サーバーまたはデータベースの隣のアイコンから、現在の状態がわかります。
 - ![Icon for a v12 server](./media/sql-database-v12-whats-new/v12_icon.png) **V12 の論理サーバー**
 - ![以前のバージョンのサーバーのアイコン](./media/sql-database-v12-whats-new/earlier_icon.png) **以前のバージョンの論理サーバー**


データベースで `SELECT @@version;` ステートメントを実行し、次のような結果を確認することで、バージョンを確認することもできます。


- **12**.0.2000.10 &nbsp; *(バージョン V12)*
- **11**.0.9228.18 &nbsp; *(バージョン V11)*


V12 の論理サーバーでのみ、V12 データベースをホストできます。V12 サーバーは V12 データベースのみをホストできます。


まだ V12 で実行していない場合は、[SQL Database V12 へのインプレース アップグレード](sql-database-v12-plan-prepare-upgrade.md)の手順に従って、論理サーバーをアップグレードできます。


## <a name="V12AzureSqlDbPreviewGaTable"></a> 一般公開リージョン


- 2015 年 7 月 31 日までにすべてのリージョンが完全一般公開 (GA) に昇格しました。
- V12 は 2014 年 12 月にリリースされましたが、プレビューの段階でした。

[Microsoft Azure プレビューの使用条件に関する補足](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

<!---HONumber=AcomDC_0525_2016-->