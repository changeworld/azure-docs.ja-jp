---
title: Azure ディスク プール (プレビュー) のトラブルシューティングの概要
description: Azure ディスク プールに関する問題のトラブルシューティング。 一般的なエラー コードとその解決方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 878cec7021ebcd7c3b63ae4af1f29a0793977280
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277281"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Azure ディスク プール (プレビュー) のトラブルシューティング

この記事では、Azure ディスク プール (プレビュー) に関連する一般的なエラー コードの一部を紹介します。 また、ディスク プールの状態に関して考えられる解決方法をわかりやすく説明します。

## <a name="disk-pool-status"></a>ディスク プールの状態

ディスク プールと iSCSI ターゲットには、それぞれ "**不明**"、"**実行中**"、"**更新中**"、"**停止済み (割り当て解除)** " という 4 つの状態があります。

"**不明**" とは、リソースの状態が正しくない、または不明であることを意味します。 復旧を試みるには、リソースに対して更新操作 (ディスクまたは LUNS の追加や削除など) を実行するか、ディスク プールを削除して再デプロイします。

"**実行中**" とは、リソースが実行中であり、正常な状態であることを意味します。

"**更新中**" とは、リソースが更新中であることを意味します。 通常、これは、デプロイ時、またはディスクや LUN の追加などの更新プログラムの適用時に発生します。

"**停止 (割り当て解除)** " とは、リソースが停止しており、その基礎となるリソースの割り当てが解除されていることを意味します。 リソースを再起動して、ディスク プールまたは iSCSI ターゲットを復旧することができます。

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>ディスク プールをデプロイするときの一般的なエラー コード
 
|コード  |説明  |
|---------|---------|
|UnexpectedError     |通常、バックエンドで回復不能なエラーが発生した場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラー メッセージの追跡 ID を伝えてください。         |
|DeploymentFailureZonalAllocationFailed     |これは、指定された Azure のリージョンまたはゾーンに VM をプロビジョニングする容量が不足している場合に発生します。 別の時間にデプロイを再試行してください。         |
|DeploymentFailureQuotaExceeded     |ディスク プールのデプロイに使用されたサブスクリプションは、このリージョンの VM コア クォータを超えています。 Dsv3 シリーズの [Azure VM シリーズあたりの vCPU クォータ制限の引き上げを依頼する](../azure-portal/supportability/per-vm-quota-requests.md)ことができます。         |
|DeploymentFailurePolicyViolation     |サブスクリプションのポリシーにより、ディスク プールをサポートするために必要な Azure リソースをデプロイできませんでした。 詳細については、エラーを参照してください。         |
|DeploymentTimeout     |ディスク プール インフラストラクチャのデプロイがスタックし、割り当てられた時間内に完了しなかった場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラー メッセージの追跡 ID を伝えてください。         |
|GoalStateApplicationTimeoutError     |ディスク プール インフラストラクチャがリソース プロバイダーに応答しなくなったときに発生します。 [ネットワークの前提条件](disks-pools-deploy.md#prerequisites)を満たしていることを確認してから、デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラーの追跡 ID を伝えてください。         |
|OngoingOperationInProgress     |ディスク プールで操作が進行中です。 その操作が完了するまで待ってから、デプロイを再試行してください。         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>ディスク プールで iSCSI を有効にするときの一般的なエラー コード

|コード  |説明  |
|---------|---------|
|GoalStateApplicationError     |iSCSI ターゲット構成が無効であり、ディスク プールに適用できない場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラーの追跡 ID を伝えてください。         |
|GoalStateApplicationTimeoutError     |ディスク プール インフラストラクチャがリソース プロバイダーに応答しなくなったときに発生します。 [ネットワークの前提条件](disks-pools-deploy.md#prerequisites)を満たしていることを確認してから、デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラーの追跡 ID を伝えてください。         |
|OngoingOperationInProgress     |ディスク プールで操作が進行中です。 その操作が完了するまで待ってから、デプロイを再試行してください。         |

## <a name="next-steps"></a>次のステップ

[ディスク プール (プレビュー) を管理する](disks-pools-manage.md)
