---
title: Azure Monitor for VMs のゲストの正常性 (プレビュー) をトラブルシューティングする
description: Azure Monitor for VMs の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 2aa83a861697882127c75eb696807f604c3061f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184554"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Azure Monitor for VMs のゲストの正常性 (プレビュー) をトラブルシューティングする
この記事では、Azure Monitor for VMs の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。

## <a name="error-message-that-no-data-is-available"></a>使用できるデータがないというエラー メッセージ 

![データなし](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>仮想マシンが構成要件を満たしていることを確認する

1. 仮想マシンが Azure 仮想マシンであることを確認します。 Azure Arc for servers は現在サポートされていません。
2. 仮想マシンで、[サポートされているオペレーティング システム](vminsights-health-enable.md?current-limitations.md)が実行中であることを確認します。
3. 仮想マシンが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。
4. Log Analytics ワークスペースが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。

### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM が適切にオンボードされていることを確認する
Azure Monitor エージェント拡張機能とゲスト VM 正常性エージェントが、仮想マシンに正常にプロビジョニングされていることを確認します。 Azure portal で、仮想マシンのメニューから **[拡張機能]** を選択します。 一覧に 2 つのエージェントが示されている場合は、「[]()」を参照してください。

![VM 拡張機能](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>仮想マシンでシステム割り当て ID が有効になっていることを確認する
仮想マシンでシステム割り当て ID が有効になっていることを確認します。 Azure portal で、仮想マシンのメニューから **[ID]** を選択します。 一覧に 2 つのエージェントが示されている場合は、「[]()」を参照してください。

![システム割り当て ID](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>データ収集ルールを確認する
データ ソースとして正常性拡張機能を指定するデータ収集ルールが、仮想マシンに関連付けられていることを確認します。

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>アクセス許可が十分でないため無効な要求であることを示すエラー メッセージ
このエラーは、**Microsoft.WorkloadMonitor** リソース プロバイダーがサブスクリプションに登録されなかったことを示しています。 このリソース プロバイダーの登録の詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」を参照してください。 

![正しくない要求](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>次のステップ

- [Azure Monitor for VMs のゲストの正常性機能の概要を確認します](vminsights-health-overview.md)