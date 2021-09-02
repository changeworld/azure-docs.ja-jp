---
title: Azure ディスク プール (プレビュー) のトラブルシューティングの概要
description: Azure ディスク プールに関する問題のトラブルシューティング。 一般的なエラー コードとその解決方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6dc1b5ae3e885231a0d80ade2b8477a66e85d62d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456242"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Azure ディスク プール (プレビュー) のトラブルシューティング

この記事では、Azure ディスク プール (プレビュー) に関連する一般的なエラー コードの一部を紹介します。 また、ディスク プールの状態に関して考えられる解決方法をわかりやすく説明します。

## <a name="disk-pool-status"></a>ディスク プールの状態

ディスク プールと iSCSI ターゲットには、それぞれ "**不明**"、"**実行中**"、"**更新中**"、"**停止済み (割り当て解除)** " という 4 つの状態があります。

"**不明**" とは、リソースの状態が正しくない、または不明であることを意味します。 復旧を試みるには、リソースに対して更新操作 (ディスクまたは LUNS の追加や削除など) を実行するか、ディスク プールを削除して再デプロイします。

"**実行中**" とは、リソースが実行中であり、正常な状態であることを意味します。

"**更新中**" とは、リソースが更新中であることを意味します。 通常、これは、デプロイ時、またはディスクや LUN の追加などの更新プログラムの適用時に発生します。

"**停止 (割り当て解除)** " とは、リソースが停止しており、その基礎となるリソースの割り当てが解除されていることを意味します。 リソースを再起動して、ディスク プールまたは iSCSI ターゲットを復旧することができます。

## <a name="recover-a-disk-pool-or-an-iscsi-target"></a>ディスク プールまたは iSCSI ターゲットを復旧する

まず、ディスク プールを停止し、再起動します。 次に、ディスク プールと iSCSI ターゲットの状態を確認します。 それらが復旧した場合、ディスク プールにアクセスできない状態が 24 時間を超えている場合を除き、ディスク プールに接続されている Azure VMware クラスターは自動的に復旧します。 24 時間を超える時間が経過した場合は、Azure サポートに連絡して、ディスク プールに関連付けられているアクセスできないデータストアを強制的に切断する必要があります。 その後は、VS クラスターをディスク プールに再接続し、データストアを構成することができます。

このプロセスの後もディスク プールが復旧しない場合は、Azure サポートに連絡し、受け取ったエラー メッセージの追跡 ID を伝えてください。

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>ディスク プールをデプロイするときの一般的なエラー コード
 
|コード  |説明  |
|---------|---------|
|UnexpectedError     |通常、バックエンドで回復不能なエラーが発生した場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラー メッセージの追跡 ID を伝えてください。         |
|DeploymentFailureZonalAllocationFailed     |これは、指定された Azure のリージョンまたはゾーンに VM をプロビジョニングする容量が不足している場合に発生します。 別の時間にデプロイを再試行してください。         |
|DeploymentFailureQuotaExceeded     |ディスク プールのデプロイに使用されたサブスクリプションは、このリージョンの VM コア クォータを超えています。 Dsv3 シリーズの [Azure VM シリーズあたりの vCPU クォータ制限の引き上げを依頼する](../azure-portal/supportability/per-vm-quota-requests.md)ことができます。         |
|DeploymentFailurePolicyViolation     |サブスクリプションのポリシーにより、ディスク プールをサポートするために必要な Azure リソースをデプロイできませんでした。 詳細については、エラーを参照してください。         |
|DeploymentTimeout     |ディスク プール インフラストラクチャのデプロイがスタックし、割り当てられた時間内に完了しなかった場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラー メッセージの追跡 ID を伝えてください。         |
|OngoingOperationInProgress     |ディスク プールで操作が進行中です。 その操作が完了するまで待ってから、デプロイを再試行してください。         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>ディスク プールで iSCSI を有効にするときの一般的なエラー コード

|コード  |説明  |
|---------|---------|
|GoalStateApplicationError     |iSCSI ターゲット構成が無効であり、ディスク プールに適用できない場合に発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラーの追跡 ID を伝えてください。         |
|GoalStateApplicationTimeoutError     |ディスク プール インフラストラクチャがリソース プロバイダーに応答しなくなったときに発生します。 デプロイを再試行してください。 問題が解決しない場合は、Azure サポートに連絡し、エラーの追跡 ID を伝えてください。         |
|OngoingOperationInProgress     |ディスク プールで操作が進行中です。 その操作が完了するまで待ってから、デプロイを再試行してください。         |

## <a name="next-steps"></a>次のステップ

[ディスク プール (プレビュー) を管理する](disks-pools-manage.md)