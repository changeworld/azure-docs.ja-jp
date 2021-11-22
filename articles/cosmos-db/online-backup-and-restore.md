---
title: Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
description: この記事では、自動バックアップとオンデマンドのデータ復元のしくみについて説明します。 また、継続的および定期的な各バックアップ モードの違いについても説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/15/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1a87371e697d39b73a647e7dea1dbbceb1e028d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523222"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、データのバックアップが一定の間隔で自動的に取得されます。 自動バックアップは、データベース操作のパフォーマンスや可用性に影響を与えずに取得されます。 すべてのバックアップは、ストレージ サービスに個別に格納されます。 自動バックアップは、Azure Cosmos アカウント、データベース、またはコンテナーを誤って削除または更新した後でデータの回復が必要になったシナリオの場合に役に立ちます。 Azure Cosmos DB バックアップは、Microsoft マネージド サービス キーを使用して暗号化されます。 これらのバックアップは、セキュリティで保護された非パブリック ネットワークを使用して転送されます。 つまり、バックアップ データは、ネットワーク上で転送されるときも、保存中でも暗号化されたままになるということです。 特定のリージョン内のアカウントのバックアップは、同じリージョン内のストレージ アカウントにアップロードされます。

## <a name="backup-modes"></a>バックアップ モード

バックアップには、次の 2 つのモードがあります。

* **定期的バックアップ モード** - このモードは、すべての既存のアカウントの既定のバックアップ モードです。 このモードでは、バックアップは定期的な間隔で実行され、サポート チームに要求を作成することによってデータが復元されます。 このモードでは、アカウントのバックアップ間隔と保有期間を構成します。 最大保有期間は 1 か月まで延長されます。 最小バックアップ間隔は 1 時間です。  詳細については、[定期的バックアップ モード](configure-periodic-backup-restore.md)に関する記事をご覧ください。

* **継続的バックアップ モード** – このモードは、Azure Cosmos DB アカウントの作成時に選択します。 このモードを使用すると、過去 30 日以内の任意の時点に復元できます。 詳細については、[継続的バックアップ モードの概要](continuous-backup-restore-introduction.md)と、[Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用した継続的バックアップのプロビジョニングに関する記事をご覧ください。

  > [!NOTE]
  > 新しいアカウントを継続的バックアップで構成した場合は、Azure portal、PowerShell、または CLI を使用して、セルフサービス復元を行うことができます。 アカウントを継続的モードで構成した場合は、定期的モードに切り替えて戻すことはできません。

Azure Synapse Link が有効なアカウントの場合、分析ストア データはバックアップと復元に含まれません。 Synapse Link が有効になっている場合、Azure Cosmos DB がトランザクション ストア内のデータを、スケジュールされたバックアップ間隔で引き続き自動的にバックアップします。 現時点では、分析ストア内のデータの自動バックアップと復元はサポートされていません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-restore-from-an-account-a-in-subscription-s1-to-account-b-in-a-subscription-s2"></a>サブスクリプション S1 のアカウント A からサブスクリプション S2 のアカウント B に復元できますか?

いいえ。 復元できるのは、同じサブスクリプション内のアカウント間のみです。

### <a name="can-i-restore-into-an-account-that-has-fewer-partitions-or-low-provisioned-throughput-than-the-source-account"></a>ソース アカウントよりもパーティション数が少ない、またはプロビジョニングされたスループットが低いアカウントに復元できますか?

いいえ。 RU/秒またはパーティションの数が少ないアカウントに復元することはできません。

## <a name="next-steps"></a>次のステップ

次に、アカウントを定期的および継続的バックアップ モードで構成して管理する方法について学習できます。

* [定期的なバックアップ ポリシーを構成および管理する](configure-periodic-backup-restore.md)
* [継続的バックアップ](continuous-backup-restore-introduction.md) モードとは
* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して継続的バックアップ アカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
