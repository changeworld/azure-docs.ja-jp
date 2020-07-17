---
title: Azure Migrate のよくあるご質問
description: Azure Migrate サービスに関する一般的な質問の回答を示します。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81530319"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。 この記事を読んでから疑問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿できます。 また、次の記事も参照できます。

- [Azure Migrate アプライアンス](common-questions-appliance.md)に関する質問
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md)に関する質問

## <a name="what-is-azure-migrate"></a>Azure Migrate とは

Azure Migrate は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの ISV オファリングが用意されています。 [詳細については、こちらを参照してください](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate でできること

Azure Migrate を使用して、オンプレミスのインフラストラクチャ、アプリケーション、データの検出および評価を行い、Azure に移行します。 Azure Migrate は、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想化された VM、データベース、Web アプリ、仮想デスクトップの評価と移行をサポートしています。 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate と Azure Site Recovery の違い

[Azure Migrate](migrate-services-overview.md) は、評価と Azure への移行を行うための中央ハブとなります。 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) はディザスター リカバリー ソリューションです。 

Azure Migrate: Server Migration ツールは、一部のオンプレミス マシンのリフトアンドシフト移行に、いくつかのバックエンド Site Recovery 機能を使用します。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate:Server Assessment と Map Toolkit の違いは何ですか。

Server Assessment は、移行準備を支援する評価と、Azure への移行のためのワークロードの評価を提供します。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) は、新しいバージョンの Windows クライアントおよびサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡など、他のタスクに役立ちます。 そのような目的には、引き続き MAP Toolkit を使用します。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Server Assessment と Site Recovery Deployment Planner の違いは何ですか。

Server Assessment は移行計画ツールです。 Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

操作の目的に応じてツールを選択します。

- **オンプレミスの Azure への移行計画**:オンプレミスのサーバーを Azure に移行する予定がある場合は、移行計画に Server Assessment を使用します。 Server Assessment ではオンプレミスのワークロードが評価され、移行する際に役立つガイダンスとツールが提供されます。 移行計画の準備ができたら、Azure Migrate:Server Migration などのツールを使ってマシンを Azure に移行できます。
- **Azure へのディザスター リカバリーの計画**:Site Recovery を使用してオンプレミスから Azure へのディザスター リカバリーを設定する場合は、Site Recovery Deployment Planner を使用します。 Deployment Planner は、ディザスター リカバリーを目的として、オンプレミス環境に対する Site Recovery 独自の詳細な評価を提供します。 また、レプリケーションやフェールオーバーなどのディザスター リカバリーに関する推奨事項を提供します。

## <a name="how-does-server-migration-work-with-site-recovery"></a>Server Migration と Site Recovery の連動のしくみ

- Azure Migrate を使用する場合、Server Migration を使用して、オンプレミスの VMware VM の "*エージェントレス*" 移行を行った場合、移行が Azure Migrate に対してネイティブとなり、Site Recovery は使用されません。
- Azure Migrate を使用する場合、Server Migration を使用して、VMware VM の "*エージェントベースの*" 移行を実行するか、Hyper-V VM または物理サーバーを移行する場合、Azure Migrate:Server Migration は Azure Site Recovery のレプリケーション エンジンを使用します。

## <a name="which-geographies-are-supported"></a>サポートされている地域を教えてください。

[パブリック クラウド](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

## <a name="how-do-i-get-started"></a>開始するには?

必要なツールを特定し、次にそのツールを Azure Migrate プロジェクトに追加します。 

ISV ツールまたは Movere を追加する場合は、次のようにします。

1. ツールのポリシーに従って、ライセンスを取得するか、無料試用版にサインアップすることによって、使用を開始します。 ツールのライセンスは、ISV またはツールのライセンス モデルに準拠します。
2. 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示とドキュメントに従って、ツールを Azure Migrate に接続します。

Azure とその他のツール全体にわたって、Azure Migrate プロジェクト内から移行の過程を追跡できます。

## <a name="how-do-i-delete-a-project"></a>プロジェクトを削除する方法を教えてください。

[プロジェクトを削除する](how-to-delete-project.md)方法を確認してください。 

## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。
