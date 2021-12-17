---
title: Azure ディスク プール (プレビュー) を別のサブスクリプションに移動する
description: Azure ディスク プールを別のサブスクリプションに移動する方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71b0fd25412f7269e7de717ee2b8f1b4df056011
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082780"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>ディスク プール (プレビュー) を別のサブスクリプションに移動する

Azure ディスク プール (プレビュー) の移動には、ディスク プール自体、ディスク プールに含まれるディスク、ディスク プールの管理対象リソース グループ、管理対象リソース グループに含まれるすべてのリソースの移動が含まれます。 現在、Azure は、複数のリソース グループを一度に別のサブスクリプションに移動することをサポートしていません。 

- 既存のディスク プールのテンプレートをエクスポートします。
- 以前のディスク プールを削除します。
- ディスク プールの作成に必要な Azure リソースを移動します。
- ディスク プールを再デプロイします。

## <a name="export-your-existing-disk-pool-template"></a>既存のディスク プールのテンプレートをエクスポートする

再デプロイ プロセスをシンプルにするために、既存のディスク プールからテンプレートをエクスポートします。 このテンプレートを使用して、選択したサブスクリプションに同じ構成でディスク プールを再デプロイできます。 リソースからテンプレートをエクスポートする方法については、[この記事](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)を参照してください。

## <a name="delete-the-old-disk-pool"></a>以前のディスク プールを削除する

テンプレートのエクスポートが完了したら、以前のディスク プールを削除します。 ディスク プールを削除すると、ディスク プールのリソースとその管理対象リソース グループが削除されます。 ディスク プールを削除する方法については、[この記事](disks-pools-deprovision.md)を参照してください。

## <a name="move-your-disks-and-virtual-network"></a>ディスクと仮想ネットワークを移動する

ディスク プールが削除されたので、仮想ネットワーク、ディスク、場合によってはクライアントも、変更するサブスクリプションに移動できます。 Azure リソースを別のサブスクリプションに移動する方法については、[この記事](../azure-resource-manager/management/move-resource-group-and-subscription.md)を参照してください。

## <a name="redeploy-your-disk-pool"></a>ディスク プールを再デプロイする

他のリソースをサブスクリプションに移動したら、ディスク、仮想ネットワーク、サブネット、クライアントへのすべての参照が新しいリソースの URI を指すように、古いディスク プールのテンプレートを更新します。 これが完了したら、テンプレートを新しいサブスクリプションに再デプロイします。 テンプレートを編集してデプロイする方法については、[この記事](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template)を参照してください。

## <a name="next-steps"></a>次のステップ

ディスク プールを管理する方法については、[ディスク プールの管理](disks-pools-manage.md)に関するページを参照してください。
