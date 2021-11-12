---
title: 自動フェールオーバー グループ
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 自動フェールオーバー グループを使用すると、サーバー上のデータベースのグループ、またはマネージド インスタンス上のすべてのデータベースの geo レプリケーションと自動/調整フェールオーバーを管理できます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: 2d4aa937a29170ff1ebabc9b1e0bbe316b526189
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894129"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-geo-failover-of-multiple-databases"></a>自動フェールオーバー グループを使用して、複数のデータベースの透過的かつ調整された geo フェールオーバーを有効にする
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

自動フェールオーバー グループ機能を使用すると、サーバー上のデータベースのグループ、またはマネージド インスタンス内のすべてのデータベースの別のリージョンへのレプリケーションやフェールオーバーを管理できます。 これは、既存の[アクティブ geo レプリケーション](active-geo-replication-overview.md)機能に基づく宣言型の抽象化であり、geo レプリケーション対応データベースの大規模なデプロイと管理を簡素化するように設計されています。 geo フェールオーバーは手動で開始するか、ユーザー定義ポリシーに基づいて Azure サービスに委任できます。 後者のオプションの場合、プライマリ リージョンで発生した致命的な障害やその他の計画外のイベントにより SQL Database や SQL Managed Instance の可用性がすべてまたは一部失われたときに、セカンダリ リージョンの複数の関連データベースを自動的に復元できます。 フェールオーバー グループには、通常同じアプリケーションによって使用される、1 つまたは複数のデータベースを含めることができます。 さらに、読み取り可能なセカンダリ データベースを使用して、読み取り専用クエリ ワークロードをオフロードできます。

