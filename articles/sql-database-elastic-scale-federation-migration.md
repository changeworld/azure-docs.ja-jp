<properties title="Federations Migration" pageTitle="フェデレーションの移行" description="Outlines the steps to migrate an existing app built with Federations feature to Elastic Scale model." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

#フェデレーションの移行 

Azure SQL Database Federations 機能は、2015 年 9 月をもって、Web エディションおよび Business エディションと共に廃止されます。それ以降、Federations 機能を利用するアプリケーションは実行できなくなります。移行を成功させるには、計画と実施に十分な時間を充てられるようにすぐに作業を始めることを強くお勧めします。このドキュメントでは、Federations Migration Utility のコンテキスト、例、および概要について説明し、現在の Federations アプリケーションを [Azure SQL DB Elastic Scale プレビュー API](http://go.microsoft.com/?linkid=9862592) にシームレスに移行する方法を紹介します。このドキュメントは、データを移動することなく Federations アプリケーションを移行するための推奨手順を示すことを目的としています。

既存の Federations アプリケーションを Elastic Scale API を使用するアプリケーションに移行するには、次の 3 つの手順を実行します。

1. [フェデレーション ルートからシャード マップ マネージャーを作成する]
2. [既存のアプリケーションを変更する]
3. [既存のフェデレーション メンバーに対して SWITCH OUT を実行する]
    

### 移行のサンプル ツール
このプロセスを支援するために、[Federations Migration Utility](http://go.microsoft.com/?linkid=9862613) が用意されています。このユーティリティは、手順 1. ～ 3. を実行します。

## <a name="create-shard-map-manager"></a>フェデレーション ルートからシャード マップ マネージャーを作成する
Federations アプリケーションを移行するための最初の手順として、フェデレーション ルートのメタデータをシャード マップ マネージャーの構成要素に複製します。 

![Clone the federation root to the shard map manager][1]
 
テスト環境で既存の Federations アプリケーションを起動します。
 
**Federations Migration Utility** を使用して、フェデレーション ルートのメタデータを Elastic Scale [シャード マップ マネージャー](http://go.microsoft.com/?linkid=9862595)の構成要素に複製します。フェデレーション ルートと同様、シャード マップ マネージャー データベースは、シャード マップ (つまり、フェデレーション)、シャードへの参照 (つまり、フェデレーション メンバー)、および対応する範囲マッピングを含むスタンドアロンのデータベースです。 

フェデレーション ルートをシャード マップ マネージャーに複製する操作には、メタデータをコピーして翻訳する操作が含まれます。フェデレーション ルートのメタデータを変更することはありません。Federations Migration Utility によるフェデレーション ルートの複製は特定の時点に対する操作であり、フェデレーション ルートまたはシャード マップに加えられた変更は他方のデータ ストアには反映されません。新しい API のテスト中にフェデレーション ルートが変更された場合、Federations Migration Utility を使用してシャード マップを更新して、最新の状態を反映することができます。 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 既存のアプリケーションを変更する 

シャード マップ マネージャーを配置し、(移行ユーティリティを使用して) フェデレーションのメンバーと範囲がシャード マップ マネージャーを登録すると、Elastic Scale API を使用するように既存の Federations アプリケーションを変更できます。上の図に示すように、Elastic Scale API を介したアプリケーション接続は、シャード マップ マネージャーを介して適切なフェデレーション メンバーに (同様にシャードにも) ルーティングされます。フェデレーション メンバーをシャード マップ マネージャーにマップすると、Federations を使用するバージョンと Elastic Scale を使用するバージョンが存在することになります。これらの 2 つのアプリケーションのバージョンは、機能を検証するために同時に使うことができます。   

アプリケーションの移行中、既存のアプリケーションに次の 2 つの主要な変更を加える必要があります。


#### 変更 1: シャード マップ マネージャー オブジェクトをインスタンス化する: 

Federations とは異なり、Elastic Scale API は、**ShardMapManager** クラスを通じてシャード マップ マネージャーと対話します。**ShardMapManager** オブジェクトとシャード マップのインスタンス化は、次のように実行できます。
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 変更 2: 適切なシャードに接続をルーティングする

Federations で特定のフェデレーション メンバーへの接続を確立するには、次のように USE FEDERATION コマンドを使用します。  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Elastic Scale API で特定のシャードへの接続を確立するには、**RangeShardMap** クラスの **OpenConnectionForKey** メソッドによる[データ依存ルーティング](./sql-database-elastic-scale-data-dependent-routing.md)を使用します。 

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

このセクションの手順は必ず実行する必要がありますが、実際に発生するすべての移行シナリオに対応できない場合があります。詳細については、[Elastic Scale の概念の概要](./sql-database-elastic-scale-introduction.md)および [API リファレンス](http://go.microsoft.com/?linkid=9862604)を参照してください。

## 既存のフェデレーション メンバーに対して SWITCH OUT を実行する 

![Switch out the federation members for the shards][3]

Elastic Scale API を含むようにアプリケーションを変更した後の Federations アプリケーションを移行する最後の手順として、フェデレーション メンバーに対して **SWITCH OUT** を実行します (詳細については、MSDN の「[ALTER FEDERATION (Azure SQL データベース)](http://msdn.microsoft.com/ja-jp/library/azure/hh597475.aspx)」を参照してください。特定のフェデレーション メンバーに対して **SWITCH OUT** を発行すると、すべてのフェデレーション制約とメタデータが削除され、その結果、フェデレーション メンバーは、他の Azure SQL データベースと同じ通常の Azure SQL データベースとなります。 

フェデレーション メンバーに対して **SWITCH OUT** を発行する操作は一方向の操作であり、元に戻すことはできません。この操作を実行した後のデータベースを再びフェデレーションに追加することはできません。また、このデータベースに対しては USE FEDERATION コマンドも機能しなくなります。 

切り替えを実行するために、ALTER FEDERATION コマンドにフェデレーション メンバーを切り替えるための追加の引数が追加されています。このコマンドは、個々のフェデレーション メンバーに対して発行できます。一方、Federations Migration Utility には、プログラムで各フェデレーション メンバーに対して反復処理を行って切り替え操作を実行する機能が用意されています。 

すべての既存のフェデレーション メンバーに対して切り替え操作を実行すると、アプリケーションの移行が完了します。  
Federations Migration Utility を使用すると、次の操作を実行できます。 

1.    フェデレーション ルートをシャード マップ マネージャーに複製する。既存のシャード マップ マネージャーを新しい Azure SQL データベースに配置するか、既存のフェデレーション ルート データベースに配置するかを選択できます (前者の方法をお勧めします)。
2.    フェデレーションのすべてのフェデレーション メンバーに対して SWITCH OUT を発行する。


##機能の比較  
Elastic Scale には多くの追加機能 ([マルチシャード クエリ](./sql-database-elastic-scale-multishard-querying.md)、[シャードの分割とマージ](./sql-database-elastic-scale-overview-split-and-merge.md)、[シャードの弾力性](./sql-database-elastic-scale-elasticity.md)、[クライアント側のキャッシュ](./sql-database-elastic-scale-shard-map-management.md)など) がありますが、Elastic Scale でサポートされない Federations の機能がいくつかあることに注意してください。
  

- **FILTERING=ON** の使用。現在、Elastic Scale では、行レベルのフィルター処理はサポートされません。1 つの対応策として、次のように、シャードに対して実行されるクエリにフィルター処理のロジックを組み込みます。 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

次のコードで同じ結果が得られます。

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Elastic Scale の**分割**機能は完全にオンラインで実行されるわけではありません。分割操作時、各シャードレットは移動中にオフラインになります。
- Elastic Scale の分割機能を使用するには、手動によるデータベースのプロビジョニングとスキーマの管理が必要です。

## 追加の考慮事項

* 2015 年秋に Web および Business エディションと Federations の両方が廃止される予定です。Federations アプリケーションの移行の一部として、Basic、Standard、および Premium の各エディションでパフォーマンス テストを実施することを強くお勧めします。 

* フェデレーション メンバーに対して SWITCH OUT ステートメントを実行すると、結果のデータベースは、すべての Azure SQL データベース機能 (新しいエディション、バックアップ、PITR、監査など) を利用できるようになります。 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[フェデレーション ルートからシャード マップ マネージャーを作成する]:#create-shard-map-manager
[既存のアプリケーションを変更する]:#Modify-the-Existing-Application
[既存のフェデレーション メンバーに対して SWITCH OUT を実行する]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
