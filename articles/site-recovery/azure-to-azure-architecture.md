---
title: Azure Site Recovery での Azure から Azure へのレプリケーション アーキテクチャ | Microsoft Docs
description: この記事では、Azure Site Recovery サービスを使用して、Azure リージョン間で Azure VM をレプリケートするときに使用される、コンポーネントとアーキテクチャの概要を説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 33ab90f958e5033c0c563e4fd8921ee1f7d57c47
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915675"
---
# <a name="azure-to-azure-replication-architecture"></a>Azure から Azure へのレプリケーション アーキテクチャ


この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure リージョン間で Azure 仮想マシン (VM) をレプリケート、フェールオーバー、および復旧する場合に使用されるアーキテクチャについて説明します。




## <a name="architectural-components"></a>アーキテクチャ コンポーネント

次の図は、特定のリージョン (この例では米国東部) における Azure VM 環境の概要を示しています。 Azure VM 環境:
- 管理ディスクまたは管理対象外ディスクがストレージ アカウントに分散されている VM でアプリを実行できます。
- VM は、仮想ネットワーク内の 1 つ以上のサブネットに含めることができます。


**Azure から Azure へのレプリケーション**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>レプリケーション プロセス

### <a name="step-1"></a>手順 1

Azure VM レプリケーションを有効にすると、ソース リージョンの設定に基づいて、次のようなリソースがターゲット リージョンに自動的に作成されます。 必要に応じて、ターゲット リソースの設定をカスタマイズできます。

![レプリケーション プロセスの有効化、手順 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**リソース** | **詳細**
--- | ---
**ターゲット リソース グループ** | フェールオーバー後、レプリケートされた VM が所属するリソース グループです。 このリソース グループの場所は、ソース仮想マシンがホストされている Azure リージョンを除き、どの Azure リージョンでも構いません。
**ターゲット仮想ネットワーク** | フェールオーバー後、レプリケートされた VM が配置される仮想ネットワークです。 ソース仮想ネットワークとターゲット仮想ネットワークの間にネットワーク マッピングが作成されます (または、その逆)。
**キャッシュ ストレージ アカウント** | ソース VM の変更は、ターゲット ストレージ アカウントにレプリケートされる前に、追跡され、ソースの場所のキャッシュ ストレージ アカウントに送信されます。 この手順により、VM で実行されている運用アプリケーションへの影響を最小限に抑えられます。
**ターゲット ストレージ アカウント (ソース VM では、管理ディスクが使用されません)**  | データのレプリケート先となるターゲットの場所のストレージ アカウント。
** レプリカ管理ディスク (ソース VM が管理ディスクにある場合)**  | データのレプリケート先となるターゲットの場所の管理ディスク。
**ターゲット可用性セット**  | フェールオーバー後、レプリケートされた VM が配置される可用性セット。

### <a name="step-2"></a>手順 2.

レプリケーションが有効な場合、Site Recovery 拡張機能のモビリティ サービスは VM に自動的にインストールされます。

1. VM は、Site Recovery に登録されます。

2. 継続的なレプリケーションが VM 用に構成されます。 VM ディスクへのデータ書き込みが、ソースの場所のキャッシュ ストレージ アカウントに継続的に転送されます。

   ![レプリケーション プロセスの有効化、手順 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery には、VM への受信接続は不要です。 以下に対しては、送信接続のみが必要になります。

 - Site Recovery サービス URL / IP アドレス
 - Office 365 認証の URL / IP アドレス
 - キャッシュ ストレージ アカウントの IP アドレス

マルチ VM 整合性を有効にすると、レプリケーション グループ内のマシンは、ポート 20004 を介して相互に通信します。 ポート 20004 経由での VM 間の内部通信をブロックするファイアウォール アプライアンスがないことを確認します。

> [!IMPORTANT]
Linux VM をレプリケーション グループに含めるには、ポート 20004 の送信トラフィックが、特定の Linux バージョンのガイダンスに従って手動で開かれていることを確認します。

### <a name="step-3"></a>手順 3.

継続的なレプリケーションが進行中になった後、ディスク書き込みが直ちにキャッシュ ストレージ アカウントに転送されます。 データは Site Recovery によって処理され、ターゲット ストレージ アカウントまたはレプリカ管理ディスクに送信されます。 データ処理の完了後、復旧ポイントが数分ごとにターゲット ストレージ アカウントに生成されます。

## <a name="failover-process"></a>フェールオーバー プロセス

フェールオーバーの開始時、VM は、ターゲット リソース グループ、ターゲット仮想ネットワーク、ターゲット サブネット、およびターゲット可用性セットに作成されます。 フェールオーバー中は、任意の復旧ポイントを使用できます。

![フェールオーバー プロセス](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>次の手順

Azure VM をセカンダリ リージョンに[迅速にレプリケート](azure-to-azure-quickstart.md)します。
