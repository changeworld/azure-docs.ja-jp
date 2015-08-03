<properties 
    pageTitle="Federations の移行" 
    description="Federations 機能で作成した既存のアプリを Elastic Database モデルに移行する大まかな手順を示します。" 
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
    ms.date="05/21/2015" 
    ms.author="sidneyh"/>

# Federations の移行 

Azure SQL Database Federations 機能は、Web と Business エディションと共に、2015 年 9 月にサービスが終了します。有効期限が過ぎると Federations 機能を使用するアプリケーションは実行できなくなります。移行を成功させるには、計画と実施に十分な時間を充てられるようにすぐに作業を始めることを強くお勧めします。このドキュメントでは、Federations Migration Utility のコンテキスト、例、および概要について説明し、現在の Federations アプリケーションを、シャーディング用の [Elastic Database クライアント ライブラリ](http://go.microsoft.com/?linkid=9862592) API にシームレスに移行する方法を紹介します。このドキュメントの目的は、データを移動せずに Fedarations アプリケーションを移行するための推奨手順を紹介することです。

既存の Federations アプリケーションを Elastic Database ツールを使用するアプリケーションに移行するには、次の 3 つの手順を実行します。

1. [フェデレーション ルートからシャード マップ マネージャーを作成する](#create-a-shard-map-manager-from-a-federation-root) 
2. [既存のアプリケーションを変更する](#modify-the-existing-application)
3. [既存のフェデレーション メンバーに対して SWITCH OUT を実行する](#switch-out-existing-federation-members)
    

### 移行のサンプル ツール
このプロセスを支援するために、[Federations Migration Utility](http://go.microsoft.com/?linkid=9862613) が用意されています。このユーティリティは手順 1. と 3. が完了しています。

## フェデレーション ルートからシャード マップ マネージャーを作成する
Federations アプリケーションを移行する最初の手順は、フェデレーションのルートのメタデータを、シャード マップ マネージャーのコンストラクトに複製することです。

![フェデレーション ルートをシャード マップ マネージャーに複製する][1]
 
テスト環境で既存の Federations アプリケーションを起動します。
 
**Federations Migration Utility** を使用して、フェデレーション ルートのメタデータを Elastic Database クライアント ライブラリの[シャード マップ マネージャー](http://go.microsoft.com/?linkid=9862595)の構成要素に複製します。フェデレーション ルートと同様、シャード マップ マネージャー データベースは、シャード マップ (つまり、フェデレーション)、シャードへの参照 (つまり、フェデレーション メンバー)、および対応する範囲マッピングを含むスタンドアロンのデータベースです。

フェデレーションのルートをシャード マップ マネージャーに複製すると、メタデータがコピーして変換されます。フェデレーション ルートのメタデータを変更することはありません。Federations Migration Utility によるフェデレーション ルートの複製は特定の時点に対する操作であり、フェデレーション ルートまたはシャード マップに加えられた変更は他方のデータ ストアには反映されません。新しい API のテスト中にフェデレーション ルートが変更された場合、Federations Migration Utility を使用してシャード マップを更新して、最新の状態を反映することができます。

![Elastic Database ツール API を使用する既存のアプリケーションを移行する][2]

## 既存のアプリケーションを変更する 

シャード マップ マネージャーの準備が完了し、(移行ユーティリティを介して) フェデレーション メンバーと範囲がシャード マップ マネージャーに登録されると、Elastic Database クライアント ライブラリを使用するように既存の Federations アプリケーションを変更できます。上の図に示すように、これらの API を介したアプリケーション接続は、シャード マップ マネージャーを介して適切なフェデレーション メンバーに (同様にシャードにも) ルーティングされます。フェデレーション メンバーをシャード マップ マネージャーにマップすると、Federations を使用するバージョンと Elastic Database クライアント ライブラリを使用するバージョンが存在することになります。これらの 2 つのアプリケーションのバージョンは、機能を検証するために同時に使うことができます。

アプリケーションの移行中、既存のアプリケーションに次の 2 つの主要な変更を加える必要があります。


#### 変更 1: シャード マップ マネージャー オブジェクトをインスタンス化する: 

Federations とは異なり、Elastic Database ツール API は、**ShardMapManager** クラスを通じてシャード マップ マネージャーと対話します。**ShardMapManager** オブジェクトとシャード マップのインスタンス化は、次のように実行できます。
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 変更 2: 適切なシャードに接続をルーティングする 

Federations では、特定のフェデレーション メンバーへの接続は、次のように USE FEDERATION コマンドを使用して確立されます。

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Elastic Database ツール API で特定のシャードへの接続を確立するには、**RangeShardMap** クラスの **OpenConnectionForKey** メソッドによる[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)を使用します。

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

このセクションのステップは実行する必要があるものですが、起こりうる移行シナリオによっては、適応しない場合があります。詳細については、[Elastic Database ツールの概念の概要](sql-database-elastic-scale-introduction.md)および [API リファレンス](http://go.microsoft.com/?linkid=9862604)を参照してください。

## 既存のフェデレーション メンバーに対して SWITCH OUT を実行する 

![シャードのフェデレーション メンバーに対して切り替え操作を実行する][3]

Elastic Database ツール API を含むようにアプリケーションを変更したら、Federations アプリケーションの移行の最終ステップとして、フェデレーション メンバーに対して **SWITCH OUT** を実行します (詳細については、MSDN リファレンスの [ALTER FEDERATION (Azure SQL Database)]http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx))を参照してください)。特定のフェデレーション メンバーに対して **SWITCH OUT** を発行すると、すべてのフェデレーション制約とメタデータが削除され、その結果、フェデレーション メンバーは、他の Azure SQL Database と同じ通常の Azure SQL Database となります。

フェデレーション メンバーに対して **SWITCH OUT** を発行する操作は一方向の操作であり、元に戻すことはできません。この操作を実行した後のデータベースを再びフェデレーションに追加することはできません。また、このデータベースに対しては USE FEDERATION コマンドも機能しなくなります。

切り替えを実施するために、フェデレーション メンバーを SWITCH OUT する目的の別の引数が ALTER FEDERATION コマンドに追加されています。このコマンドは、個々のフェデレーション メンバーに対して発行できます。一方、Federations Migration Utility には、プログラムで各フェデレーション メンバーに対して反復処理を行って切り替え操作を実行する機能が用意されています。

すべての既存のフェデレーション メンバーに対する切り替えが実施されると、アプリケーションの移行が完了します。すべてのフェデレーション メンバーに対して切り替え操作が実行済みである場合は、ルートで DROP FEDERATION 操作を実行することをお勧めします。この操作は、元のメンバーには影響しないので、ルート データベースを Web および Business Edition から簡単に移行することができます。
  
Federations 移行ユーティリティが提供する機能は次のとおりです。

1.    シャード マップ マネージャーにフェデレーションのルートを複製する。新しい Azure SQL のデータベース (推奨) と既存のフェデレーション ルート データベースのどちらに既存のシャード マップ マネージャーを配置するかを選択できます。
2.    フェデレーションのすべてのフェデレーション メンバーに対して SWITCH OUT を発行する。


## 機能の比較

Elastic Database ツールには多くの追加機能 ([マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)、[シャードの分割とマージ](sql-database-elastic-scale-overview-split-and-merge.md)、[シャードの弾力性](sql-database-elastic-scale-elasticity.md)、[クライアント側のキャッシュ](sql-database-elastic-scale-shard-map-management.md)など) がありますが、Elastic Database ツールでサポートされない Federations の機能がいくつかあることに注意してください。
  
- **FILTERING=ON** の使用。行のフィルター処理には行レベルのセキュリティ (RLS) の使用をお勧めします。Federations でのフィルター処理と同様に、RLS でもシャード化したテーブル上のすべてのクエリに述語を自動的に追加します。詳細については、「[Elastic Database ツールと行レベルのセキュリティを使用したマルチテナント アプリケーション](sql-database-elastic-tools-multi-tenant-row-level-security.md)」を参照してください。 
 
 あるいは、シャードに対して実行されるクエリにフィルター処理のロジックを組み込みこむことができます。次に例を示します。

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

 次のコードで同じ結果が得られます。

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Elastic Database ツールの**分割**機能は完全にオンラインで実行されるわけではありません。分割操作中、それぞれのシャードレットは個別に、移動の途中でオフラインになります。
- 分割機能を使用するには、手動によるデータベースのプロビジョニングとスキーマの管理が必要です。

## 追加の考慮事項

* Web と Business の両エディションと Federations は、2015 年の秋に廃止されます。アプリケーションの移行の一環として、Basic、Standard、Premium の各エディションのパフォーマンス テストを実施することを強くお勧めします。 

* フェデレーション メンバーに対して SWITCH OUT ステートメントを実行すると、結果のデータベースは、すべての Azure SQL データベース機能 (新しいエディション、バックアップ、PITR、監査など) を利用できるようになります。

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[Create Shard Map Manager from a Federation Root]: #create-shard-map-manager
[Modify the Existing Application]: #Modify-the-Existing-Application
[Switch Out Existing Federation Members]: #Switch-Out-Existing-Federation-Members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
 

<!---HONumber=July15_HO4-->