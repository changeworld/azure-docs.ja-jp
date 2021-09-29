---
title: Azure API Management をリージョン間で移行する方法
description: API Management インスタンスを 1 つのリージョンから別のリージョンに移行する方法を学習します。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: danlep
ms.custom: subject-moving-resources
ms.openlocfilehash: 1007241c09e22d1fb35f6cc12733c04946af88f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591788"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>Azure API Management をリージョン間で移動する方法

この記事では、API Management インスタンスを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、インスタンスを別のリージョンに移動する場合があります。 例:

* API コンシューマーの近くにインスタンスを配置する
* 特定のリージョンでのみ使用できる機能をデプロイする
* 内部ポリシーとガバナンスの要件を満たす

ある Azure リージョンから別のリージョンに API Management インスタンスを移動するには、サービスの[バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)操作を使用します。 API Management インスタンスの名前は、異なるものでも既存の名前でも使用できます。 

> [!NOTE]
> API Management では[複数リージョンのデプロイ](api-management-howto-deploy-multi-region.md)もサポートされており、1 つの Azure API Management サービスが複数の Azure リージョンに分散されます。 複数リージョンのデプロイにより、地理的に分散した API コンシューマーによって認識される要求待ち時間が短くなり、1 つのリージョンがオフラインになった場合でもサービスの可用性を向上できます。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>注意事項

* ソースとターゲットのリージョンで同じ API Management 価格レベルを選択します。 
* 異なる種類のクラウド間で移行を行うと、バックアップと復元は機能しません。 そのため、リソースを[テンプレートとして](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)エクスポートします。 次に、エクスポートしたテンプレートをターゲットの Azure リージョンに合わせて調整し、リソースを再作成します。 

## <a name="prerequisites"></a>前提条件

* API Management の[バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)の操作に関する要件と制限事項を確認します。 
* 「[バックアップされないもの](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up)」を参照します。 インスタンスを移動した後に手動で作成し直す必要がある設定とデータを記録しておきます。
* ソース リージョンに[ストレージ アカウント](../storage/common/storage-account-create.md?tabs=azure-portal)を作成します。 このアカウントを使用して、ソース インスタンスをバックアップします。 

## <a name="prepare-and-move"></a>準備と移動

### <a name="option-1-use-a-different-api-management-instance-name"></a>オプション 1: 別の API Management インスタンス名を使用する

1. ターゲット リージョンで、ソース API Management インスタンスと同じ価格レベルで新しい API Management インスタンスを作成します。 新しいインスタンスには異なる名前を使用します。
1. 既存の API Management インスタンスをストレージ アカウントに[バックアップします](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)。 
1. ソース インスタンスのバックアップを、新しい API Management インスタンスに[復元します](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)。
1. ソース リージョンの API Management インスタンスを指すカスタム ドメインがある場合は、新しい API Management インスタンスを指すようにカスタム ドメインの CNAME を更新します。 

### <a name="option-2-use-the-same-api-management-instance-name"></a>オプション 2:同じ API Management インスタンス名を使用する

> [!WARNING]
> このオプションを使用すると、元の API Management インスタンスが削除されるので、移行の間にダウンタイムが発生します。 ソース インスタンスを削除する前に、有効なバックアップがあることを確認してください。

1. 既存の API Management インスタンスをストレージ アカウントに[バックアップします](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)。 
1. ソース リージョンの API Management インスタンスを削除します。 
1. ターゲット リージョンで、ソース リージョンのインスタンスと同じ名前を持つ新しい API Management インスタンスを作成します。
1. ソース インスタンスのバックアップを、ターゲット リージョンの新しい API Management インスタンスに[復元します](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)。  

## <a name="verify"></a>検証

1. ターゲット リージョンの API Management インスタンスにアクセスする前に、復元操作が正常に完了したことを確認します。
1. 復元操作で自動的に移動されない設定を構成します。 例: 仮想ネットワークの構成、マネージド ID、開発者ポータルのコンテンツ、カスタム ドメインとカスタム CA 証明書。
1. ターゲット リージョンの API Management エンドポイントにアクセスします。 たとえば、API をテストしたり、開発者ポータルにアクセスしたりします。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

オプション 1 を使用して API Management インスタンスを移動した場合は、ターゲット インスタンスを正常に復元して構成した後、ソース インスタンスを削除できます。

## <a name="next-steps"></a>次の手順

* バックアップと復元機能の詳細については、[ディザスター リカバリーを実装する方法](api-management-howto-disaster-recovery-backup-restore.md)に関するページを参照してください。
* Azure リソースの移行の詳細については、[Azure のリージョン間の移行に関するガイダンス](https://github.com/Azure/Azure-Migration-Guidance)を参照してください。
* [ご自分のクラウドの支出を最適化して節約する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
