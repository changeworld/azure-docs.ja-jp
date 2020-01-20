---
title: Azure Migrate についてよく寄せられる質問
description: Azure Migrate サービスに関する一般的な質問の回答を示します。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: 8bfa9237d365636c0bdaa3af06c5af23b683231d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563541"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。 この記事の内容についてさらに質問がある場合は、[Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)に投稿してください。 その他の質問については、次の記事を確認してください。

- Azure Migrate アプライアンスに関する[一般的な質問](common-questions-appliance.md)。
- 検出、評価、依存関係の視覚化に関する[質問](common-questions-discovery-assessment.md)。


## <a name="what-is-azure-migrate"></a>Azure Migrate とは

Azure Migrate は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。 [詳細については、こちらを参照してください](migrate-services-overview.md)。


## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate でできること

Azure Migrate を使用して、オンプレミスのインフラストラクチャ、アプリケーション、データの検出および評価を行い、Azure に移行します。 Azure Migrate は、オンプレミスの VMware VM、Hyper-V VM、物理サーバー、その他の仮想化された VM、データベース、Web アプリ、仮想デスクトップの評価と移行をサポートしています。 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate と Site Recovery の違いは何ですか。

Azure Migrate は、評価と Azure への移行を行うための中央ハブとなります。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) はディザスター リカバリー ソリューションです。 Azure Migrate では、Server Migration ツールは、一部のオンプレミス マシンのリフトアンドシフト移行に、いくつかのバックエンド Site Recovery 機能を使用します。

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server Assessment と MAP Toolkit の違いは何ですか。

Server Assessment は、移行準備を支援する評価と、Azure への移行のためのワークロードの評価を提供します。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) は、新しいバージョンの Windows クライアントやサーバー オペレーティング システムでの移行計画や、ソフトウェア使用状況の追跡など、他のタスクに役立ちます。 そのような目的には、引き続き MAP Toolkit を使用します。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Server Assessment と Site Recovery Deployment Planner の違いは何ですか。

Server Assessment は移行計画ツールです。 Site Recovery Deployment Planner は、ディザスター リカバリー計画ツールです。

- **オンプレミスの Azure への移行計画**:オンプレミスのサーバーを Azure に移行する予定がある場合は、移行計画に Server Assessment を使用します。 オンプレミスのワークロードが評価され、移行する際に役立つガイダンス、ツールが用意されています。 移行計画の準備ができたら、Azure Migrate Server Migration などのツールを使用して、マシンを Azure に移行できます。
- **Azure へのディザスター リカバリーの計画**:Site Recovery を使用してオンプレミスから Azure へのディザスター リカバリーを設定する場合は、Site Recovery Deployment Planner を使用します。 Deployment Planner は、ディザスター リカバリーを目的として、オンプレミス環境に対する Site Recovery 独自の詳細な評価を提供します。 また、レプリケーションやフェールオーバーなどのディザスター リカバリーに関する推奨事項を提供します。

## <a name="how-does-server-migration-work-with-site-recovery"></a>Server Migration と Site Recovery の連動のしくみ

- Azure Migrate を使用する場合、Server Migration では、オンプレミスの VMware VM のエージェントレスな移行が行われた後、移行が Azure Migrate に対してネイティブとなり、Site Recovery は使用されません。
- Azure Migrate を使用する場合、Server Migration では、VMware VM のエージェントベースの移行が行われるか、Hyper-V VM または物理サーバーが移行されます。その後、Azure Migrate Server Migration では、Azure Site Recovery レプリケーションが使用されます。


## <a name="which-geographies-are-supported"></a>サポートされている地域を教えてください。

[VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) および [Hyper-v Vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)で Azure Migrate がサポートされている地域を確認します。

## <a name="how-do-i-get-started"></a>開始するには?

必要なツールを特定し、それを Azure Migrate プロジェクトに追加します。 ISV ツールまたは Movere を追加している場合:
- ツールのポリシーに従って、ライセンスを取得するか、無料試用版にサインアップすることによって、使用を開始します。 ツールのライセンスは、ISV またはツールのライセンス モデルに準拠します。
- 各ツールには、Azure Migrate に接続するためのオプションがあります。 ツールの指示とドキュメントに従って、ツールを Azure Migrate に接続します。
Azure とその他のツール全体にわたって、Azure Migrate プロジェクト内から移行の過程を一元的に追跡します。

### <a name="how-do-i-delete-a-project"></a>プロジェクトを削除する方法を教えてください。

プロジェクトを削除する[方法](how-to-delete-project.md)を確認してください。 






## <a name="next-steps"></a>次のステップ
[Azure Migrate の概要](migrate-services-overview.md)を確認します。
