---
title: SAP HANA バックアップのための Azure Backup アーキテクチャ
description: SAP HANA バックアップのための Azure Backup アーキテクチャについて説明します。
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: df8bb6adfeff88eafba19bcc459f887f075aa75b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244580"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>SAP HANA バックアップのための Azure Backup アーキテクチャ

[Azure Backup サービス](./backup-overview.md)を使用すると、SAP HANA データベースのデータをアプリケーションで一貫した方法でバックアップできます。 この記事では、Azure Backup のアーキテクチャ、コンポーネント、およびプロセスについて説明します。

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>Azure Backup が SAP HANA データベースと連携する方法

Azure Backup には、Azure VM 上で動作する SAP HANA データベースをバックアップするストリーミング バックアップ ソリューションが用意されています。 このバックアップ オファリングはインフラストラクチャの設定が不要なので、バックアップインフラストラクチャをデプロイおよび管理する必要はありません。

Azure Backup は、SAP によって [Backint 認定](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)されているため、SAP HANA のネイティブ API を使用したバックアップをネイティブでサポートしています。 このソリューションにより、Azure VM 上で動作する SAP HANA データベースのバックアップと復元をシームレスに行い、Azure Backup に用意されているエンタープライズ管理機能を使用することができます。

Azure Backup が SAP HANA に提供する付加価値に関する[詳細情報を参照してください](./sap-hana-db-about.md#added-value)。

## <a name="where-is-the-data-backed-up"></a>データがバックアップされる場所

Azure Backup によってバックアップされたデータは Recovery Services コンテナーに格納されます。 コンテナーは、バックアップ コピー、復旧ポイント、バックアップ ポリシーなどのデータを格納するために使用される、Azure 上のオンライン ストレージ エンティティです。

Recovery Services コンテナーに関する[詳細情報を参照してください](./backup-azure-backup-faq.yml)。

## <a name="backup-agents"></a>Backup のエージェント

Azure VM 上で動作している SAP HANA データベースをバックアップするには、Azure VM へのプラグイン (SAP HANA Backup エージェント) のインストールを許可する必要があります。 このプラグインによって HANA Backint と接続され、Azure Backup サービスからコンテナーへデータを移動するのに役立ちます。 また、Azure Backup から復元を実行できるようになります。

## <a name="backup-types"></a>バックアップの種類

SAP HANA のバックアップの種類に関する[詳細情報を参照してください](./backup-architecture.md#sap-hana-backup-types)。

## <a name="about-architecture"></a>アーキテクチャの概要

[SAP HANA データベース用 Azure Backup のアーキテクチャの概要](./sap-hana-db-about.md#backup-architecture)を参照してください。 バックアップ プロセスの詳細については、次のプロセスを参照してください。

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="SAP HANA データベースのバックアップ プロセスを示す図。":::

1. バックアップ プロセスを開始するには、[Azure に Recovery Services コンテナー](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault)を作成します。 このコンテナーは、時間の経過と共に作成されたバックアップと復元ポイントを格納するために使用されます。

1. SAP HANA サーバーを実行している Azure VM がコンテナーに登録されており、バックアップされるデータベースが[検出](./tutorial-backup-sap-hana-db.md#discover-the-databases)されます。 Azure Backup サービスでデータベースを検出できるようにするには、HANA サーバー上でこの[事前登録スクリプト](https://go.microsoft.com/fwlink/?linkid=2173610)を root ユーザーとして実行する必要があります。 
   >[!Note]
   >このインスタンスのデータベースを検出する間は、HANA インスタンスの稼働状態を確保します。

1. また、[その他の前提条件](./tutorial-backup-sap-hana-db.md#prerequisites)が満たされていることを確認します。

   >[!Important]
   >適切なネットワーク接続を設定するための前提条件が満たされていることを確認します。 推奨事項については、[バックアップ オファリングを使用する追加のネットワーク コンポーネントを使用して、SAP HANA で動作している Azure VM を設定する方法](./tutorial-backup-sap-hana-db.md#set-up-network-connectivity)に関する記事を参照してください。

1. [事前登録スクリプトで実行される処理](./tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)の詳細を参照してください。 このスクリプトを実行せずに SAP HANA データベースのバックアップを構成しようとすると、エラー _UserErrorHanaScriptNotRun_ を受け取る場合があります。

1. Azure Backup サービスにより、登録されている SAP HANA サーバー上に HANA 用の Azure Backup プラグインがインストールされるようになりました。 このプラグインにより、事前登録スクリプトによって作成された Backup ユーザーが使用され、すべてのバックアップと復元の操作が実行されます。

1. 検出されたデータベースで[バックアップを構成](./tutorial-backup-sap-hana-db.md#configure-backup)するには、必要なバックアップポリシーを選択し、バックアップを有効にする必要があります。

1. SAP HANA 用 Azure Backup (Backint 認定ソリューション) は、基になるディスクまたは VM の種類に依存しません。 このバックアップは、SAP HANA で生成されたストリームによって実行されます。

## <a name="backup-flow"></a>バックアップ フロー

1. スケジュールされたバックアップは、HANA VM 上に作成された crontab エントリによって管理され、オンデマンド バックアップは、Azure Backup サービスによって直接トリガーされます。

1. SAP HANA Backup Engine および Backint がバックアップ要求を受け取ると、セーブ ポイントを作成し、基礎となるストレージ ボリュームにデータを移動するという処理で SAP HANA データベースのバックアップが準備されます。

1. 次に、Tenant データベースのインデックス サーバーと XS エンジン、SYSTEMDB のネーム サーバーなど、基盤となるデータ ボリュームからの読み取り操作が Backint によって実行されます。 Premium SSD ディスクの場合、バックアップ ストリーミング操作に最適な I/O スループットを実現できます。 ただし、M64I を使用するキャッシュされていないディスクを使用すると、より高速になります。

1. バックアップ データをストリーミングするために、Backint によって最大 3 つのパイプが作成され、Azure Backup の Recovery Services コンテナーに直接書き込まれます。

   ファイアウォールまたは NVA を使用していない場合、バックアップ ストリームは Azure ネットワークを介して Recovery Services コンテナーに転送されます。 また、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)または[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を設定すると、NVA または Azure Firewall をスキップして SAP HANA から Azure Storage にバックアップ トラフィックを直接送信できるようになります。 さらに、ファイアウォールまたは NVA を使用する場合、Azure Active Directory と Recovery Services コンテナーへのトラフィックはファイアウォールまたは NVA を通過するため、全体のバックアップ パフォーマンスには影響しません。 

1. Azure Backup により、非ログ バックアップの場合は最大 420 MB/秒、ログ バックアップの場合は最大 100 MB/秒の速度の達成が試行されます。 バックアップと復元のスループット パフォーマンスに関する[詳細情報を参照してください](./tutorial-backup-sap-hana-db.md#understanding-backup-and-restore-throughput-performance)。

1. 詳細なログは、SAP HANA インスタンス上の _backup.log_ と _backint.log_ のファイルに書き込まれます。

1. バックアップ ストリーミングが完了すると、カタログは Recovery Services コンテナーにストリーミングされます。 バックアップ (完全、差分、増分、ログ) とこのバックアップのカタログの両方が正常にストリーミングされ、Recovery Services コンテナーに保存された場合、Azure Backup により、バックアップ操作は成功したと判断されます。

次の SAP HANA の設定を参照し、上記のバックアップ操作の実行を確認してください。

**SAP HANA の設定シナリオ: Azure ネットワーク - NVA または Azure Firewall なし**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="NVA または Azure Firewall を使用しない Azure ネットワークの場合の SAP HANA 設定を示す図。":::

**SAP HANA の設定シナリオ: Azure ネットワーク - UDR + NVA または Azure Firewall あり**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="UDR + NVA または Azure Firewall を使用する Azure ネットワークの場合の SAP HANA 設定を示す図。":::

>[!Note]
>NVA または Azure Firewall を使用すると、SAP HANA ストリームを Azure Storage または Recovery Services コンテナー (データ プレーン) にバックアップするときにオーバーヘッドが発生する可能性があります。 上の図の "_ポイント 6_" を参照してください。

**SAP HANA の設定シナリオ: Azure ネットワーク - UDR + NVA または Azure Firewall + プライベート エンドポイントまたはサービス エンドポイントあり**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="UDR + NVA または Azure Firewall + プライベート エンドポイントまたはサービス エンドポイントがある Azure ネットワークの場合の SAP HANA の設定を示す図。":::

## <a name="next-steps"></a>次のステップ

[Azure VM での SAP HANA データベースのバックアップ](./backup-azure-sap-hana-database.md)。