> [!NOTE]
> 自動フェールオーバー グループは、グループ内のすべてのデータベースの geo レプリケーションを、別のリージョン内の 1 つのセカンダリ サーバーまたはインスタンスにのみサポートします。 同じプライマリ レプリカに対して複数Azure SQL Database geo セカンダリ レプリカ (同じリージョンまたは異なるリージョン) を作成する必要がある場合は、アクティブ geo レプリケーション[を使用します](active-geo-replication-overview.md)。
> 
> 自動フェールオーバー グループは現在、[Hyperscale](service-tier-hyperscale.md) サービス レベルではサポートされていません。 Hyperscale データベースの地理的フェールオーバーの場合は、[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用してください。

自動フェールオーバー ポリシーで自動フェールオーバー グループを使用している場合、グループ内の 1 つ以上のデータベースに影響を与える停止により、自動 geo フェールオーバーが発生します。 通常、これらは組み込みの高可用性インフラストラクチャでは自動的に軽減できない停止です。 geo フェールオーバー トリガーの例としては、コンピューティング ノードでの OS カーネル メモリ リークが原因で SQL Database テナント リングまたは制御リングがダウンした場合のインシデントや、ハードウェアの日常的な使用停止中に間違ったネットワーク ケーブルが誤って切断されたため、1 つ以上のテナント リングがダウンしたインシデントが含まれます。 詳細については、[SQL Database の高可用性](high-availability-sla.md)に関する記事を参照してください。

さらに、自動フェールオーバー グループは、geo フェールオーバー中に変更されない読み取り/書き込みリスナーと読み取り専用リスナーのエンド ポイントを提供します。 手動または自動フェールオーバーのアクティブ化を使用する場合でも、geo フェールオーバーでは、グループ内のすべてのセカンダリ データベースがプライマリ ロールに切り替まれます。 geo フェールオーバーが完了すると、DNS レコードが自動的に更新され、エンドポイントが新しいリージョンにリダイレクトされます。 geo フェールオーバー RPO と RTO については、「ビジネス継続性の [概要」を参照してください](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

自動フェールオーバー ポリシーで自動フェールオーバー グループを使用している場合、サーバーまたはマネージド インスタンス上のデータベースに影響を与える停止により、自動 geo フェールオーバーが発生します。 

自動フェールオーバー グループは、以下を使用して管理できます。

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: フェールオーバー グループ](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: フェールオーバー グループ](scripts/add-database-to-failover-group-powershell.md)
- [REST API: フェールオーバー グループ](/rest/api/sql/failovergroups)

フェールオーバー グループを構成する場合は、geo フェールオーバー後に geo セカンダリが新しいプライマリになったときに、セカンダリの認証とネットワーク アクセスが正しく機能するように設定されていることを確認します。 詳細については、 [障害復旧後の SQL Database のセキュリティ](active-geo-replication-security-configure.md)に関するページを参照してください。

完全なビジネス継続性を実現するには、リージョン データベースの冗長性を追加する方法は、ソリューションの一部に限定されます。 致命的な障害の後にアプリケーション (サービス) をエンド ツー エンドで復旧するには、そのサービスと依存しているサービスを構成するすべてのコンポーネントを復旧する必要があります。 このようなコンポーネントの例には、クライアント ソフトウェア (カスタム JavaScript が設定されたブラウザーなど)、Web フロント エンド、ストレージ、DNS などがあります。 すべてのコンポーネントが同じ障害に対する復元性を備えており、アプリケーションの目標復旧時間 (RTO) 以内に利用可能になることが重要です。 そのため、依存するサービスをすべて特定し、これらのサービスが提供する保証と機能について把握しておく必要があります。 そのうえで、依存するサービスのフェールオーバー中もサービスが確実に機能するように対策を講じる必要があります。 ディザスター リカバリーのソリューション設計の詳細については、[アクティブ geo レプリケーションを使用したディザスター リカバリー用のクラウド ソリューションの設計](designing-cloud-solutions-for-disaster-recovery.md)に関するページを参照してください。

## <a name="failover-group-terminology-and-capabilities"></a><a name="terminology-and-capabilities"></a> フェールオーバー グループの用語と機能

- **フェールオーバー グループ (FOG)**

  フェールオーバー グループは、プライマリ リージョンでの機能停止により、すべてまたは一部のプライマリ データベースが使用できなくなった場合に、1 つの単位として別のリージョンにフェールオーバーできるマネージド インスタンス内の、または単一サーバーによって管理されるデータベースの名前付きグループです。 SQL Managed Instance に対して作成された場合、フェールオーバー グループには、そのインスタンス内のすべてのユーザー データベースが含まれています。そのため、インスタンス上に構成できるフェールオーバー グループは 1 つのみとなります。
  
  > [!IMPORTANT]
  > フェールオーバー グループの名前は、`.database.windows.net` ドメイン内でグローバルに一意である必要があります。

- **サーバー**

  論理サーバー上のユーザー データベースの一部またはすべてがフェールオーバー グループに配置できます。 また、サーバーでは、単一サーバー上の複数のフェールオーバー グループがサポートされます。

- **プライマリ**

  フェールオーバー グループのプライマリ データベースをホストするサーバーまたはマネージド インスタンス。

- **セカンダリ**

  フェールオーバー グループのセカンダリ データベースをホストするサーバーまたはマネージド インスタンス。 セカンダリをプライマリと同じリージョンに配置することはできません。

- **フェールオーバー グループへの単一データベースの追加**

  同じサーバー上の複数の単一データベースを、同じフェールオーバー グループに配置することができます。 フェールオーバー グループに単一データベースを追加すると、セカンダリ サーバー上の同じエディションとコンピューティング サイズを使用してセカンダリ データベースが自動的に作成されます。 フェールオーバー グループを作成したときに、そのサーバーを指定しています。 セカンダリ サーバーにセカンダリ データベースが既に存在するデータベースを追加する場合、その geo レプリケーション リンクがグループに継承されます。 フェールオーバー グループの一部でないサーバーにセカンダリ データベースが既に存在するデータベースを追加すると、セカンダリ サーバーに新しいセカンダリが作成されます。

  > [!IMPORTANT]
  > 既存のセカンダリ データベースである場合を除き、セカンダリ サーバーに同じ名前のデータベースがないことを確認してください。 SQL Managed Instance のフェールオーバー グループでは、すべてのユーザー データベースがレプリケートされます。 フェールオーバー グループで、レプリケーション対象としてユーザー データベースのサブセットを選択することはできません。

- **エラスティック プール内のデータベースをフェールオーバー グループに追加する**

  1 つのエラスティック プール内のすべてまたは複数のデータベースを同じフェールオーバー グループに置くことができます。 プライマリ データベースがエラスティック プール内にある場合、セカンダリは同じ名前のエラスティック プール (セカンダリ プール) に自動的に作成されます。 セカンダリ サーバーに全く同じ名前のエラスティック プールが含まれており、フェールオーバー グループによって作成されるセカンダリ データベースをホストするのに十分な空き容量があることを確認する必要があります。 セカンダリ プールにセカンダリ データベースが既に存在するプールにデータベースを追加する場合、その geo レプリケーション リンクがグループに継承されます。 フェールオーバー グループの一部でないサーバーにセカンダリ データベースが既に存在するデータベースを追加すると、セカンダリ プールに新しいセカンダリが作成されます。
  
- **初期シード処理**

  データベース、エラスティック プール、またはマネージド インスタンスをフェールオーバー グループに追加する場合、データ レプリケーションが開始される前に、初期シード処理フェーズがあります。 初期シード処理フェーズは、最も時間がかかり、最も負荷の高い操作です。 初期シード処理が完了すると、データは同期され、その後はそれ以降のデータ変更のみがレプリケートされます。 初期シード処理が完了するまでにかかる時間は、データのサイズ、レプリケートされたデータベースの数、プライマリ データベースにかかる負荷、プライマリとセカンダリ間のリンクの速度によって異なります。 通常の環境において可能なシード処理の速度は、SQL Database では最大 500 GB/時、SQL Managed Instance では最大 360 GB/時になります。 シード処理は、すべてのデータベースに対して並列で実行されます。

  SQL Managed Instance の場合は、初期シード処理フェーズの時間を見積もるときに、2 つのインスタンス間の Express Route リンクの速度を考慮してください。 2 つのインスタンス間のリンクの速度が必要な速度よりも遅い場合は、シードする時間が大きな影響を受けそうになります。 提示したシード処理速度、データベースの数、データの合計サイズ、およびリンク速度を使用して、データ レプリケーションが開始される前に初期シード処理フェーズにかかる時間を見積もることができます。 たとえば、100 GB のデータベースが 1 つの場合、リンクで 1 時間あたり 84 GB をプッシュでき、他のデータベースにシードしていないのであれば、初期シード処理フェーズにかかる時間は約 1.2 時間になります。 リンクが転送できるのが 1 時間あたり 10 GB のみの場合、100 GB のデータベースのシード処理には約 10 時間かかります。 レプリケートするデータベースが複数ある場合、シード処理は並列で実行されます。そして、低速リンク速度と組み合わせると、すべてのデータベースからのデータの並列シード処理が使用可能なリンク帯域幅を超えている場合は、初期シード処理の時間が大幅に長くなることがあります。 2 つのインスタンス間のネットワーク帯域幅が制限されていて、複数のマネージド インスタンスをフェールオーバー グループに追加する場合は、複数のマネージド インスタンスを 1 つずつ順番にフェールオーバー グループに追加することを検討してください。 2 つのマネージド インスタンスの間に適切なサイズのゲートウェイ SKU があり、企業ネットワークの帯域幅で許される場合は、最大 360 GB/時の速度を実現できます。  

- **DNS ゾーン**

  新しい SQL Managed Instance の作成時に自動的に生成される一意の ID。 このインスタンスのマルチドメイン (SAN) は、同じ DNS ゾーンのインスタンスに対するクライアント接続を認証する目的でプロビジョニングされます。 同じフェールオーバー グループに属する 2 つのマネージド インスタンスでは、DNS ゾーンが共有される必要があります。
  
  > [!NOTE]
  > DNS ゾーン ID は、ドメインに対して作成されたフェールオーバー グループには必要SQL Database。

- **フェールオーバー グループの読み取り/書き込みリスナー**

  現在のプライマリをポイントする DNS CNAME レコード。 フェールオーバー グループが作成されると自動的に作成され、フェールオーバー後にプライマリが変更された場合に読み取り/書き込みワークロードがプライマリに透過的に再接続できます。 サーバーでフェールオーバー グループが作成されたときに、リスナー URL の DNS CNAME レコードの形式は `<fog-name>.database.windows.net` になります。 SQL Managed Instance でフェールオーバー グループが作成されたときに、リスナー URL の DNS CNAME レコードの形式は `<fog-name>.<zone_id>.database.windows.net` になります。

- **フェールオーバー グループの読み取り専用リスナー**

  現在のセカンダリをポイントする DNS CNAME レコード。 フェールオーバー グループが作成されると自動的に作成され、フェールオーバー後にセカンダリが変更されると、読み取り専用の SQL ワークロードがセカンダリに透過的に接続できます。 サーバーでフェールオーバー グループが作成されたときに、リスナー URL の DNS CNAME レコードの形式は `<fog-name>.secondary.database.windows.net` になります。 SQL Managed Instance でフェールオーバー グループが作成されたときに、リスナー URL の DNS CNAME レコードの形式は `<fog-name>.secondary.<zone_id>.database.windows.net` になります。

- **自動フェールオーバー ポリシー**

  既定では、フェールオーバー グループは自動フェールオーバー ポリシーを使用して構成されます。 障害が検出され、猶予期間が切れた後、システムによって geo フェールオーバーがトリガーされます。 システムは、影響の規模が原因で、組み込みの[高可用性インフラストラクチャ](high-availability-sla.md)によって停止を軽減できないことを確認する必要があります。 アプリケーションから、または手動で geo フェールオーバー ワークフローを制御する場合は、自動フェールオーバー ポリシーをオフにできます。
  
  > [!NOTE]
  > 障害の規模とその軽減にかかる時間の検証には、人間による操作が必要なため、猶予期間を 1 時間未満に設定することはできません。 この制約は、データの同期状態に関係なく、フェールオーバー グループ内のすべてのデータベースに適用されます。

- **読み取り専用フェールオーバー ポリシー**

  既定では、読み取り専用リスナーのフェールオーバーは無効になっています。 セカンダリがオフラインのとき、プライマリのパフォーマンスに影響が出ないようにします。 ただし、セカンダリが回復するまで、読み取り専用セッションは接続できません。 読み取り専用セッションのダウンタイムを許容できず、プライマリのパフォーマンスが下がる可能性があっても、読み取り専用と読み取り/書き込みの両方のトラフィックにプライマリを使用してもよければ、`AllowReadOnlyFailoverToPrimary` プロパティを構成することによって、読み取り専用リスナーのフェールオーバーを有効にできます。 その場合、セカンダリが利用できないと、読み取り専用トラフィックがプライマリに自動的にリダイレクトされます。

  > [!NOTE]
  > プロパティは、自動フェールオーバー ポリシーが有効で、自動 geo フェールオーバーがトリガーされている場合 `AllowReadOnlyFailoverToPrimary` にのみ有効です。 この場合、プロパティが True に設定されていると、新しいプライマリは読み取り/書き込みセッションと読み取り専用セッションの両方を提供します。

- **計画されたフェールオーバー**

  計画されたフェールオーバーでは、セカンダリがプライマリ ロールに切り替わる前に、プライマリ データベースとセカンダリ データベース間の完全なデータ同期が行われます。 これにより、データ損失が発生しないことが保証されます。 計画されたフェールオーバーは、次のシナリオで使用されます。

  - データ損失が許容されない場合は、運用環境でディザスター リカバリー (DR) ドリルを行います
  - データベースを別のリージョンに再配置します
  - 機能停止が軽減 (フェールバック) された後、データベースをプライマリ リージョンに返します

- **計画されていないフェールオーバー**

  計画外または強制的なフェールオーバーの場合、最近の変更がプライマリから反映されるのを待たずに、直ちにセカンダリがプライマリに切り替わります。 この操作を行うとデータが失われる可能性があります。 計画されていないフェールオーバーは、機能停止時においてプライマリにアクセスできない場合に回復手段として使用されます。 停止が緩和されると、以前のプライマリは自動的に再接続され、新しいセカンダリになります。 計画的されたフェールオーバーを実行してフェールバックし、レプリカを元のプライマリとセカンダリのロールに戻すこともできます。

- **手動フェールオーバー**

  自動フェールオーバーの構成に関係なく、いつでも手動で geo フェールオーバーを開始できます。 プライマリに影響する障害が発生したときに、自動フェールオーバー ポリシーが構成されていない場合、セカンダリをプライマリ ロールに昇格させるために手動でのフェールオーバーが必要です。 強制的 (計画外) またはフレンドリ (計画された) フェールオーバーを開始できます。 フレンドリ フェールオーバーは、以前のプライマリにアクセスできる場合にのみ可能であり、データを失うことなくプライマリをセカンダリ リージョンに再配置するために使用できます。 フェールオーバーが完了すると、確実に新しいプライマリに接続されるように、DNS レコードが自動的に更新されます。

- **データ消失の猶予期間**

  セカンダリ データベースは非同期レプリケーションを使用して同期されるので、自動 geo フェールオーバーによってデータが失われる可能性があります。 アプリケーションのデータ消失の許容範囲を反映するように、自動フェールオーバー ポリシーをカスタマイズできます。 `GracePeriodWithDataLossHours` を構成することで、データ損失につながる可能性がある強制フェールオーバーの開始までにシステムが待機する時間を制御できます。

- **複数のフェールオーバー グループ**

  同じサーバーのペアに対して複数のフェールオーバー グループを構成して、geo フェールオーバーのスコープを制御できます。 各グループは独立してフェールオーバーします。 データベースごとのテナント アプリケーションが複数のリージョンにデプロイされ、エラスティック プールを使用している場合は、この機能を使用して、各プール内のプライマリ データベースとセカンダリ データベースを混在できます。 これにより、一部のテナント データベースに対する停止の影響を軽減できる場合があります。

  > [!NOTE]
  > SQL Managed Instance では、複数のフェールオーバー グループはサポートされません。
  
## <a name="permissions"></a>アクセス許可

フェールオーバー グループに対するアクセス許可は、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) によって管理されます。 [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)ロールには、フェールオーバー グループを管理するために必要なすべてのアクセス許可があります。

