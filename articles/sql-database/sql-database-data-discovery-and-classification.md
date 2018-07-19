---
title: Azure SQL Database のデータの検出と分類 | Microsoft Docs
description: Azure SQL Database のデータの検出と分類
services: sql-database
author: giladmit
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: giladm
ms.openlocfilehash: cc093bebb4b3c39140d6fa5370a78d59168990fa
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950812"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Azure SQL Database のデータの検出と分類
データの検出と分類 (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの**検出**、**分類**、**ラベル付け** & **保護**を行う高度な機能が用意されます。
最も機微なデータの検出と分類 (ビジネス/金融、医療、PII など) は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。
* データのプライバシー基準および規制のコンプライアンス要件を満たす支援。
* さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
* 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。

データの検出と分類は、高度な SQL セキュリティ機能の統合パッケージである [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) の一部です。 データの検出と分類は、SQL ATP ポータルを使って一元的にアクセスおよび管理できます。

> [!NOTE]
> このドキュメントは、Azure SQL Database にのみ関係があります。 SQL Server (オンプレミス) については、「[SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999)」(SQL のデータの検出と分類) をご覧ください。

## <a id="subheading-1"></a>データの検出と分類とは
データの検出と分類では、一連の高度なサービスと新しい SQL 機能が導入され、データベースだけでなくデータの保護も目的とする新しい SQL Information Protection パラダイムが形成されます。
* **検出と推奨事項** – 分類エンジンは、データベースをスキャンして、機微なデータが含まれる可能性のある列を識別します。 その後、Azure Portal を使ってレビューし、適切な分類の推奨事項を適用するための簡単な方法を提供します。
* **ラベル付け** – 機密度分類ラベルは、SQL エンジンに導入された新しい分類メタデータ属性を使って、列に永続的にタグを付けることができます。 このメタデータは、機密度に基づく高度な監査と保護のシナリオに利用できます。
* **クエリ結果セットの機密度** – 監査のため、クエリ結果セットの機密度がリアルタイムで計算されます。
* **可視性** データベースの分類状態は、ポータルで詳細ダッシュボードで見ることができます。 さらに、コンプライアンスと監査の目的や他のニーズに使われる Excel 形式のレポートをダウンロードできます。

## <a id="subheading-2"></a>機密性の高い列の検出、分類、ラベル付け
次のセクションでは、データベースの機密データを含む列を検出、分類、ラベル付けする手順、およびデータベースの現在の分類状態を表示してレポートをエクスポートする手順について説明します。

分類には 2 つのメタデータ属性が含まれます。
* ラベル – メインの分類属性であり、列に格納されているデータの機密度レベルを定義するために使われます。  
* 情報の種類 – 列に格納されているデータの種類に対する追加の細分性を提供します。

## <a name="classify-your-sql-database"></a>SQL Database を分類する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. [Azure SQL Database] ウィンドウの [セキュリティ] で、**[Advanced Threat Protection]** に移動します。 [Advanced Threat Protection] をクリックして有効にし、**[データの検出と分類 (プレビュー)]** カードをクリックします。

   ![データベースのスキャン](./media/sql-data-discovery-and-classification/data_classification.png)

3. **[概要]** タブには、データベースの現在の分類状態の概要が表示されます。これには分類済みのすべての列の詳細な一覧が含まれ、これをフィルター処理して、特定のスキーマの部分、情報の種類、ラベルだけを表示することもできます。 まだどの列も分類していない場合は、[手順 5 に進みます](#step-5)。

   ![現在の分類状態の概要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Excel 形式でレポートをダウンロードするには、ウィンドウ上部のメニューの **[エクスポート]** オプションをクリックします。

   ![Excel へのエクスポート](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5.  <a id="step-5"></a>データの分類を始めるには、ウィンドウの上部にある **[分類]** タブをクリックします。

    ![データを分類する](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. 分類エンジンがデータベースで機密データを含む可能性のある列をスキャンし、**推奨される列の分類**のリストを提供します。 分類の推奨事項を表示して適用するには:

    * 推奨される列の分類のリストを表示するには、ウィンドウの下部にある推奨事項パネルをクリックします。

      ![データを分類する](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

    * 推奨事項の一覧を確認し、特定の列に対する推奨事項を受け入れるには、該当する行の左側にあるチェック ボックスをオンにします。 推奨テーブルのヘッダーのチェック ボックスをオンにして、"*すべての推奨事項*" を受け入れるようにマークすることもできます。

       ![推奨事項の一覧を確認する](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

    * 選んだ推奨事項を適用するには、青い **[Accept selected recommendations]\(選択した推奨事項を受け入れる\)** ボタンをクリックします。

      ![推奨事項の適用](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. また、推奨事項に基づく分類の代わりに、または推奨事項に基づく分類に加えて、列を**手動で分類**することもできます。

    * ウィンドウの上部メニューの **[Add classification]\(分類の追加\)** をクリックします。

      ![分類を手動で追加する](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

    * 表示されるコンテキスト ウィンドウで、分類するスキーマ > テーブル > 列、情報の種類、機密度ラベルを選びます。 コンテキスト ウィンドウの下部にある青い **[Add classification]\(分類の追加\)** ボタンをクリックします。

      ![分類する列を選択する](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 分類を完了し、新しい分類メタデータでデータベースの列に永続的にラベル (タグ) を付けるには、ウィンドウの上部メニューの **[保存]** をクリックします。

   ![保存](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>機密データへのアクセスの監査

機密データへのアクセスを監視できることは、情報保護パラダイムの重要な要素の 1 つです。 [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) が拡張され、監査ログに *data_sensitivity_information* という名前の新しいフィールドが追加されています。このフィールドには、クエリによって返された実際のデータの重大度の分類 (ラベル) が記録されます。

![監査ログ](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>自動/プログラムによる分類

T-SQL を使って、列の分類を追加/削除し、データベース全体のすべての分類を取得することができます。

> [!NOTE]
> T-SQL を使ってラベルを管理するとき、列に追加されるラベルが組織の情報保護ポリシー (ポータルのレコメンデーションに表示されるラベルのセット) に存在することの検証は行われません。 したがって、その検証はユーザーが行う必要があります。

* 1 つまたは複数の列の分類の追加/更新: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/en-us/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
* 1 つまたは複数の列の分類の削除: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/en-us/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
* データベースのすべての分類の表示: [sys.sensitivity_classifications](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

## <a id="subheading-5"></a>次のステップ

- [Azure SQL Database の Advanced Threat Protection](sql-advanced-threat-protection.md) についてさらに詳しく学習します。
- 分類済みの機密データへのアクセスを監視および監査するように [Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) を構成することを検討します。

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
