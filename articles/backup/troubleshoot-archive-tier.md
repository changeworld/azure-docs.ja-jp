---
title: アーカイブ層のトラブルシューティング
description: Azure Backup のアーカイブ層エラーのトラブルシューティングについて説明します。
ms.topic: troubleshooting
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 560c8900e5d0c06dc436ec1222583db58f7cdda3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089970"
---
# <a name="troubleshooting-recovery-point-archive-using-archive-tier"></a>アーカイブ層を使用した回復ポイントのアーカイブのトラブルシューティング

この記事では、回復ポイントをアーカイブに移動できない場合に表示されるエラーコードのトラブルシューティングの詳細について説明します。

## <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**エラーメッセージ**: Recovery-Point の種類はアーカイブの移動に適合していません

**説明**: このエラーコードは、選択した回復ポイントの種類をアーカイブに移動する資格がない場合に表示されます。

**推奨される操作**: 回復ポイントの資格を確認します。 [サポートされているワークロード](archive-tier-support.md#supported-workloads)をご覧ください。

## <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**エラーメッセージ**: 復元に対するアクティブな依存関係がある Recovery-Point は、アーカイブの移動に適合していません

**説明 –** 選択したリカバリーポイントにはアクティブな依存関係があるため、アーカイブに移動できません。

**推奨される操作**"回復ポイントの有効性を確認します。 [サポートされているワークロード](archive-tier-support.md#supported-workloads)をご覧ください。

## <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**エラーメッセージ**: 資格情報コンテナーで使用された有効期間が Standard レベルより低いため、Recovery-Point はアーカイブ移動の対象になりません

**説明**: 復旧ポイントは、azure 仮想マシンの場合は3か月以上、azure 仮想マシンの場合は 45 SQL Server 日で、Standard レベルのままにする必要があります

**推奨される操作**: 回復ポイントの資格を確認します。 [サポートされているワークロード](archive-tier-support.md#supported-workloads)をご覧ください。

## <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**エラーメッセージ**: Recovery-Point 残存有効期間が、必要な最小値を下回っています。

**説明**: アーカイブの移動資格の回復ポイントに必要な最小有効期間は6か月です。

**推奨される操作**: 回復ポイントの資格を確認します。 [サポートされているワークロード](archive-tier-support.md#supported-workloads)をご覧ください。

## <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**エラーメッセージ**: Recovery-Point は既にアーカイブ層に移動されているため、アーカイブ移動の対象になりません

**説明**: 選択された回復ポイントは既にアーカイブに含まれています。 そのため、アーカイブに移動する資格がありません。

## <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**エラーメッセージ**: Datasource の種類は、推奨設定 API に適合していません。

**説明**: 推奨 API は、Azure 仮想マシンにのみ適用されます。 選択したデータソースの種類には適用されません。

## <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**エラーメッセージ**: 回復ポイントは既に復元されています。 リハイドレートがこの RP では許可されていません。

**説明**: 選択された回復ポイントは既に復元されています。

## <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**エラーメッセージ**: Recovery-Point はアーカイブの移動に適合していません。

**説明**: 選択された回復ポイントはアーカイブ移動の対象になりません。

## <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**エラーメッセージ**: アーカイブの回復ポイントが復元されていません。 アーカイブ RP でリハイドレートの完了後に復元を再試行してください。

**説明**: 回復ポイントが復元されていません。 復旧ポイントをリハイドレートした後で復元をお試しください。

## <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**エラーメッセージ**: 復元はアーカイブ回復ポイントでのみサポートされています-復元はアーカイブ回復ポイントでのみサポートされています

**説明** – 選択した回復ポイントではリハイドレートはできません。

## <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**エラーメッセージ**: アーカイブの回復ポイントでは、復元が既に In-Progress ています。

**説明**: 選択された回復ポイントの復元が既に進行中です。

## <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**エラー メッセージ** - ポリシーで指定された保持期間が十分でないため、復旧ポイントをアーカイブ層に移動できません

**推奨される操作**: 保護された項目のポリシーを適切な保有期間の設定で更新して、もう一度やり直してください。

## <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**エラーメッセージ**: この回復ポイントを移動できるかどうかを確認しています。

**説明**: 回復ポイントの移動準備はまだ確定されていません。

**推奨される操作**: しばらく待ってからもう一度確認してください。
