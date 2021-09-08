---
title: Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
description: この記事では、自動バックアップとオンデマンドのデータ復元のしくみについて説明します。 また、継続的および定期的な各バックアップ モードの違いについても説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/21/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9441cb178769b7d8cad6e60d0bf411c4c38710c5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473462"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、データのバックアップが一定の間隔で自動的に取得されます。 自動バックアップは、データベース操作のパフォーマンスや可用性に影響を与えずに取得されます。 すべてのバックアップは、ストレージ サービスに個別に格納されます。 自動バックアップは、Azure Cosmos アカウント、データベース、またはコンテナーを誤って削除または更新した後でデータの回復が必要になったシナリオの場合に役に立ちます。 バックアップには、次の 2 つのモードがあります。

* **定期的バックアップ モード** - このモードは、すべての既存のアカウントの既定のバックアップ モードです。 このモードでは、バックアップは定期的な間隔で実行され、サポート チームに要求を作成することによってデータが復元されます。 このモードでは、アカウントのバックアップ間隔と保有期間を構成します。 最大保有期間は 1 か月まで延長されます。 最小バックアップ間隔は 1 時間です。  詳細については、[定期的バックアップ モード](configure-periodic-backup-restore.md)に関する記事をご覧ください。

* **継続的バックアップ モード** – このモードは、Azure Cosmos DB アカウントの作成時に選択します。 このモードを使用すると、過去 30 日以内の任意の時点に復元できます。 詳細については、[継続的バックアップ モードの概要](continuous-backup-restore-introduction.md)と、[Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用した継続的バックアップのプロビジョニングに関する記事をご覧ください。

  > [!NOTE]
  > 新しいアカウントを継続的バックアップで構成した場合は、Azure portal、PowerShell、または CLI を使用して、セルフサービス復元を行うことができます。 アカウントを継続的モードで構成した場合は、定期的モードに切り替えて戻すことはできません。

Azure Synapse Link が有効なアカウントのバックアップと復元には、分析ストア データは含まれません。 Synapse Link が有効になっている場合、トランザクション ストア内のデータは、Azure Cosmos DB によって、スケジュールされたバックアップ間隔で引き続き自動的にバックアップされます。 現時点では、分析ストア内のデータの自動バックアップと復元はサポートされていません。

## <a name="next-steps"></a>次のステップ

次に、アカウントを定期的および継続的バックアップ モードで構成して管理する方法について学習できます。

* [定期的なバックアップ ポリシーを構成および管理する](configure-periodic-backup-restore.md)
* [継続的バックアップ](continuous-backup-restore-introduction.md) モードとは
* [Azure portal](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli)、または [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template) を使用して継続的バックアップをプロビジョニングします。
* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用して継続的バックアップ アカウントを復元します。
* [定期的なバックアップから継続的バックアップにアカウントを移行します](migrate-continuous-backup.md)。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