### <a name="create-a-failover-group"></a><a name="create-failover-group"></a> フェールオーバー グループを作成する

フェールオーバー グループを作成するには、プライマリ サーバーとセカンダリ サーバーの両方、およびフェールオーバー グループ内のすべてのデータベースへの Azure RBAC 書き込みアクセス権が必要です。 SQL Managed Instance の場合、プライマリとセカンダリの両方の SQL Managed Instance への Azure RBAC 書き込みアクセス権が必要です。しかし、個々の SQL Managed Instance データベースをフェールオーバー グループに対して追加または削除することはできないため、個々のデータベースに対するアクセス許可は関係しません。

### <a name="update-a-failover-group"></a>フェールオーバー グループの更新

フェールオーバー グループを更新するには、そのフェールオーバー グループ、および現在のプライマリ サーバーまたはマネージド インスタンス上のすべてのデータベースへの Azure RBAC 書き込みアクセス権が必要です。  

### <a name="fail-over-a-failover-group"></a>フェールオーバー グループをフェールオーバーする

フェールオーバー グループをフェールオーバーするには、新しいプライマリ サーバーまたはマネージド インスタンス上のフェールオーバー グループへの Azure RBAC 書き込みアクセス権が必要です。

## <a name="failover-group-best-practices-for-sql-database"></a><a name="best-practices-for-sql-database"></a>フェールオーバー グループのベスト プラクティス :SQL Database

自動フェールオーバー グループはプライマリ サーバーに構成する必要があり、それを別の Azure リージョンのセカンダリ サーバーに接続します。 グループには、これらのサーバーのすべてまたは一部のデータベースを含めることができます。 次の図に、複数のデータベースと自動フェールオーバー グループを使用する、geo 冗長クラウド アプリケーションの一般的な構成を示します。

