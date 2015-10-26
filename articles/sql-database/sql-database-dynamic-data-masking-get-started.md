<properties 
   pageTitle="SQL Database 動的データ マスクの使用 (Azure プレビュー ポータル)" 
   description="Azure プレビュー ポータルでの SQL Database 動的データ マスクの使用方法" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# SQL Database 動的データ マスクの使用 (Azure プレビュー ポータル)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## 概要

SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。動的データ マスクは、V12 バージョンの Azure SQL Database の Basic、Standard と Premium の各サービス階層でプレビューとして使用できます。

動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。

たとえば、コール センターのサポート担当者は、社会保障番号やクレジット カード番号の一部の数字から電話の相手を特定できますが、このようなデータ項目をサポート担当者にすべて公開してはなりません。開発者は、結果セットの社会保障番号やクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを各クエリ結果に適用するように定義できます。別の例として、開発者は、適切なデータ マスクを使用して個人を特定できる情報 (PII) データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

## SQL Database 動的データ マスクの基礎

SQL Database 構成ブレードの動的データ マスク操作を選択することで、Azure プレビュー ポータルで動的データ マスク ポリシーを設定します。動的データ マスクを設定する前に、["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用しているかどうかを確認します。


### 動的データ マスクのアクセス許可

動的データ マスクを構成できるのは、Azure Database 管理者、サーバー管理者、またはセキュリティ責任者の各ロールです。

### 動的データ マスク ポリシー

* **特権ログイン** - SQL クエリ結果に含まれるマスクされていないデータを取得するログインのセット。
  
* **マスク ルール** - マスクされる指定のフィールドと使用されるマスク関数を定義するルールのセット。データベース テーブル名と列名を使用して、指定のフィールドを定義できます。

* **マスク関数** - さまざまなシナリオに対応してデータの公開を制御する方法のセット。

| マスク関数 | マスク ロジック |
|----------|---------------|
| **既定値** |**指定のフィールドのデータ型に応じたフル マスク**<br/><br/>• 文字列データ型 (nchar、ntext、nvarchar) のフィールドのサイズが 8 文字未満の場合は、XXXXXXXX またはそれ未満の数の X を使用します。<br/>• 数値データ型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) の場合は、値 0 を使用します。<br/>• 日付/時刻データ型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) の場合は、01-01-1900 を使用します。<br/>• SQL バリアントの場合は、現在の型の既定値が使用されます。<br/>• XML の場合は、ドキュメント <masked/> が使用されます。<br/>• 特殊なデータ型 (タイムスタンプ テーブル、hierarchyid、GUID、binary、image、varbinary 空間型) の場合は、空の値を使用します。
| **クレジット カード** |クレジット カードのフォームでプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXXX-XXXX-XXXX-1234|
| **社会保障番号** |米国の社会保障番号のフォームでプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 2 桁を公開するマスク方法**。<br/><br/>XXX-XX-XX12 |
| **電子メール** | 電子メール アドレスのフォームでプレフィックスとして定数文字列を使用し、**最初の文字を公開し、ドメインを XXX.com に交換するマスク方法**。<br/><br/>aXX@XXXX.com |
| **ランダムな数値** | 選択した境界と実際のデータ型に応じて**乱数を生成するマスク方法**。指定された境界が等しい場合、マスク関数は定数になります。<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **カスタム テキスト** | 間にカスタム埋め込み文字列を追加し、**最初と最後の文字を公開するマスク方法**。<br/>プレフィックス [埋め込み] サフィックス<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### セキュリティが有効な接続文字列

["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用する場合、変更した接続文字列を使用するには既存のクライアント (例: アプリケーション) を更新する必要があります。詳細については、[こちら](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)をご覧ください。

## Azure プレビュー ポータル使用によるデータベースの動的データ マスク設定

1. [https://portal.azure.com](https://portal.azure.com) で Azure プレビュー ポータルを起動します。
	 
2. マスクするデリケートなデータを含むデータベースの構成ブレードに移動します。
	
3. **[動的データ マスク]** タイルをクリックして、**[動的データ マスク]** 構成ブレードを起動します。

	* この方法に代わって、下にスクロールして **[操作]** セクションを表示し、**[動的データ マスク]** をクリックすることもできます。
	 
	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. **[動的データ マスク]** 構成ブレードで、**[マスクの追加]** をクリックし、**[マスク ルールの追加]** 構成ブレードを開きます。

	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. **[テーブル]** と **[列]** を選択し、マスクする指定のフィールドを定義します。

6. 機密データのマスク カテゴリの一覧から **[マスク フィールド形式]** を選択します。

	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. データ マスク ルール ブレードの **[保存]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。

8. マスクされていないデリケートなデータへのアクセスを許可する特権ログインを入力します。
 
	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、データベースの照会に使用するアプリケーションの SQL ログインを追加します。デリケートなデータの公開を最小限に抑えるには、この一覧に含めるログインの数を最小限にすることを強くお勧めします。

9. データ マスク構成ブレードの **[保存]** をクリックして、新しいマスク ルールまたは更新されたマスク ポリシーを保存します。

10. ["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用する場合、変更した接続文字列を使用するには既存のクライアント (例: アプリケーション) を更新する必要があります。詳細については、「[ダウンレベル クライアント](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)」をご覧ください。

## Powershell コマンドレットを使用して、データベースの動的データ マスクを設定する

「[Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt163521.aspx)」をご覧ください。


## REST API を使用してデータベース用の動的データ マスクを設定する

「[Azure SQL データベースの操作](https://msdn.microsoft.com/library/dn505719.aspx)」を参照してください。

<!---HONumber=Oct15_HO3-->