---
title: 動的データ マスク
description: 動的データ マスキングを使用し、SQL Database と Data Warehouse に対する権限を持たないユーザーに対して機密データをマスクすることにより、機密データの公開を制限します
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: 149c42829762920583948958da8252a01e35ef1f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721916"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Azure SQL Database と Data Warehouse に対する動的データ マスキング

SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 

動的データ マスクでは、公開するデリケートなデータの量を指定することで、デリケートなデータに対する未承認のアクセスを防ぎ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能です。これにより、データベース内のデータはそのままで、指定されたデータベース フィールドに対するクエリの結果セットで機微なデータを非表示にすることができます。

たとえば、コール センターのサポート担当者は、クレジット カード番号の一部の数字から電話の相手を特定できますが、このようなデータ項目をサポート担当者にすべて公開してはなりません。 クエリの結果セットのクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを定義できます。 別の例として、開発者は、適切なデータ マスクを定義し、個人を特定できる情報 (PII) データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

## <a name="dynamic-data-masking-basics"></a>動的データ マスキングの基礎

SQL Database の構成ブレードまたは設定ブレードで動的データ マスク操作を選ぶことにより、Azure Portal で動的データ マスク ポリシーを設定します。 この機能は、SQL DW 用ポータルを使用して設定することはできません (Powershell または REST API を使用してください)

### <a name="dynamic-data-masking-permissions"></a>動的データ マスクのアクセス許可

動的データ マスクを構成できるのは、Azure SQL Database 管理者、サーバー管理者、または [SQL セキュリティ管理者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)の各ロールです。

### <a name="dynamic-data-masking-policy"></a>動的データ マスク ポリシー

* **マスクから除外する SQL ユーザー** - SQL クエリの結果でデータがマスクされない SQL ユーザーまたは AAD の ID のセット。 管理者特権を持つユーザーは常にマスクから除外され、マスクのない元のデータを見ることができます。
* **マスク ルール** - マスクされる指定のフィールドと使用されるマスク関数を定義するルールのセット。 データベースのスキーマ名、テーブル名、列名を使用し、指定のフィールドを定義できます。
* **マスク関数** - さまざまなシナリオに対応してデータの公開を制御する方法のセット。

| マスク関数 | マスク ロジック |
| --- | --- |
| **[Default]** |**指定のフィールドのデータ型に応じたフル マスク**<br/><br/>• 文字列データ型 (nchar、ntext、nvarchar) のフィールドのサイズが 4 文字未満の場合は、XXXX またはそれ未満の数の X を使用します。<br/>• 数値データ型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real) の場合は、値 0 を使用します。<br/>• 日付/時刻データ型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time) の場合は、01-01-1900 を使用します。<br/>• SQL バリアントの場合は、現在の型の既定値が使用されます。<br/>• XML の場合は、ドキュメント \<masked/> が使用されます。<br/>• 特殊なデータ型 (タイムスタンプ テーブル、hierarchyid、GUID、binary、image、varbinary 空間型) の場合は、空の値を使用します。 |
| **クレジット カード** |クレジット カードの形式でプレフィックスとして定数文字列を追加し、**指定のフィールドの末尾 4 桁を公開するマスク方法**。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **電子メール** |電子メール アドレスの形式でプレフィックスとして定数文字列を使用して、**最初の文字を公開し、ドメインを XXX.com に置き換えるマスク方法**。<br/><br/>aXX@XXXX.com |
| **ランダムな数値** |選択した境界と実際のデータ型に応じて**乱数を生成するマスク方法**。 指定された境界が等しい場合、マスク関数は定数になります。<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **カスタム テキスト** |間にカスタム埋め込み文字列を追加し、**最初と最後の文字を公開するマスク方法**。 元の文字列が公開されたプレフィックスやサフィックスより短い場合、埋め込み文字列のみが使用されます。 <br/>prefix[padding]suffix<br/><br/>![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>マスクが推奨されるフィールド

DDM の推奨エンジンでは、データベースの特定のフィールドに「機密データの可能性あり」の注意が付けられます。この注意を参考にマスク候補を選択できます。 ポータルの動的データ マスク ブレードには、データベースの推奨列が表示されます。 1 つまたは複数の列の **[マスクの追加]** をクリックし、 **[保存]** をクリックするだけでそれらのフィールドにマスクを適用できます。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用して、ご使用のデータベースの動的データ マスクを設定する

「 [Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql)」をご覧ください。

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>REST API を使用してデータベース用の動的データ マスクを設定する

「[Azure SQL Database の操作](https://docs.microsoft.com/rest/api/sql/)」を参照してください。
