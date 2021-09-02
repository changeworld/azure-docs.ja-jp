---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: 事業継続とディザスター リカバリー (BCDR)'
description: あらゆるミッション クリティカルなシステムと同様に、バックアップと復元は、ディザスター リカバリー (BCDR) 戦略とともに、システム設計全体の重要な部分です。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 35ab4f952b2e8082f4923926f11698fef352c8f8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296221"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-business-continuity-and-disaster-recovery-bcdr"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: 事業継続とディザスター リカバリー (BCDR)

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[最適化](11-optimization.md)

## <a name="back-up-and-restore"></a>バックアップと復元

あらゆるミッション クリティカルなシステムと同様に、バックアップと復元は、ディザスター リカバリー (BCDR) 戦略とともに、システム設計全体の重要な部分です。 予期しないイベントが発生した場合は、妥当な時間 (目標復旧時間) で特定の時点 (目標復旧ポイント) にデータを復元できる必要があります。

### <a name="backup"></a>バックアップ

Azure Database for MySQL では、既定で 7 日間の自動バックアップがサポートされています。 これを現在の最大値である 35 日に変更することが適切な場合があります。 この値を 35 日に変更した場合、割り当てられたストレージの 1 倍を超える追加のバックアップ ストレージに対し料金が発生する点に注意してください。

「[Azure Database for MySQL でのバックアップと復元](../../concepts-backup.md)」の記事で説明されているように、データベース バックアップ機能には現在いくつかの制限があります。 実装する必要がある追加の戦略を決定する際には、これらについて理解しておくことが重要です。

注意すべき項目には次のものがあります。

- バックアップに直接アクセスすることはできません

- 最大 4 TB を許容するレベルでは、1 週間に 1 回の完全バックアップ、1 日に 2 回の差分バックアップ、5 分ごとにログ記録が行われます

- 最大 16 TB を許容するレベルでは、バックアップはスナップショット ベースで行われます

    > [!NOTE]
    > [一部のリージョン](../../concepts-pricing-tiers.md#storage)では、最大 16 TB のストレージはまだサポートされていません。

### <a name="restore"></a>復元

サーバーの作成時に冗長性 (ローカルまたは geo) を構成する必要があります。 ただし、geo リストアを実行でき、復元プロセス中にこれらのオプションを変更できます。 復元操作を実行すると、接続が一時的に停止する可能性があり、すべてのアプリケーションが復元プロセス中にダウンします。

データベースの復元中に、データベースの外部にあるすべてのサポート項目を復元する必要があります。 移行プロセスを確認してください。 詳細については、「[復元後のタスクの実行](../../concepts-backup.md#perform-post-restore-tasks)」を参照してください。

## <a name="read-replicas"></a>読み取りレプリカ

[読み取りレプリカ](../../concepts-read-replicas.md)を使用すると、MySQL の読み取りスループットを向上させ、地域ユーザーのパフォーマンスを向上させ、ディザスター リカバリーを実装することができます。 1 つ以上の読み取りレプリカを作成する場合は、プライマリ サーバーと同じコンピューティングとストレージに追加料金が適用されることに注意してください。

## <a name="deleted-servers"></a>削除されたサーバー

管理者や悪意のあるユーザーが Azure portal で、または自動メソッドを使用してサーバーを削除した場合、すべてのバックアップと読み取りレプリカが削除されます。 Azure Database for MySQL リソース グループに[リソース ロック](../../../azure-resource-manager/management/lock-resources.md)を作成して、追加の削除防止レイヤーをインスタンスに加えることが重要です。

## <a name="regional-failure"></a>リージョンの障害

まれですが、リージョンの障害が起こった場合は、geo 冗長バックアップまたは読み取りレプリカを使用して、データ ワークロードを再度実行することができます。 予期しないリージョンの障害からできるだけ保護するには、geo レプリケーションと読み取りレプリカの両方を使用できるようにしておくことが最善です。

> [!NOTE]
> データベース サーバーのリージョンを変更すると、エンドポイントが変更され、それに応じてアプリケーション構成を更新する必要が生じることにもなります。

### <a name="load-balancers"></a>ロード バランサー

アプリケーションが世界中の多数の異なるインスタンスから構成されている場合は、一部のクライアントを更新できなくなる可能性があります。 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) または [Application Gateway](../../../application-gateway/overview.md) を利用して、シームレスなフェールオーバー機能を実装します。 これらのツールは、便利で時間の節約になりますが、リージョンのフェールオーバー機能には必要ありません。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI では、読み取りレプリカのフェールオーバー機能をテストする必要があったため、次に説明する手順を実行しました。

### <a name="creating-a-read-replica"></a>読み取りレプリカの作成

- Azure portal を開きます。

- Azure Database for MySQL インスタンスを参照します。

- **[設定]** で、 **[レプリケーション]** を選択します。

- **[レプリカの追加]** を選択します。

- サーバー名を入力します。

- リージョンを選択します。

- **[OK]** を選択し、インスタンスがデプロイされるまで待ちます。 メイン インスタンスのサイズによって、レプリケーションに時間がかかることがあります。

    > [!NOTE]
    > 各レプリカには、メイン インスタンスと同じ追加料金が発生します。

### <a name="fail-over-to-read-replica"></a>読み取りレプリカにフェールオーバーする

読み取りレプリカが作成され、レプリケーション プロセスを完了したら、フェールオーバーに使用できます。 レプリケーションはフェールオーバー中に停止し、読み取りレプリカが専用のメイン インスタンスになります。

フェールオーバーの手順:

- Azure portal を開きます。

- Azure Database for MySQL インスタンスを参照します。

- **[設定]** で、 **[レプリケーション]** を選択します。

- いずれかの読み取りレプリカを選択します。

- **[レプリケーションの停止]** を選択します。 これにより、読み取りレプリカが切断されます。

- すべてのアプリケーションの接続文字列を、新しいメイン インスタンスを指すように変更します。

## <a name="bcdr-checklist"></a>BCDR チェックリスト

- 要件を満たすようにバックアップの頻度を変更します。

- 読み取り集中型のワークロードとリージョン内フェールオーバーに合わせて読み取りレプリカを設定します。

- リソース グループにリソース ロックを作成します。

- 迅速なフェールオーバーを行えるようにアプリケーションの負荷分散戦略を実装します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Security](./13-security.md)