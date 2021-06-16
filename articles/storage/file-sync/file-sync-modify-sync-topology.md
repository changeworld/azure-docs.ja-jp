---
title: Azure File Sync トポロジを変更する |Microsoft Docs
description: Azure File Sync の同期トポロジを変更する方法に関するガイダンス
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 12bab4b8099b074f79d0e5307780ec34178f13b8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968478"
---
# <a name="modify-your-azure-file-sync-topology"></a>Azure File Sync トポロジを修正する

この記事では、お客様が Azure File Sync トポロジを変更する最も一般的な方法と、その方法に関するレコメンデーションについて説明します。 Azure File Sync トポロジを変更する場合は、エラーやデータ損失の可能性を回避するために、以下のベストプラクティスを参照してください。

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>サーバー エンドポイントを別の Azure File Sync ストレージ同期サービスに移行する

ローカル サーバーでデータが最新の状態になっていることを確認したら、サーバー エンドポイントのプロビジョニングを解除します。 これを行う方法については、「[Azure File Sync サーバー エンドポイントのプロビジョニングを解除する](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)」を参照してください。 その後、目的の同期グループとストレージ同期サービスで再プロビジョニングします。

サーバーに関連付けられているすべてのサーバー エンドポイントを別の同期グループまたはストレージ同期サービスに移行する場合は、「[登録済みサーバーに関連付けられているすべてのサーバー エンドポイントのプロビジョニングを解除する](#deprovision-all-server-endpoints-associated-with-a-registered-server)」を参照してください。

## <a name="change-the-granularity-of-a-server-endpoint"></a>サーバー エンドポイントの細分性の変更

ローカル サーバー上のデータが最新であることを確認した後 (「[Azure File Sync サーバー エンドポイントのプロビジョニングを解除する](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)」を参照)、サーバー エンドポイントのプロビジョニングを解除します。 その後、必要な細分性で再プロビジョニングします。

## <a name="deprovision-azure-file-sync-topology"></a>Azure File Sync トポロジのプロビジョニング解除

Azure File Sync リソースは、サーバー エンドポイント、同期グループ、ストレージ サービスなど、特定の順序でプロビジョニング解除する必要があります。 フロー全体が以下に記載されていますが、任意のレベルで停止することもできます。 

まず、Azure portal でストレージ同期サービス リソースに移動し、ストレージ同期サービスで同期グループを選択します。 「[Azure File Sync サーバー エンドポイントのプロビジョニングを解除する](./file-sync-deprovision-server-endpoint.md)」の手順に従って、サーバー エンドポイントを削除するときにデータの整合性と可用性を確保します。 同期グループまたはストレージ同期サービスのプロビジョニングを解除するには、すべてのサーバー エンドポイントを削除する必要があります。 特定のサーバー エンドポイントのみを削除することを目的としている場合は、ここで停止することができます。 

同期グループ内のすべてのサーバー エンドポイントを削除したら、クラウド エンドポイントを削除します。 

その後、同期グループを削除します。 

削除するストレージ同期サービスのすべての同期グループに対して、これらの手順を繰り返します。 そのストレージ同期サービス内のすべての同期グループが削除されたら、ストレージ同期サービスのリソースを削除します。

## <a name="rename-a-server-endpoint-path-or-sync-group"></a>サーバー エンドポイントのパスまたは同期グループの名前を変更する

現時点では、これはサポートされていません。 

現在 D ドライブを使用していて、クラウドへの移行を計画している場合は、「[VM の D: ドライブをデータディスクにする - Azure Virtual Machines](../../virtual-machines/windows/change-drive-letter.md)」を参照してください。

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>登録済みサーバーに関連付けられているすべてのサーバー エンドポイントをプロビジョニング解除する

プロビジョニング解除する前にデータが安全で完全に更新されるようにするには、「[Azure File Sync サーバーエンドポイントのプロビジョニングを解除する](./file-sync-deprovision-server-endpoint.md)」を参照してください。

ストレージ同期サービスリソースに移動し、登録済みサーバー タブに移動します。登録を解除するサーバーを選択し、**サーバーの登録解除** を選択します。 これにより、そのサーバーに関連付けられているすべてのサーバー エンドポイントがすぐにプロビジョニング解除されます。

## <a name="next-steps"></a>次のステップ
* [Azure File Sync サーバー エンドポイントのプロビジョニングを解除する](./file-sync-deprovision-server-endpoint.md)