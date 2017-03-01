---
title: "Azure SQL Database - 監査 | Microsoft Docs"
description: "Azure SQL Database 監査では、データベース イベントを追跡して Azure Storage アカウントの監査ログに書き込みます。"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>SQL Database 監査の概念
Azure SQL Database 監査では、データベース イベントを追跡して Azure Storage アカウントの監査ログに書き込みます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査は、標準準拠を強化し促進しますが、準拠を保証するものではありません。 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。

* [Azure SQL Database 監査の概要]
* [データベースに対する監査を設定する]
* [監査ログとレポートを分析する]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1">Azure SQL Database 監査の概要</a>
SQL Database 監査により、以下のことが可能になります。

* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

**監査方法**は&2; つあります。

* **BLOB 監査** - Azure Blob ストレージにログが書き込まれます。 これは新しい監査方法であり、パフォーマンスが高く、粒度の高いオブジェクト レベルの監査をサポートし、コスト効率に優れています。
* **テーブル監査** - Azure Table Storage にログが書き込まれます。

> [!IMPORTANT]
> 新しい BLOB 監査の導入により、サーバーの監査ポリシーがデータベースによって継承される方法が大幅に変更されました。 

さまざまな種類のイベント カテゴリの監査を構成できます。

* Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal を使用した監査](sql-database-auditing-portal.md)に関するページを参照してください。
* PowerShell を用いて監査を構成および管理する方法については、[PowerShell を使用した監査](sql-database-auditing-powershell.md)に関するページを参照してください。
* REST API を使用して監査を構成および管理する方法については、[REST API を使用した監査](sql-database-auditing-rest.md)に関するページを参照してください。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。 既定のサーバー監視ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

## <a name="blob-auditing"></a>BLOB 監査

サーバーの BLOB 監査が有効になっている場合は、以下の設定に関係なく、データベース (サーバー上のすべてのデータベース) に対して常に監査が適用されます。
    - データベース監査設定。
    - データベース ブレードで [サーバーから設定を継承] チェックボックスがオンになっているかどうか。

> [!IMPORTANT]
> サーバー上だけでなくデータベース上でも BLOB 監査を有効にした場合であっても、サーバーの BLOB 監査の設定が無効になったり変更されたりすることは**ありません**。どちらの監査も並行して存在します。 つまり、データベースは並行して&2; 回監査されることになります (1 回はサーバー ポリシー、もう&1; 回はデータベース ポリシーによって監査されます)。

次の場合を除き、サーバー BLOB 監査とデータベース BLOB 監査の両方を有効にすることは避けてください。
 * 特定のデータベースに対して異なる "*ストレージ アカウント*" または "*リテンション期間*" を使用する必要がある場合。
 * 特定のデータベースについて、監査対象とするイベントの種類またはカテゴリが、このサーバー上の他のデータベースで監査対象としたイベントの種類またはカテゴリと異なる場合 (たとえば、特定のデータベースに対してのみテーブルの挿入を監査する必要がある場合)。

これらに該当しない場合は、サーバー レベルの BLOB 監査のみを有効にし、すべてのデータベースに対してデータベース レベルの監査を無効にすることをお勧めします。

## <a name="table-auditing"></a>テーブル監査

サーバー レベルのテーブル監査が有効になっているときは、データベース ブレードの [サーバーから設定を継承] チェックボックスがオンになっている場合に限り、データベースに監査が適用されます (このボックスは、既存のデータベースと新しく作成されるデータベースすべてについて既定でオンになっています)。

- このチェックボックスが "*オン*" の場合、データベースの監査設定に加えられた変更は、このデータベースのサーバー設定を**上書き**します。

- このチェックボックスが "*オフ*" になっていて、データベースレベルの監査が無効になっている場合、データベースは監査されません。

## <a name="analyze-audit-logs-and-reports"></a>監査ログとレポートを分析する
監査ログは、設定時に選択した Azure ストレージ アカウントで集計されます。

[Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用して監査ログを調査できます。

## <a name="next-steps"></a>次のステップ

* Azure Portal を使用して監査を構成および管理する方法については、[Azure Portal での監査の構成](sql-database-auditing-portal.md)に関するページを参照してください。
* PowerShell を使用して監査を構成および管理する方法については、[PowerShell を使用した監査の構成](sql-database-auditing-powershell.md)に関するページを参照してください。
* REST API を使用して監査を構成および管理する方法については、[REST API を使用した監査の構成](sql-database-auditing-rest.md)に関するページを参照してください。


<!--Anchors-->
[Azure SQL Database 監査の概要]: #subheading-1
[データベースに対する監査を設定する]: #subheading-2
[監査ログとレポートを分析する]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

