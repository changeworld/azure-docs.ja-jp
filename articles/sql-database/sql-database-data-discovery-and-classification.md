---
title: "Azure SQL Database のデータの検出と分類 | Microsoft Docs"
description: "Azure SQL Database のデータの検出と分類"
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 1ff7f3509ccdaa6911cee0487d5a6d90b11eb79f
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Azure SQL Database のデータの検出と分類
データの検出と分類 (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの**検出**、**分類**、**ラベル付け** & **保護**を行う高度な機能が用意されます。
最も機微なデータの検出と分類 (ビジネス/金融、医療、PII など) は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。
* データのプライバシー基準および規制のコンプライアンス要件を満たす支援 (GDPR など)。
* さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
* 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。

## <a id="subheading-1"></a>概要
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

<br>
**SQL Database を分類するには:**

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. SQL Database の **[データの検出と分類 (プレビュー)]** 設定に移動します。

    ![ナビゲーション ウィンドウ][1]

3. **[概要]** タブには、データベースの現在の分類状態の概要が表示されます。これには分類済みのすべての列の詳細な一覧が含まれ、これをフィルター処理して、特定のスキーマの部分、情報の種類、ラベルだけを表示することもできます。 まだどの列も分類していない場合は、[手順 5 に進みます](#step-5)。

    ![ナビゲーション ウィンドウ][2]

4. Excel 形式でレポートをダウンロードするには、ウィンドウ上部のメニューの **[エクスポート]** オプションをクリックします。

    ![ナビゲーション ウィンドウ][3]

5.  <a id="step-5"></a>データの分類を始めるには、ウィンドウの上部にある **[分類]** タブをクリックします。

    ![ナビゲーション ウィンドウ][4]

6. 分類エンジンがデータベースで機密データを含む可能性のある列をスキャンし、**推奨される列の分類**のリストを提供します。 分類の推奨事項を表示して適用するには:

    * 推奨される列の分類のリストを表示するには、ウィンドウの下部にある推奨事項パネルをクリックします。

        ![ナビゲーション ウィンドウ][5]

    * 推奨事項の一覧を確認し、特定の列に対する推奨事項を受け入れるには、該当する行の左側にあるチェック ボックスをオンにします。 推奨テーブルのヘッダーのチェック ボックスをオンにして、"*すべての推奨事項*" を受け入れるようにマークすることもできます。

        ![ナビゲーション ウィンドウ][6]

    * 選んだ推奨事項を適用するには、青い **[Accept selected recommendations]\(選択した推奨事項を受け入れる\)** ボタンをクリックします。

        ![ナビゲーション ウィンドウ][7]

7. また、推奨事項に基づく分類の代わりに、または推奨事項に基づく分類に加えて、列を**手動で分類**することもできます。

    * ウィンドウの上部メニューの **[Add classification]\(分類の追加\)** をクリックします。

        ![ナビゲーション ウィンドウ][8]

    * 表示されるコンテキスト ウィンドウで、分類するスキーマ > テーブル > 列、情報の種類、機密度ラベルを選びます。 コンテキスト ウィンドウの下部にある青い **[Add classification]\(分類の追加\)** ボタンをクリックします。

        ![ナビゲーション ウィンドウ][9]

8. 分類を完了し、新しい分類メタデータでデータベースの列に永続的にラベル (タグ) を付けるには、ウィンドウの上部メニューの **[保存]** をクリックします。

    ![ナビゲーション ウィンドウ][10]

## <a id="subheading-3"></a>機密データへのアクセスの監査

機密データへのアクセスを監視できることは、情報保護パラダイムの重要な要素の 1 つです。

[Azure SQL Database Auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) が拡張され、監査ログに *data_sensitivity_information* という名前の新しいフィールドが追加されています。このフィールドには、クエリによって返された実際のデータの重大度の分類 (ラベル) が記録されます。

![ナビゲーション ウィンドウ][11]

## <a id="subheading-4"></a>次のステップ
分類済みの機密データへのアクセスを監視および監査するように [Azure SQL Database Auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) を構成することを検討します。

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
