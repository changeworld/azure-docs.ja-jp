<properties
   pageTitle="SQL Database 動的データ マスクの使用 (Azure クラシック ポータル)"
   description="Azure クラシック ポータルでの SQL Database 動的データ マスクの使用方法"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/11/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# SQL Database 動的データ マスクの使用 (Azure クラシック ポータル)

> [AZURE.SELECTOR]
- [動的データ マスク - Azure ポータル](sql-database-dynamic-data-masking-get-started.md)

## 概要

SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。Azure SQL Database の V12 バージョンでは、動的データ マスクがサポートされています。

動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。

たとえば、コール センターのサポート担当者は、社会保障番号やクレジット カード番号の一部の数字から電話の相手を特定できますが、このようなデータ項目をサポート担当者にすべて公開してはなりません。クエリの結果セットの社会保障番号やクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを定義できます。別の例として、開発者は、適切なデータ マスクを定義し、個人を特定できる情報 (PII) データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

## SQL Database 動的データ マスクの基礎

Azure クラシック ポータルの [監視とセキュリティ] タブでデータベース用の動的データ マスク ポリシーを設定します。


> [AZURE.NOTE] Azure ポータルで動的データ マスクを設定する方法について詳しくは、「[SQL Database 動的データ マスク (Azure ポータル)](sql-database-dynamic-data-masking-get-started.md)」を参照してください。


### 動的データ マスクのアクセス許可

動的データ マスクを構成できるのは、Azure Database 管理者、サーバー管理者、またはセキュリティ責任者の各ロールです。

### 動的データ マスク ポリシー

* **マスクから除外する SQL ユーザー** - SQL クエリの結果でデータがマスクされない SQL ユーザーまたは AAD の ID のセット。管理者特権を持つユーザーは常にマスクから除外され、マスクのない元のデータを見ることができることに注意してください。

* **マスク ルール** - マスクされる指定のフィールドと使用されるマスク関数を定義するルールのセット。データベースのスキーマ名、テーブル名、列名を使用し、指定のフィールドを定義できます。

* **マスク関数** - さまざまなシナリオに対応してデータの公開を制御する方法のセット。

| マスク関数 | マスク ロジック |
|----------|---------------|
| **既定値** |**指定のフィールドのデータ型に応じたフル マスク**<br/><br/>• 文字列データ型 (nchar、ntext、nvarchar) のフィールドのサイズが 4 文字未満の場合は、XXXX またはそれ未満の数の X を使用します。<br/>• 数値データ型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) の場合は、値 0 を使用します。<br/>• 日付/時刻データ型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) の場合は、01-01-1900 を使用します。<br/>• SQL バリアントの場合は、現在の型の既定値が使用されます。<br/>• XML の場合は、ドキュメント <masked/> が使用されます。<br/>• 特殊なデータ型 (タイムスタンプ テーブル、hierarchyid、GUID、binary、image、varbinary 空間型) の場合は、空の値を使用します。
| **クレジット カード** |クレジット カードのフォームでプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXXX-XXXX-XXXX-1234|
| **社会保障番号** |米国の社会保障番号のフォームでプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXX-XX-1234 |
| **電子メール** | 電子メール アドレスのフォームでプレフィックスとして定数文字列を使用し、**最初の文字を公開し、ドメインを XXX.com に交換するマスク方法**。<br/><br/>aXX@XXXX.com |
| **ランダムな数値** | 選択した境界と実際のデータ型に応じて**乱数を生成するマスク方法**。指定された境界が等しい場合、マスク関数は定数になります。<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **カスタム テキスト** | 間にカスタム埋め込み文字列を追加し、**最初と最後の文字を公開するマスク方法**。元の文字列が公開されたプレフィックスやサフィックスより短い場合、埋め込み文字列のみが使用されます。 <br/>プレフィックス[埋め込み]サフィックス<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## Azure クラシック ポータル使用によるデータベースの動的データ マスク設定

1. [https://manage.windowsazure.com](https://manage.windowsazure.com) で Azure クラシックを起動します。

2. マスクするデータベースをクリックしてから **[監査とセキュリティ]** タブをクリックします。

3. **[動的データ マスク]** で、**[有効]** をクリックして動的データ マスク機能を有効にします。

4. マスクから除外し、マスクされていない機密データへのアクセスを与える SQL ユーザーまたは AAD の ID を入力します。ユーザーをセミコロンで区切った一覧にします。管理者特権を持つユーザーは常にマスクされていない元のデータにアクセスできることに注意してください。

	>[AZURE.TIP] アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、アプリケーションストアでデータベースの照会に使用される SQL ユーザーまたは AAD の ID を追加します。デリケートなデータの公開を最小限に抑えるには、この一覧に含める特権ユーザーの数を最小限にすることを強くお勧めします。

	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. ページの下部にあるメニュー バーの **[マスクの追加]** をクリックして、マスク ルール構成ウィンドウを開きます。

6. ドロップダウン リストから **[スキーマ]**、**[テーブル]**、**[列]** を選択し、マスクする指定のフィールドを定義します。

7. 機密データのマスク カテゴリの一覧から **[マスク関数]** を選択します。

	![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. データ マスク ルール ウィンドウの **[OK]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。

9. **[保存]** をクリックして、新しいマスク ルールまたは更新されたマスク ポリシーを保存します。


## Transact-SQL ステートメントを使用したデータベースの動的データ マスクの設定

「[Dynamic Data Masking (動的データ マスク)](https://msdn.microsoft.com/library/mt130841.aspx)」をご覧ください。

## Powershell コマンドレットを使用して、データベースの動的データ マスクを設定する

「[Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)」をご覧ください。

## REST API を使用してデータベース用の動的データ マスクを設定する

「[Azure SQL Database の操作](https://msdn.microsoft.com/library/dn505719.aspx)」を参照してください。

<!---HONumber=AcomDC_0413_2016-->