![図では、複数のデータベースと自動フェールオーバー グループを使用する、geo 冗長クラウド アプリケーションの一般的な構成が示されています。](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> フェールオーバー グループに SQL Database のデータベースを追加する詳細なステップバイステップのチュートリアルについては、[フェールオーバー グループへの SQL Database の追加](failover-group-add-single-database-tutorial.md)に関するページを参照してください。

ビジネス継続性を考慮してサービスを設計する場合は、次の一般的なガイドラインに従います。

### <a name="use-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a><a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a> 1 つ以上のフェールオーバー グループを使用して複数のデータベースのフェールオーバーを管理する

1 つまたは複数のフェールオーバー グループを異なるリージョンの 2 つのサーバー (プライマリおよびセカンダリ サーバー) 間に作成できます。 各グループには、プライマリ リージョンに発生した機能停止によりすべてまたは一部のプライマリ データベースが使用できなくなった際にユニットとして復元できる、1 つまたは複数のデータベースを含めることができます。 フェールオーバー グループを作成すると、プライマリと同じサービス目標を持つ geo セカンダリ データベースが作成されます。 既存の geo レプリケーション関係をフェールオーバー グループに追加する場合は、geo セカンダリがプライマリと同じサービス レベルとコンピューティング サイズで構成されていることを確認します。
  
### <a name="use-the-read-write-listener-to-connect-to-primary"></a><a name="using-read-write-listener-for-oltp-workload"></a> 読み取り/書き込みリスナーを使用してプライマリに接続する

読み取り/書き込みワークロードの場合は、接続文字列 `<fog-name>.database.windows.net` のサーバー名として を使用します。 接続は自動的にプライマリに向けられる。 この名前はフェールオーバー後に変更されません。 フェールオーバーでは DNS レコードの更新が行われるため、クライアントの接続は、クライアント DNS キャッシュが最新の情報に更新された後にのみ新しいプライマリにリダイレクトされます。 プライマリおよびセカンダリ リスナーの DNS レコードの有効期限 (TTL) は 30 秒です。

### <a name="use-the-read-only-listener-to-connect-to-geo-secondary"></a><a name="using-read-only-listener-for-read-only-workload"></a> 読み取り専用リスナーを使用して geo セカンダリに接続する

データ待機時間に耐える読み取り専用ワークロードを論理的に分離している場合は、geo セカンダリで実行できます。 読み取り専用セッションの場合は`<fog-name>.secondary.database.windows.net`、接続文字列のサーバー名として を使用します。 接続は自動的に geo セカンダリに向けられる。 `ApplicationIntent=ReadOnly` を使用して、接続文字列で読み取りの意図を示すこともお勧めします。

> [!NOTE]
> Premium、Business Critical、Hyperscale の各サービス レベルの SQL Database では、読み取り専用クエリのワークロードを軽減するために、[読み取り専用レプリカ](read-scale-out.md)の使用がサポートされています。この場合、接続文字列に `ApplicationIntent=ReadOnly` パラメーターが使用されます。 geo セカンダリを構成した場合は、この機能を使用して、プライマリの場所または geo レプリケートされた場所の読み取り専用レプリカに接続できます。
>
> - プライマリ ロケーションの読み取り専用レプリカに接続するには、`ApplicationIntent=ReadOnly` と `<fog-name>.database.windows.net` を使用します。
> - セカンダリ ロケーションの読み取り専用レプリカに接続するには、`ApplicationIntent=ReadOnly` と `<fog-name>.secondary.database.windows.net` を使用します。

### <a name="potential-performance-degradation-after-geo-failover"></a><a name="preparing-for-performance-degradation"></a> geo フェールオーバー後のパフォーマンス低下の可能性

一般的な Azure アプリケーションでは、複数の Azure サービスを使用し、複数のコンポーネントで構成されます。 フェールオーバー グループの自動 geo フェールオーバーは、Azure SQL コンポーネントの状態に基づいてトリガーされます。 プライマリ リージョンのその他の Azure サービスは機能停止の影響を受けない場合があり、それらのコンポーネントを引き続きそのリージョンで利用できる可能性があります。 プライマリ データベースがセカンダリ リージョンに切り替えられると、依存コンポーネント間の待機時間が長くなる場合があります。 アプリケーションのパフォーマンスに対する待機時間の長い影響を回避するには、DR リージョン内のすべてのアプリケーションコンポーネントの冗長性を確保し、次の[ネットワーク セキュリティガイドライン](#failover-groups-and-network-security)に従い、関連するアプリケーション コンポーネントの geo フェールオーバーをデータベースと共に調整します。

### <a name="potential-data-loss-after-geo-failover"></a><a name="preparing-for-data-loss"></a> geo フェールオーバー後のデータ損失の可能性

プライマリ リージョンで障害が発生した場合、最近のトランザクションを geo セカンダリにレプリケートできない可能性があります。 自動フェールオーバー ポリシーが構成されている場合、自動 geo フェールオーバーを開始する前に、 で指定した期間システム `GracePeriodWithDataLossHours` が待機します。 既定値は 1 時間です。 これにより、データが失われるのを超えるデータベースの可用性が向上します。 24 時間など、より大きな数に設定したり、自動 geo フェールオーバーを無効にしたりすると、データベースの可用性を犠牲にしてデータ損失の可能性 `GracePeriodWithDataLossHours` を減らできます。

> [!IMPORTANT]
> DTC が 800 以下、仮想コア数が 8 以下、データベース数が 250 を超えるエラスティック プールでは、計画された geo フェールオーバーの時間の長さやパフォーマンスの低下などの問題が発生する可能性があります。 これらの問題は、書き込み集中型のワークロード、geo レプリカが地理的に広く分離されている場合、またはデータベースごとに複数のセカンダリ geo レプリカが使用されている場合に発生する可能性が高い。 これらの問題の症状は、時間の過ぎた geo レプリケーションのラグが増加し、停止中のデータ損失が拡大する可能性があります。 このラグは、[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) を使用して監視できます。 これらの問題が発生した場合の軽減策には、DTC または仮想コアを増やしたり、プール内の geo レプリケートされたデータベースの数を減らしたりするために、プールをスケールアップする作業が含まれます。

### <a name="change-the-secondary-region-of-a-failover-group"></a><a name="changing-secondary-region-of-the-failover-group"></a> フェールオーバー グループのセカンダリ リージョンを変更する

変更のシーケンスを説明するために、サーバー A はプライマリ サーバー、サーバー B は既存のセカンダリ サーバー、サーバー C は 3 番目のリージョンの新しいセカンダリであると仮定します。 移行を行うには、次の手順を実行します。

1. サーバー A の各データベースの追加のセカンダリを、[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用してサーバー C に作成します。 サーバー A の各データベースには、2 つのセカンダリがあり、1 つはサーバー B に、もう 1 つはサーバー C にあります。これにより、移行中、プライマリ データベースが確実に保護された状態を維持できます。
2. フェールオーバー グループを削除します。 この時点で、フェールオーバー グループ エンドポイントを使用したログイン試行は失敗します。
3. サーバー A と C の間に同じ名前でフェールオーバー グループを作成し、もう一度作成します。
4. サーバー A のすべてのプライマリ データベースを新しいフェールオーバー グループに追加します。 この時点で、ログイン試行は失敗しないます。
5. サーバー B を削除します。B のすべてのデータベースは自動的に削除されます。

### <a name="change-the-primary-region-of-a-failover-group"></a><a name="changing-primary-region-of-the-failover-group"></a> フェールオーバー グループのプライマリ リージョンを変更する

変更のシーケンスを説明するために、サーバー A はプライマリ サーバー、サーバー B は既存のセカンダリ サーバー、サーバー C は 3 番目のリージョンの新しいプライマリであると仮定します。 移行を行うには、次の手順を実行します。

1. 計画的 geo フェールオーバーを実行してプライマリ サーバーを B に切り替えます。サーバー A は新しいセカンダリ サーバーになります。 フェールオーバーによって、数分間のダウンタイムが発生する場合があります。 実際の時間は、フェールオーバー グループのサイズによって異なります。
2. サーバー B の各データベースの追加のセカンダリを、[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用してサーバー C に作成します。 サーバー B の各データベースには、2 つのセカンダリがあり、1 つはサーバー A に、もう 1 つはサーバー C にあります。これにより、移行中、プライマリ データベースが確実に保護された状態を維持できます。
3. フェールオーバー グループを削除します。 この時点で、フェールオーバー グループ エンドポイントを使用したログイン試行は失敗します。
4. サーバー B と C の間で同じ名前でフェールオーバー グループを作成し、もう一度作成します。
5. B のすべてのプライマリ データベースを新しいフェールオーバー グループに追加します。 この時点で、ログイン試行は失敗しないます。
6. フェールオーバー グループの計画的 geo フェールオーバーを実行して、B と C を切り替えます。これで、サーバー C がプライマリになり、B がセカンダリになります。 サーバー A 上のすべてのセカンダリ データベースは、自動的に C のプライマリにリンクされます。手順 1 と同様に、フェールオーバーによって数分間のダウンタイムが発生する場合があります。
7. サーバー A を削除します。A のすべてのデータベースは自動的に削除されます。

> [!IMPORTANT]
> フェールオーバー グループを削除すると、リスナー エンドポイントの DNS レコードも削除されます。 その時点で、他のユーザーがフェールオーバー グループまたは同じ名前のサーバー DNS エイリアスを作成する確率は 0 以外です。 フェールオーバー グループ名と DNS エイリアスはグローバルに一意である必要があります。これにより、同じ名前を再度使用する必要はありません。 このリスクを最小限に抑えるには、汎用フェールオーバーグループ名を使用しないでください。

## <a name="failover-group-best-practices-for-sql-managed-instance"></a><a name="best-practices-for-sql-managed-instance"></a>フェールオーバー グループの SQL Managed Instanceベスト プラクティス

自動フェールオーバー グループはプライマリ インスタンスに構成する必要があり、それを別の Azure リージョンのセカンダリ インスタンスに接続します。  インスタンス内のすべてのユーザー データベースは、セカンダリ インスタンスにレプリケートされます。 _master_ や _msdb_ などのシステム データベースはレプリケートされません。

次の図に、マネージド インスタンスと自動フェールオーバー グループを使用する、geo 冗長クラウド アプリケーションの一般的な構成を示します。

![自動フェールオーバーの図](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> フェールオーバー グループを使用するための SQL Managed Instance の追加に関する詳細なステップバイステップのチュートリアルについては、[フェールオーバー グループへのマネージド インスタンスの追加](../managed-instance/failover-group-add-instance-tutorial.md)に関するページを参照してください。

アプリケーションでデータ層として SQL Managed Instance を使用する場合は、ビジネス継続性を考慮して設計する際にこれらの一般的なガイドラインに従います。

### <a name="create-the-geo-secondary-managed-instance"></a><a name="creating-the-secondary-instance"></a> geo セカンダリ マネージド インスタンスを作成する

フェールオーバー後にプライマリ サーバーへの中断SQL Managed Instance接続を確保するには、プライマリ インスタンスとセカンダリ インスタンスの両方が同じ DNS ゾーンにある必要があります。 同じマルチドメイン (SAN) 証明書を使用して、フェールオーバー グループ内の 2 つのインスタンスのいずれかへのクライアント接続を認証できます。 アプリケーションを運用環境にデプロイする準備ができたら、別のリージョンでセカンダリ SQL Managed Instance を作成し、プライマリ SQL Managed Instance と DNS ゾーンを共有していることを確認します。 これは、作成時に省略可能なパラメーターを指定することで実行できます。 PowerShell またはコマンドレットを使用している場合REST API、省略可能なパラメーターの名前は です`DNSZonePartner`。 テーブル内の対応する省略可能なフィールドのAzure portalは *、Primary Managed Instance です*。

> [!IMPORTANT]
> サブネットに作成された最初のマネージド インスタンスにより、同じサブネット内のそれ以降のすべてのインスタンスに対する DNS ゾーンが決まります。 つまり、同じサブネットの 2 つのインスタンスが異なる DNS ゾーンに属することはできません。

プライマリ インスタンスと同じ DNS ゾーンでのセカンダリ SQL Managed Instance の作成の詳細については、「[セカンダリ マネージド インスタンスを作成する](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)」を参照してください。

### <a name="use-paired-regions"></a><a name="using-geo-paired-regions"></a> ペアリージョンを使用する

パフォーマンス上の理由により、両方のマネージド インスタンスを、[ペアになっているリージョン](../../best-practices-availability-paired-regions.md)にデプロイします。 SQLManaged Instanceリージョン内のフェールオーバー グループのパフォーマンスは、ペアのリージョンと比較して優れたものになります。

### <a name="enable-geo-replication-traffic-between-two-managed-instances"></a><a name="enabling-replication-traffic-between-two-instances"></a> 2 つのマネージド インスタンス間の geo レプリケーション トラフィックを有効にする

各マネージド インスタンスは独自の VNet に分離されているので、これらの VNet 間の 2 方向トラフィックを許可する必要があります。 [Azure VPN ゲートウェイ](../../vpn-gateway/vpn-gateway-about-vpngateways.md)に関するページを参照してください

### <a name="create-a-failover-group-between-managed-instances-in-different-subscriptions"></a><a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a> 異なるサブスクリプションのマネージド インスタンス間にフェールオーバー グループを作成する

サブスクリプションが同じ [Azure Active Directory テナント](../../active-directory/fundamentals/active-directory-whatis.md#terminology)に関連付けられている限り、2 つの異なるサブスクリプションの SQL Managed Instances 間でフェールオーバー グループを作成することができます。 PowerShell API を使用する場合は、セカンダリ SQL Managed Instance の `PartnerSubscriptionId` パラメーターを指定することでこれを実行できます。 パラメーターをREST API、パラメーターに含まれる各インスタンス ID `properties.managedInstancePairs` に独自のサブスクリプション ID を設定できます。
  
> [!IMPORTANT]
> Azure Portal では、異なるサブスクリプション間でのフェールオーバー グループの作成はサポートされません。 また、異なるサブスクリプションやリソース グループにまたがる既存のフェールオーバー グループについては、プライマリ SQL Managed Instance からポータルを使用して手動でフェールオーバーを開始することはできません。 代わりに、geo セカンダリ インスタンスから開始してください。

### <a name="manage-geo-failover-to-a-geo-secondary-instance"></a><a name="managing-failover-to-secondary-instance"></a> geo セカンダリ インスタンスへの geo フェールオーバーを管理する

フェールオーバー グループは、プライマリ マネージド インスタンス上のすべてのデータベースの geo フェールオーバーを管理します。 グループが作成されると、インスタンス内の各データベースが geo セカンダリ インスタンスに自動的に geo レプリケートされます。 フェールオーバー グループを使用して、データベースのサブセットの部分的なフェールオーバーを開始することはできません。

> [!IMPORTANT]
> プライマリ マネージド インスタンスでデータベースが削除された場合は、geo セカンダリ マネージド インスタンスでも自動的に削除されます。

### <a name="use-the-read-write-listener-to-connect-to-the-primary-managed-instance"></a><a name="using-read-write-listener-for-oltp-workload"></a> 読み取り/書き込みリスナーを使用してプライマリ マネージド インスタンスに接続する

読み取り/書き込みワークロードの場合は、 `<fog-name>.zone_id.database.windows.net` サーバー名として を使用します。 接続は自動的にプライマリに向けられる。 この名前はフェールオーバー後に変更されません。 geo フェールオーバーには DNS レコードの更新が含まれるので、クライアント接続は、クライアント DNS キャッシュが更新された後にのみ新しいプライマリにリダイレクトされます。 セカンダリ インスタンスは DNS ゾーンをプライマリと共有するために、クライアント アプリケーションは同じサーバー側 SAN 証明書を使用してそのゾーンに再接続できます。

### <a name="use-the-read-only-listener-to-connect-to-the-geo-secondary-managed-instance"></a><a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a> 読み取り専用リスナーを使用して geo セカンダリ マネージド インスタンスに接続する

データ待機時間に耐える読み取り専用ワークロードを論理的に分離している場合は、geo セカンダリで実行できます。 geo セカンダリに直接接続するには、サーバー名として `<fog-name>.secondary.<zone_id>.database.windows.net` を使用します。

> [!NOTE]
> Business Critical レベルでは、SQL Managed Instance では、接続文字列の`ApplicationIntent=ReadOnly` パラメーターを使用して、[読み取り専用レプリカ](read-scale-out.md)を使用して読み取り専用クエリ ワークロードをオフロードできます。 geo レプリケートされたセカンダリを構成した場合は、この機能を使用して、プライマリ ロケーション、または geo レプリケートされたロケーションの読み取り専用レプリカに接続できます。
>
> - プライマリ ロケーションの読み取り専用レプリカに接続するには、`ApplicationIntent=ReadOnly` と `<fog-name>.<zone_id>.database.windows.net` を使用します。
> - セカンダリ ロケーションの読み取り専用レプリカに接続するには、`ApplicationIntent=ReadOnly` と `<fog-name>.secondary.<zone_id>.database.windows.net` を使用します。

### <a name="potential-performance-degradation-after-failover-to-the-geo-secondary-managed-instance"></a>geo セカンダリ マネージド インスタンスへのフェールオーバー後にパフォーマンスが低下する可能性があります

一般的な Azure アプリケーションでは、複数の Azure サービスを使用し、複数のコンポーネントで構成されます。 フェールオーバー グループの自動 geo フェールオーバーは、Azure SQL コンポーネントの状態に基づいてトリガーされます。 プライマリ リージョンのその他の Azure サービスは機能停止の影響を受けない場合があり、それらのコンポーネントを引き続きそのリージョンで利用できる可能性があります。 プライマリ データベースがセカンダリ リージョンに切り替えられると、依存コンポーネント間の待機時間が長くなる場合があります。 待機時間が長くなることがアプリケーションのパフォーマンスに与える影響を回避するには、セカンダリ リージョンのすべてのアプリケーション コンポーネントに冗長性を確保し、アプリケーション コンポーネントをデータベースと共にフェールオーバーします。 構成時に、[ネットワーク セキュリティのガイドライン](#failover-groups-and-network-security)に従って、セカンダリ リージョンでデータベースへの接続があることを確認します。

### <a name="potential-data-loss-after-failover-to-the-geo-secondary-managed-instance"></a>geo セカンダリ マネージド インスタンスへのフェールオーバー後のデータ損失の可能性

プライマリ リージョンで障害が発生した場合、最近のトランザクションを geo セカンダリにレプリケートできない可能性があります。 自動フェールオーバー ポリシーが構成されている場合は、データ損失がゼロの場合に geo フェールオーバーがトリガーされます。 そうでないと、`GracePeriodWithDataLossHours` を使用して指定した期間のフェールオーバーは延期されます。 自動フェールオーバー ポリシーを構成した場合は、データ損失に備えて準備してください。 一般に、機能の停止中、Azure では可用性が重視されます。 24 時間など、より大きな数に設定したり、自動 geo フェールオーバーを無効にしたりすると、データベースの可用性を犠牲にしてデータ損失の可能性 `GracePeriodWithDataLossHours` を減らできます。

読み取り/書き込みリスナーの DNS の更新は、フェールオーバーが開始された後すぐに行われます。 この操作でデータが失われることはありません。 しかし、データベース ロールの切り替えプロセスには、通常の状況で最大 5 分かかる場合があります。 これが完了するまで、新しいプライマリ インスタンスの一部のデータベースは引き続き読み取り専用となります。 PowerShell を使用してフェールオーバーが開始された場合、プライマリ レプリカ ロールを切り替える操作は同時に発生します。 Azure ポータルを使用して開始された場合、UI で完了状態が示されます。 REST API を使用して開始された場合は、標準的な Azure リソース マネージャーのポーリング メカニズムを使用して、完了を監視します。

> [!IMPORTANT]
> geo フェールオーバーの原因となる停止が軽減された後は、手動計画フェールオーバーを使用してプライマリを元の場所に戻します。
  
### <a name="change-the-secondary-region-of-the-managed-instance-failover-group"></a>マネージド インスタンスフェールオーバー グループのセカンダリ リージョンを変更する

インスタンス A がプライマリ インスタンス、インスタンス B が既存のセカンダリ インスタンス、インスタンス C が 3 番目のリージョン内の新しいセカンダリ インスタンスであるとします。 移行を行うには、次の手順を実行します。

1. 同じ DNS ゾーン内で、A と同じサイズのインスタンス C を作成します。
2. インスタンス A と B 間のフェールオーバー グループを削除します。この時点ではログインは失敗します。フェールオーバー グループ リスナーの SQL エイリアスが削除されていて、ゲートウェイがフェールオーバー グループ名を認識しないためです。 セカンダリ データベースはプライマリから切断され、読み取り/書き込みデータベースになります。
3. インスタンス A と C の間に同じ名前のフェールオーバー グループを作成します。[SQL Managed Instance を含むフェールオーバー グループのチュートリアル](../managed-instance/failover-group-add-instance-tutorial.md)に関するページの手順に従ってください。 これはデータ サイズに左右される操作であり、インスタンス A のすべてのデータベースがシード処理され、同期されると完了します。
4. 不要な料金が発生しないように、インスタンス B が不要であれば削除してください。

> [!NOTE]
> 手順 2 の後、手順 3 が完了するまでは、インスタンス A のデータベースは、インスタンス A の致命的な障害から保護されないままになります。

### <a name="change-the-primary-region-of-the-managed-instance-failover-group"></a>マネージインスタンスのフェールオーバーグループのプライマリリージョンを変更する

インスタンス A がプライマリ インスタンス、インスタンス B が既存のセカンダリ インスタンス、インスタンス C が 3 番目のリージョン内の新しいプライマリ インスタンスであるとします。 移行を行うには、次の手順を実行します。

1. 同じ DNS ゾーン内で、B と同じサイズのインスタンス C を作成します。
2. インスタンス B に接続し、手動でフェールオーバーしてプライマリ インスタンスを B に切り替えます。インスタンス A が自動的に新しいセカンダリ インスタンスになります。
3. インスタンス A とインスタンス B 間のフェールオーバーグループを削除します。この時点で、フェールオーバーグループエンドポイントを使用したログイン試行は失敗します。 のセカンダリデータベースはプライマリから切断され、読み取り/書き込みデータベースになります。
4. インスタンス A と C の間に同じ名前のフェールオーバー グループを作成します。[マネージド インスタンスを含むフェールオーバー グループのチュートリアル](../managed-instance/failover-group-add-instance-tutorial.md)に関する記事の手順に従ってください。 これはデータ サイズに左右される操作であり、インスタンス A のすべてのデータベースがシード処理され、同期されると完了します。 この時点で、ログイン試行は失敗しなくなります。
5. 不要な料金が発生しないように、インスタンス A が不要であれば削除してください。

> [!CAUTION]
> 手順 3 の後、手順 4 が完了するまでは、インスタンス A のデータベースは、インスタンス A の致命的な障害から保護されないままになります。

> [!IMPORTANT]
> フェールオーバー グループを削除すると、リスナー エンドポイントの DNS レコードも削除されます。 その時点で、他のユーザーが同じ名前のフェールオーバーグループを作成する確率は0以外になります。 フェールオーバーグループ名はグローバルに一意である必要があるため、同じ名前を再度使用することはできません。 このリスクを最小限に抑えるには、汎用フェールオーバーグループ名を使用しないでください。

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>システム データベースのオブジェクトに依存するシナリオを実現させる

システム データベースは、フェールオーバー グループのセカンダリ インスタンスにはレプリケート **されません**。 システム データベースのオブジェクトに依存するシナリオを実現するには、セカンダリ インスタンスに同じオブジェクトを作成し、プライマリ インスタンスとの同期を維持する必要があります。 

たとえば、セカンダリ インスタンスで同じログインを使用する予定の場合は、必ず、同じ SID でそれらを作成してください。 

```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 
### <a name="synchronize-instance-properties-and-retention-policies-between-primary-and-secondary-instance"></a>プライマリ インスタンスとセカンダリ インスタンスの間でインスタンスのプロパティと保持ポリシーを同期する

フェールオーバーグループ内のインスタンスは個別の Azure リソースを保持します。プライマリインスタンスの構成に対して行われた変更は、セカンダリインスタンスに自動的にレプリケートされません。 プライマリ インスタンス _と_ セカンダリ インスタンスの両方で、関連するすべての変更を実行する必要があります。 たとえば、プライマリ インスタンスでバックアップ ストレージの冗長性または長期的なバックアップ保持ポリシーを変更した場合は、セカンダリ インスタンスでも必ず変更してください。

## <a name="failover-groups-and-network-security"></a>フェールオーバー グループとネットワーク セキュリティ

一部のアプリケーションでは、セキュリティ規則によって、データ層へのネットワークアクセスが、VM や web サービスなどの特定のコンポーネントまたはコンポーネントに制限されている必要があります。この要件により、ビジネス継続性の設計とフェールオーバーグループの使用に関するいくつかの課題が生じます。 このような制限付きアクセスを実装する場合は、次のオプションを検討してください。

### <a name="use-failover-groups-and-virtual-network-service-endpoints"></a><a name="using-failover-groups-and-virtual-network-rules"></a> フェールオーバーグループと仮想ネットワークサービスエンドポイントの使用

[仮想ネットワーク サービス エンドポイントおよび規則](vnet-service-endpoint-rule-overview.md)を使用して、SQL Database または SQL Managed Instance のデータベースへのアクセスを制限する場合、各仮想ネットワーク サービス エンドポイントは 1 つの Azure リージョンにのみ適用されることに注意してください。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。 そのため、同じリージョンにデプロイされているクライアント アプリケーションのみが、プライマリ データベースに接続できます。 geo フェールオーバーによって、SQL Database のクライアントセッションが別の (セカンダリ) リージョンのサーバーに再ルーティングされるため、このようなセッションは、そのリージョンの外部のクライアントから発生した場合に失敗します。 そのため、参加するサーバーまたはインスタンスが仮想ネットワーク規則に含まれている場合、自動フェールオーバー ポリシーを有効にすることはできません。 手動フェールオーバーをサポートするには、次の手順を行います。

1. アプリケーションのフロントエンドコンポーネント (web サービス、仮想マシンなど) の冗長コピーをセカンダリリージョンにプロビジョニングします。
2. プライマリサーバーとセカンダリサーバーに対して、 [仮想ネットワークルール](vnet-service-endpoint-rule-overview.md) を個別に構成します。
3. [Traffic manager の構成を使用してフロントエンドフェールオーバー](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)を有効にします。
4. 障害が検出されたときに、手動 geo フェールオーバーを開始します。 このオプションは、フロント エンドとデータ層の間で一貫した待機時間を必要とするアプリケーションに対して最適化されており、フロント エンド、データ層、またはこの両方が機能停止の影響を受ける場合に回復をサポートします。

> [!NOTE]
> **読み取り専用リスナー** を使用して読み取り専用ワークロードの負荷を分散する場合は、このワークロードを必ずセカンダリ リージョン内の VM などのリソースで実行することで、セカンダリ データベースに接続できるようにします。

### <a name="use-failover-groups-and-firewall-rules"></a>フェールオーバー グループおよびファイアウォール規則を使用する

ビジネス継続性計画で自動フェールオーバーを含むグループを使用したフェールオーバーが必要な場合は、パブリック IP ファイアウォール規則を使用して SQL Database でデータベースへのアクセスを制限できます。 自動フェールオーバーをサポートするには、次の手順を行います。

1. [パブリック IP を作成します](../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address)。
2. [パブリック ロード バランサーを作成](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)し、パブリック IP を割り当てます。
3. フロントエンドコンポーネント用の[仮想ネットワークと仮想マシンを作成](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)します。
4. [ネットワーク セキュリティ グループを作成](../../virtual-network/network-security-groups-overview.md)し、受信接続を構成します。
5. `Sql.<Region>`[サービスタグ](../../virtual-network/network-security-groups-overview.md#service-tags)を使用して、発信接続がリージョン内の Azure SQL Database に開かれていることを確認します。
6. [SQL Database ファイアウォール規則](firewall-configure.md)を作成して、手順 1 で作成したパブリック IP アドレスからの受信トラフィックを許可します。

送信アクセスを構成する方法と、ファイアウォール規則で使用する IP の詳細については、[ロード バランサー送信接続](../../load-balancer/load-balancer-outbound-connections.md)に関するページを参照してください。

上記の構成では、自動 geo フェールオーバーによってフロントエンドコンポーネントからの接続がブロックされることはなく、フロントエンドとデータ層の間の待機時間が長くなることを前提としています。

> [!IMPORTANT]
> 地域的な障害発生時のビジネス継続性を保証するには、フロントエンドコンポーネントとデータベースの両方で地理的な冗長性を確保する必要があります。

## <a name="enabling-geo-replication-between-managed-instance-virtual-networks"></a><a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a> マネージインスタンス仮想ネットワーク間で geo レプリケーションを有効にする

2 つの異なるリージョンのプライマリとセカンダリの SQL Managed Instance の間にフェールオーバー グループを設定すると、各インスタンスは独立した仮想ネットワークを使用して分離されます。 これらの VNet 間のレプリケーション トラフィックを許可するには、以下の前提条件が満たされていることを確認します。

- SQL Managed Instance の 2 つのインスタンスは、異なる Azure リージョンにある必要があります。
- SQL Managed Instance の 2 つのインスタンスは同じサービス レベルである必要があり、ストレージ サイズも同じである必要があります。
- SQL Managed Instance のセカンダリ インスタンスは空 (ユーザー データベースなし) である必要があります。
- SQL Managed Instance のインスタンスによって使用される仮想ネットワークは、[VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) または [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 経由で接続されている必要があります。 2 つの仮想ネットワークがオンプレミスのネットワーク経由で接続されている場合、ポート 5022 および 11000-11999 をブロックするファイアウォール規則がないことを確認します。 グローバル VNet ピアリングはサポート対象ですが、次の注記で説明する制限事項があります。

   > [!IMPORTANT]
   > [2020 年 9 月 22 日に、新しく作成された仮想クラスターに対するグローバル仮想ネットワーク ピアリングのサポートが発表されました](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 つまり、この発表日の後に空のサブネット内に作成された SQL Managed Instance のほか、これらのサブネット内に作成されたそれ以降のすべてのマネージド インスタンスでグローバル仮想ネットワーク ピアリングがサポートされます。 その他のすべての SQL Managed Instance では、[グローバル仮想ネットワーク ピアリングの制約](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)のために、ピアリングのサポートが同じリージョン内のネットワークに制限されます。 詳細については、[Azure Virtual Networks のよく寄せられる質問](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に関する記事の関連セクションも参照してください。 この発表日の前に作成された仮想クラスターの SQL Managed Instance でグローバル仮想ネットワーク ピアリングを使用できるようにするには、そのインスタンス上で既定以外の[メンテナンス期間](./maintenance-window.md)を構成することを検討してください。それにより、そのインスタンスが、グローバル仮想ネットワーク ピアリングをサポートする新しい仮想クラスターに移動されます。

- 2 つの SQL Managed Instance VNet に、重複する IP アドレスを含めることはできません。
- 他のマネージド インスタンスのサブネットからの接続では、インバウンドおよびアウトバウンドでポート 5022 およびその範囲の 11000 から 12000 を開くように、ネットワーク セキュリティ グループ (NSG) を設定する必要があります。 これで、インスタンス間のレプリケーション トラフィックを許可します。

   > [!IMPORTANT]
   > NSG セキュリティ規則が正しく構成されていないと、データベースのシード処理操作がスタックします。

- セカンダリ SQL Managed Instance は正しい DNS ゾーン ID で構成されます。 DNS ゾーンは、SQL Managed Instance と基になる仮想クラスターのプロパティであり、ホスト名のアドレスにその ID が含まれます。 ゾーン ID は、各 VNet で最初の SQL Managed Instance が作成されたときに、ランダムな文字列として生成され、同じ ID が同じサブネットの他のすべてのインスタンスに割り当てられます。 一度割り当てられると、DNS ゾーンは変更できません。 同じフェールオーバー グループに含まれる SQL Managed Instance で、DNS ゾーンを共有する必要があります。 これは、セカンダリ インスタンスの作成時、プライマリ インスタンスのゾーン ID を DnsZonePartner パラメーターの値として渡すことで実行します。

   > [!NOTE]
   > SQL Managed Instance を使用したフェールオーバー グループの構成の詳細なチュートリアルについては、[フェールオーバー グループへの SQL Managed Instance の追加](../managed-instance/failover-group-add-instance-tutorial.md)に関するページを参照してください。

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> プライマリデータベースのスケーリング

Geo セカンダリを切断せずに、プライマリデータベースを (同じサービスレベル内の) 別のコンピューティングサイズにスケールアップまたはスケールダウンすることができます。 スケールアップするときは、まず geo セカンダリをスケールアップしてから、プライマリをスケールアップすることをお勧めします。 スケールダウンする場合は、順序を逆にします。最初にプライマリをスケールダウンし、次にセカンダリをスケールダウンします。 データベースを別のサービス階層にスケーリングする場合は、この推奨設定が適用されます。

このシーケンスは、より低い SKU の geo セカンダリが過負荷になり、アップグレードまたはダウングレードプロセス中に再シードする必要がある問題を回避するために特に推奨されています。 プライマリを読み取り専用にすることでこの問題を回避することもできます。その場合、プライマリへのすべての読み取り/書き込みワークロードに影響が出ることになります。

> [!NOTE]
> フェールオーバーグループの構成の一部として geo セカンダリを作成した場合は、geo セカンダリをスケールダウンすることは推奨されません。 これは、geo フェールオーバー後の通常のワークロードを処理するのに十分な容量がデータ層にあることを確認するためのものです。

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> 重要なデータが失われないようにする

ワイドエリアネットワークの待機時間が長いため、geo レプリケーションは非同期レプリケーションメカニズムを使用します。 非同期レプリケーションを使用すると、プライマリに障害が発生した場合にデータ損失が回避される可能性があります。 重要なトランザクションをデータ損失から保護するために、アプリケーション開発者はトランザクションをコミットした直後に [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) ストアドプロシージャを呼び出すことができます。 を呼び出す `sp_wait_for_database_copy_sync` と、最後にコミットされたトランザクションが転送され、セカンダリデータベースのトランザクションログに書き込まれるまで、呼び出し元のスレッドがブロックされます。 ただし、転送されたトランザクションがセカンダリで再生 (再実行) されるのを待機することはありません。 `sp_wait_for_database_copy_sync` は、特定の geo レプリケーションリンクにスコープが設定されています。 プライマリ データベースへの接続権限を持つユーザーが、このプロシージャを呼び出すことができます。

> [!NOTE]
> `sp_wait_for_database_copy_sync` 特定のトランザクションの geo フェールオーバー後のデータ損失を防ぎますが、読み取りアクセスの完全同期は保証しません。 プロシージャ呼び出しによって発生する遅延は `sp_wait_for_database_copy_sync` 大きくなる可能性があり、呼び出し時のプライマリでまだ転送されていないトランザクションログのサイズによって異なります。

## <a name="failover-groups-and-point-in-time-restore"></a>フェールオーバー グループとポイントインタイム リストア

フェールオーバー グループでのポイントインタイム リストアの使用については、[特定の時点への復元 (PITR)](recovery-using-backups.md#point-in-time-restore) に関するページを参照してください。

## <a name="limitations-of-failover-groups"></a>フェールオーバー グループの制限

次の制限事項に注意してください。

- 同じ Azure リージョン内の 2 つのサーバーまたはインスタンス間で、フェールオーバー グループを作成することはできません。
- フェールオーバー グループの名前を変更することはできません。 グループを削除し、別の名前で再作成する必要があります。
- フェールオーバー グループ内のデータベースでは、データベース名の変更はサポートされていません。 データベースの名前を変更したり、フェールオーバー グループからデータベースを削除したりするには、フェールオーバー グループを一時的に削除する必要があります。
- システム データベースは、フェールオーバー グループのセカンダリ インスタンスにはレプリケートされません。 したがって、システム データベースのオブジェクトに依存するシナリオでは、オブジェクトをセカンダリ インスタンスで手動で作成する必要があります。また、プライマリ インスタンスで行われた変更があれば、手動で同期を保つ必要があります。 唯一の例外は SQL Managed Instance のサービス マスター キー (SMK) で、これはフェールオーバー グループの作成時にセカンダリ インスタンスに自動的にレプリケートされます。 ただし、プライマリ インスタンスでの SMK のその後の変更は、セカンダリ インスタンスにレプリケートされません。
- インスタンスがインスタンスプールに存在する場合、インスタンス間にフェールオーバーグループを作成することはできません。

## <a name="programmatically-manage-failover-groups"></a><a name="programmatically-managing-failover-groups"></a> フェールオーバーグループをプログラムで管理する

前に説明したように、自動フェールオーバー グループは、Azure PowerShell、Azure CLI、および REST API を使用して管理することもできます。 次の表では、使用できるコマンド セットについて説明します。 アクティブ geo レプリケーションには、管理のための Azure Resource Manager API 一式 ([Azure SQL Database REST API](/rest/api/sql/)、[Azure PowerShell コマンドレット](/powershell/azure/)など) が含まれています。 これらの API では、リソース グループを使用する必要があり、Azure のロール ベースのアクセス制御 (Azure RBAC) がサポートされます。 アクセス ロールの実装方法の詳細については、[Azure のロール ベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) に関するページをご覧ください。

### <a name="manage-sql-database-geo-failover"></a><a name="manage-sql-database-failover"></a>SQL Database geo フェールオーバーの管理

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| コマンドレット | 説明 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |このコマンドはフェールオーバー グループを作成し、それをプライマリとセカンダリの両方のサーバーに登録します。|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | フェールオーバー グループをサーバーから削除します。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | フェールオーバー グループの構成を取得します。 |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |フェールオーバー グループの構成を変更します。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | セカンダリ サーバーへのフェールオーバー グループのフェールオーバーをトリガーします |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|1 つまたは複数のデータベースをフェールオーバー グループに追加します。|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| コマンド | 説明 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |このコマンドはフェールオーバー グループを作成し、それをプライマリとセカンダリの両方のサーバーに登録します。|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | フェールオーバー グループをサーバーから削除します。 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | フェールオーバー グループの構成を取得します。 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |フェールオーバー グループの構成の変更や、フェールオーバー グループへの 1 つ以上のデータベースの追加を行います。|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | セカンダリ サーバーへのフェールオーバー グループのフェールオーバーをトリガーします |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 説明 |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/failovergroups/createorupdate) | フェールオーバー グループを作成または更新します |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | フェールオーバー グループをサーバーから削除します。 |
| [Failover (計画されたフェールオーバー)](/rest/api/sql/failovergroups/failover) | 完全なデータ同期を行って、現在のプライマリ サーバーからセカンダリ サーバーへのフェールオーバーをトリガーします。|
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | データを同期せずに、現在のプライマリ サーバーからセカンダリ サーバーへのフェールオーバーをトリガーします。 この操作を行うとデータが失われる可能性があります。 |
| [フェールオーバー グループの取得](/rest/api/sql/failovergroups/get) | フェールオーバー グループの構成を取得します。 |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | サーバーのフェールオーバー グループの一覧を表示します。 |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | フェールオーバー グループの構成を更新します。 |

---

### <a name="manage-sql-managed-instance-geo-failover"></a><a name="manage-sql-managed-instance-failover"></a>geo フェールオーバー Managed Instance SQL を管理する

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| コマンドレット | 説明 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |このコマンドはフェールオーバー グループを作成し、それをプライマリとセカンダリの両方のインスタンスに登録します。|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |フェールオーバー グループの構成を変更します。|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |フェールオーバー グループの構成を取得します。|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |フェールオーバー グループのセカンダリ インスタンスに対するフェールオーバーをトリガーします。|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | フェールオーバー グループを削除します|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| コマンド | 説明 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |このコマンドはフェールオーバー グループを作成し、それをプライマリとセカンダリの両方のサーバーに登録します。|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | フェールオーバー グループをサーバーから削除します。 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | フェールオーバー グループの構成を取得します。 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |フェールオーバー グループの構成の変更や、フェールオーバー グループへの 1 つ以上のデータベースの追加を行います。|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | セカンダリ サーバーへのフェールオーバー グループのフェールオーバーをトリガーします |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 説明 |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/instancefailovergroups/createorupdate) | フェールオーバー グループの構成を作成または更新します。 |
| [Delete Failover Group](/rest/api/sql/instancefailovergroups/delete) | フェールオーバー グループをインスタンスから削除します。 |
| [Failover (計画されたフェールオーバー)](/rest/api/sql/instancefailovergroups/failover) | 完全なデータ同期を行って、現在のプライマリ インスタンスからこのインスタンスへのフェールオーバーをトリガーします。 |
| [Force Failover Allow Data Loss](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | データを同期せずに、現在のプライマリ インスタンスからセカンダリ インスタンスへのフェールオーバーをトリガーします。 この操作を行うとデータが失われる可能性があります。 |
| [フェールオーバー グループの取得](/rest/api/sql/instancefailovergroups/get) | フェールオーバー グループの構成を取得します。 |
| [List Failover Groups - List By Location](/rest/api/sql/instancefailovergroups/listbylocation) | 場所ごとにフェールオーバー グループをリストします。 |

---

## <a name="next-steps"></a>次のステップ

- 詳細なチュートリアルについては、以下を参照してください
  - [フェールオーバー グループに SQL Database を追加する](failover-group-add-single-database-tutorial.md)
  - [フェールオーバー グループにエラスティック プールを追加する](failover-group-add-elastic-pool-tutorial.md)
  - [フェールオーバー グループに SQL Managed Instance を追加する](../managed-instance/failover-group-add-instance-tutorial.md)
- サンプル スクリプトは、以下を参照してください。
  - [PowerShell を使用して Azure SQL Database 用にアクティブ geo レプリケーションを構成する](scripts/setup-geodr-and-failover-database-powershell.md)
  - [PowerShell を使用して、Azure SQL Database のプールされたデータベースに対してアクティブ geo レプリケーションを構成する](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [PowerShell を使用して Azure SQL Database をフェールオーバー グループに追加する](scripts/add-database-to-failover-group-powershell.md)
- ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](automated-backups-overview.md)」を参照してください
- 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](recovery-using-backups.md)に関する記事を参照してください。
- 新しいプライマリ サーバーとデータベースの認証要件については、 [障害復旧後の SQL Database のセキュリティ](active-geo-replication-security-configure.md)に関する記事を参照してください。
