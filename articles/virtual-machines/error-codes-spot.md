---
title: Azure スポット VM とスケール セットのインスタンスのエラー コード
description: スポット VM とスケール セットのインスタンスを使用する場合に表示される可能性があるエラー コードについて説明します。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 1863c510967dcf80044d768aee1a37643a068ce4
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115857"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>プレビュー:スポット VM とスケール セットのエラー メッセージ


> [!IMPORTANT]
> スポット VM と仮想マシン スケール セットは、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


ここでは、スポット VM とスケール セットを使用する場合に受け取る可能性のあるエラー コードを示します。


| Key | Message | 説明 |
|-----|---------|-------------|
| SkuNotAvailable | リソース '\<resource\>' に要求された階層は、場所 '\<location\>' ではサブスクリプション '\<subscriptionID\>' に対して現在使用できません。 別の階層を試すか、別の場所にデプロイしてください。 | この場所には、VM またはスケール セットのインスタンスを作成するための十分な Azure スポットの容量がありません。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  排除ポリシーは、Azure Spot Virtual Machines のみで設定できます。 | この VM はスポット VM ではないため、排除ポリシーを設定することはできません。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure Spot Virtual Machine は、可用性セットではサポートされていません。 | スポット VM を使用するか、可用性セット内の VM を使用するかを選択する必要があります。両方を選択することはできません。 |
| AzureSpotFeatureNotEnabledForSubscription  |  Azure スポット機能でサブスクリプションが有効になっていません。 | スポット VM をサポートするサブスクリプションが必要です。 |
| VMPriorityCannotBeApplied  |  仮想マシンの作成時に優先順位が指定されていないため、指定された優先順位値 '{0}' を仮想マシン '{1}' に適用できません。 | VM の作成時に優先度を指定する必要があります。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  指定された最大価格 '{1} 米国ドル' が、Azure スポット VM サイズ '{3}' の現在のスポット価格 '{2} 米国ドル' より低いため、操作 '{0}' を実行できません。 | より高い最大価格を選択してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での料金情報を参照してください。|
| MaxPriceValueInvalid  |  最大価格の値が無効です。 最大価格でサポートされている値は、-1 または 0 より大きい小数のみです。 最大価格値 -1 は、Azure スポットの仮想マシンが価格を理由に排除されないことを示します。 | 有効な最大価格を入力してください。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) または [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) での価格を参照してください。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | VM '{0}' が現在割り当てられている場合、最大価格の変更は許可されません。 割り当てを解除してから、もう一度お試しください。 | VM を停止 / 割り当て解除して、最大価格を変更できるようにします。 |
| MaxPriceChangeNotAllowed | 最大価格の変更は許可されていません。 | この VM の最大価格を変更することはできません。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure スポットは、この API バージョンではサポートされていません。 | API バージョンは 2019-03-01 である必要があります。 |
| AzureSpotIsNotSupportedForThisVMSize  |  この VM サイズ {0} に対しては Azure スポットはサポートされていません。 | 別の VM サイズを選択してください。 詳細については、[Spot Virtual Machines](./linux/spot-vms.md) に関するページを参照してください。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  最大価格は、Azure Spot Virtual Machines のみでサポートされています。 | 詳細については、[Spot Virtual Machines](./linux/spot-vms.md) に関するページを参照してください。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  リソースの移動要求には、Azure スポットの仮想マシンが含まれています。 現在これはサポートされていません。 仮想マシン ID のエラーの詳細を確認してください。 | スポット VM を移動することはできません。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  リソースの移動要求には、Azure スポットの仮想マシン スケール セットが含まれています。 現在これはサポートされていません。 仮想マシン スケール セット ID のエラーの詳細を確認してください。 | スポット スケール セットのインスタンスを移動することはできません。 |
| EphemeralOSDisksNotSupportedForSpotVMs | 一時 OS ディスクは、スポット VM ではサポートされていません。 | スポット VM には、通常の OS ディスクを使用する必要があります。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | VM オーケストレーション モードの仮想マシン スケール セットでは、Azure Spot Virtual Machine はサポートされていません。 | スポット インスタンスを使用するには、オーケストレーション モードを仮想マシン スケール セットに設定します。 |


**次の手順** の詳細については、「[Spot Virtual Machines](./linux/spot-vms.md)」を参照してください。