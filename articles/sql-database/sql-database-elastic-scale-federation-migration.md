<properties 
	pageTitle="Federations の移行" 
	description="Federations 機能で作成した既存のアプリを Elastic Scale モデルに移行する大まかな手順を示します。" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Federations の移行 

Azure SQL Database Federations 機能は、Web と Business エディションと共に、2015 年 9 月にサービスが終了します。有効期限が過ぎると Federations 機能を使用するアプリケーションは実行できなくなります。移行を成功裏に完了するために最も推奨されるのは、できるだけ早い時期から移行の作業を開始して、余裕のある計画と遂行ができるようにすることです。このドキュメントでは、現在の Federations アプリケーションを [Azure SQL DB Elastic Scale プレビュー API](http://go.microsoft.com/?linkid=9862592) にシームレスに移行する方法について、Federations 移行ユーティリティのコンテキスト、例、手順を用いて説明します。このドキュメントの目的は、データを移動せずに Fedarations アプリケーションを移行するための推奨手順を紹介することです。

既存の Federations アプリケーションを Elastic Scale API を使用するアプリケーションに移行するには、主な手順として以下の 3 つがあります。

1. [フェデレーションのルートからシャード マップ マネージャーを作成する] 
2. [既存のアプリケーションを変更する]
3. [既存のフェデレーション メンバーに対して SWITCH OUT を実行する]
    

### 移行のサンプル ツール
このプロセスを支援するために、[Federations 移行ユーティリティ](http://go.microsoft.com/?linkid=9862613)が作成されました。このユーティリティは手順 1. と 2. が完了しています。 

## <a name="create-shard-map-manager"></a>フェデレーションのルートからシャード マップ マネージャーを作成する
Federations アプリケーションを移行する最初の手順は、フェデレーションのルートのメタデータを、シャード マップ マネージャーのコンストラクトに複製することです。 

![Clone the federation root to the shard map manager][1]
 
既存の Federations アプリケーションをテスト環境で起動します。
 
**移行ユーティリティ**を使用して、フェデレーションのルートのメタデータを、Elastic Scale の[シャード マップ マネージャー](http://go.microsoft.com/?linkid=9862595)のコンストラクトに複製します。フェデレーション ルートと同様、シャード マップ マネージャー データベースは、シャード マップ (つまり、フェデレーション)、シャードへの参照 (つまり、フェデレーション メンバー)、対応する範囲マッピングを含むスタンドアロンのデータベースです。 

フェデレーションのルートをシャード マップ マネージャーに複製すると、メタデータがコピーして変換されます。フェデレーションのルートにあるメタデータ自体は変更されません。Federations 移行ユーティリティでフェデレーションのルートを複製することは、あくまでも、ある特定の時点における操作であり、フェデレーションのルートまたはシャード マップに対する変更が他の関連のデータ ストアに反映されることはありません。新しい API のテスト中にフェデレーションのルートに対して変更が加えられた場合は、Federations 移行ユーティリティを使用して、現在の状態が表示されるようにシャード マップを最新の情報に更新できます。 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 既存のアプリケーションを変更する 

シャード マップ マネージャーの準備が完了し、移行ユーティリティを介してフェデレーション メンバーと範囲がシャード マップ マネージャーに登録されると、ユーザーは Federations アプリケーションを変更して、Elastic Scale API を利用できます。前の図に示したように、Elastic Scale API を介したアプリケーションの接続は、シャード マップ マネージャーを通じて、適切なフェデレーション メンバーに (この段階でシャードにも) ルーティングされます。フェデレーション メンバーをシャード マップ マネージャーにマッピングすることにより、Federations を使用するアプリケーションと Elastic Scale を使用するアプリケーションの 2 種類のアプリケーションを使用することができ、これらを並列的に実行した機能の検証が有効になります。   

アプリケーションの移行時、既存のアプリケーションに対して実行する必要がある、代表的な変更には次の 2 種類があります。


#### 変更 1:シャード マップ マネージャー オブジェクトのインスタンス化: 

Federations とは異なり、Elastic Scale API は、**ShardMapManager** クラスを通じてシャード マップ マネージャーとやり取りします。**ShardMapManager** オブジェクトとシャード マップのインスタンス化は、次のように行うことができます。
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 変更 2:適切なシャードに接続をルーティングする 

Federations では、特定のフェデレーション メンバーへの接続は、次のように USE FEDERATION コマンドを使用して確立されます。  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

Elastic Scale API では、特定のシャードへの接続は、[データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md) を介して確立され、**RangeShardMap** クラスの **OpenConnectionForKey** メソッドが使用されます。 

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

このセクションのステップは実行する必要があるものですが、起こりうる移行シナリオによっては、適応しない場合があります。詳細については、[Elastic Scale の概念をまとめたページ](sql-database-elastic-scale-introduction.md) と「[Class Library (クラス ライブラリ)](http://go.microsoft.com/?linkid=9862604)」をご覧ください。

## 既存のフェデレーション メンバーに対して SWITCH OUT を実行する 

![Switch out the federation members for the shards][3]

Elastic Scale API が含まれるようにアプリケーションを変更したら、Federations アプリケーションの移行の最終ステップは、フェデレーション メンバーに対して **SWITCH OUT** を実施することです (詳細については、MSDN リファレンス ページの「[ALTER FEDERATION (Azure SQL データベース)][1]」をご覧ください。特定のフェデレーション メンバーに対して **SWITCH OUT** を実行すると、最終的な結果としては、他の Azure SQL Database と変わらない通常の Azure SQL Database としてフェデレーション メンバーを表示する、すべてのフェデレーション制約とメタデータが削除されることになります。  
[1]: media/sql-database-elastic-scale-federation-migration/dn269988(v=sql.120

フェデレーション メンバーに対する **SWITCH OUT** の実行は一方向の操作であり、元に戻すことはできません。一度実行すると、結果のデータベースをフェデレーションに戻すことはできません。また、このデータベースには USE FEDERATION コマンドを使うことはできなくなります。 

切り替えを実施するために、フェデレーション メンバーを SWITCH OUT する目的の別の引数が ALTER FEDERATION コマンドに追加されています。このコマンドを個々のフェデレーション メンバーに対して発行できる一方で、Federations 移行ユーティリティにより、それぞれのフェデレーション メンバー間をプログラムによって反復処理して切り替えを操作する機能が提供されます。 

すべての既存のフェデレーション メンバーに対する切り替えが実施されると、アプリケーションの移行が完了します。  
Federations 移行ユーティリティが提供する機能は次のとおりです。 

1.    シャード マップ マネージャーにフェデレーションのルートを複製する。新しい Azure SQL のデータベース (推奨) と既存のフェデレーション ルート データベースのどちらに既存のシャード マップ マネージャーを配置するかを選択できます。
2.    フェデレーション内のすべてのフェデレーション メンバーに対して SWITCH OUT を実行する。


## 機能の比較  
Elastic Scale には数多くの機能がありますが ([マルチシャード クエリ実行](sql-database-elastic-scale-multishard-querying.md)、[シャードの分割とマージ](sql-database-elastic-scale-overview-split-and-merge.md)、[シャードの弾力性](sql-database-elastic-scale-elasticity.md)、[クライアント側によるキャッシュ](sql-database-elastic-scale-shard-map-management.md)など)、Federations には Elastic Scale ではサポートされない注目すべきいくつかの機能があります。
  

- **FILTERING=ON** の使用。Elastic Scale では現在、行レベルのフィルタリングをサポートしていません。軽減策の 1 つとして、シャードに対して発行するクエリに、フィルタリング ロジックをビルドする次の方法があります。 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

これは、次を実行した場合と同じ結果が得られます。

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- Elastic Scale の**分割**機能は、完全なオンラインではありません。分割操作中、それぞれのシャードレットは個別に、移動の途中でオフラインになります。
- Elastic Scale の分割機能には、手動によるデータベースのプロビジョニングとスキーマ管理が必要です。

## 付加的な考慮事項

* Web と Business の両エディションと Federations は、2015 年の秋に廃止されます。アプリケーションの移行の一環として、Basic、Standard、Premium の各エディションのパフォーマンス テストを実施することを強くお勧めします。 

* SWITCH OUT ステートメントをフェデレーション メンバーに対して実行すると、実行後のデータベースは Azure SQL Database のすべての機能 (エディション更新、バックアップ、PITR、監査など) を活用できるようになります。 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
[フェデレーションのルートからシャード マップ マネージャーを作成する]:#create-shard-map-manager
[既存のアプリケーションを変更する]:#Modify-the-Existing-Application
[既存のフェデレーション メンバーに対して SWITCH OUT を実行する]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=47-->
 