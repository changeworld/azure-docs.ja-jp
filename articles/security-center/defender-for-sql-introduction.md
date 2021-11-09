---
title: Microsoft Defender for SQL - 利点と機能
description: Microsoft Defender for SQL の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 51e031034e344de6e1e8b12874a443fbf51afb42
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057463"
---
# <a name="introduction-to-microsoft-defender-for-sql"></a>Microsoft Defender for SQL の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for SQL には、Microsoft Defender for Cloud の[データ セキュリティ パッケージ](../azure-sql/database/azure-defender-for-sql.md)を拡張して、データベースとそのデータを場所を問わずにセキュリティで保護する 2 つの Microsoft Defender プランが含まれています。 Microsoft Defender for SQL には、データベースの潜在的な脆弱性を検出し、軽減する機能や、データベースに対する脅威を示す異常な行動を検出する機能が含まれています。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|**Microsoft Defender for Azure SQL database servers** - 一般提供 (GA)<br>**Microsoft Defender for SQL servers on machines** - 一般提供 (GA) |
|価格:|**Microsoft Defender for SQL** を構成する 2 つのプランの課金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。|
|保護される SQL のバージョン:|[Azure 仮想マシン上の SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Azure Arc 対応サーバーでの SQL Server](/sql/sql-server/azure-arc/overview)<br>Windows マシン上のオンプレミス SQL サーバー (Azure Arc なし)<br>Azure SQL [単一データベース](../azure-sql/database/single-database-overview.md)および[エラスティック プール](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (旧称 SQL DW) 専用 SQL プール](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet (**部分的**: SQL サーバーのアラートと脆弱性評価のサブセット。 Behavioral Threat Protection は利用できません。)|
|||

## <a name="what-does-microsoft-defender-for-sql-protect"></a>Microsoft Defender for SQL の保護対象

**Microsoft Defender for SQL** は、2 つの異なる Microsoft Defender プランで構成されています。

- **Microsoft Defender for Azure SQL database servers** では、以下を保護します。
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse の専用 SQL プール](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Microsoft Defender for SQL servers on machines** では、Azure ネイティブの SQL Server の保護を拡張してハイブリッド環境を完全にサポートし、Azure、他のクラウド環境、さらにはオンプレミスのマシンでホストされている SQL サーバー (サポートされているすべてのバージョン) を保護します。
    - [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - オンプレミスの SQL サーバー:
        - [Azure Arc 対応 SQL Server (プレビュー)](/sql/sql-server/azure-arc/overview)
        - [Windows マシン上で実行される SQL Server (Azure Arc なし)](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Microsoft Defender for SQL の利点

この 2 つのプランには、データベースの潜在的な脆弱性を特定して軽減する機能や、データベースに対する脅威を示している可能性がある異常なアクティビティを検出する機能が含まれています。

脆弱性評価サービスは、データベースの潜在的な脆弱性を検出、追跡、修復するのに役立ちます。 評価スキャンでは、SQL マシンのセキュリティ状態の概要と、セキュリティ調査結果の詳細が示されます。

- Azure SQL Database の脆弱性評価については[こちら](../azure-sql/database/sql-vulnerability-assessment.md)でご確認ください。
- マシン上の Azure SQL サーバーの脆弱性評価については[こちら](defender-for-sql-on-machines-vulnerability-assessment.md)でご確認ください。

SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威を対象に、高度な脅威防止サービスが SQL サーバーを継続的に監視します。 このサービスでは、不審なアクティビティの詳細、脅威を軽減する方法に関するガイダンス、Microsoft Sentinel で調査を続行するためのオプションについて、Microsoft Defender for Cloud のアクション指向のセキュリティ アラートが提供されます。 高度な脅威防止については[こちら](../azure-sql/database/threat-detection-overview.md)でご確認ください。

 > [!TIP]
 > SQL サーバーのセキュリティ アラートの一覧については、[アラートのリファレンス ページ](alerts-reference.md#alerts-sql-db-and-warehouse)を参照してください。


## <a name="is-there-a-performance-impact-from-deploying-microsoft-defender-for-sql-on-machines"></a>Microsoft Defender for SQL on machines をデプロイするとパフォーマンスに影響が出ますか?

**Microsoft Defender for SQL on machines** では、明確にセキュリティに焦点を当てています。 しかしながら、Microsoft にとってはお客様の事業も大切であり、お使いの SQL サーバーに対する影響を最小限に抑えるべく、パフォーマンスを優先させています。 

このサービスのアーキテクチャは、データのアップロードならびにスピードとパフォーマンスのバランスを維持するよう、分割されています。 

- 一部の検出機能はマシン上で実行することで、リアルタイムのスピードというアドバンテージが与えられます。
- クラウドで実行することで、重い計算負荷からマシンが解放される場合もあります。

ソリューションを研究室でテストし、ベンチマークの負荷に対して比較したところ、ピーク スライスに対して CPU の使用率平均が 3% でした。 現行ユーザーを対象にテレメトリを分析すると、CPU とメモリの使用率に対する影響は無視できるものです。

当然ですが、パフォーマンスは環境、マシン、負荷によって常に変化します。 上記の発言と数値は一般的なガイドラインとして提供されるものであり、個々のデプロイに対する保証ではありません。


## <a name="what-kind-of-alerts-does-microsoft-defender-for-sql-provide"></a>Microsoft Defender for SQL で提供されるアラートの種類

脅威インテリジェンスによってエンリッチされたセキュリティ アラートは、次のような場合にトリガーされます。

- **潜在的な SQL インジェクション攻撃** - アプリケーションによって、データベースにエラーのある SQL ステートメントが生成されたときに検出される脆弱性など
- **異常なデータベース アクセスとクエリ パターン** - さまざまな資格情報でのサインイン試行の失敗が異常に多い場合 (ブルートフォース試行) など
- **不審なデータベース アクティビティ** - 正当なユーザーが、クリプトマイニング C&C サーバーとの通信を行う侵害されたコンピューターから SQL Server にアクセスするなど

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。



## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for SQL について説明しました。 説明したサービスを使用するために、次の作業を行います。

- Microsoft Defender for SQL servers on machines を使用して [SQL Server の脆弱性をスキャン](defender-for-sql-usage.md)する
- Microsoft Defender for SQL のプレゼンテーションについては、「[あらゆる場所にある SQL サーバーを Microsoft Defender for SQL で保護する方法](https://www.youtube.com/watch?v=V7RdB6RSVpc)」をご覧ください。
