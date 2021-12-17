---
title: Azure Automanage の一般的なオンボード エラーのトラブルシューティング
description: Automanage の一般的なオンボード エラーとそれらをトラブルシューティングする方法
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 95d2f429c1ecb308d65aa01be0a6f3fa226b6168
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458171"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Automanage の一般的なオンボード エラーのトラブルシューティング
Automanage は、サービスへのマシンのオンボードに失敗する場合があります。 このドキュメントでは、デプロイの失敗をトラブルシューティングする方法について説明し、デプロイが失敗する可能性があるいくつかの一般的な理由を示し、軽減策として考えられる次の手順について説明します。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング
マシンを Automanage にオンボードすると、Azure Resource Manager のデプロイが作成されます。 詳細については、デプロイで失敗した理由の詳細を確認します。 下の図に示すように、失敗の詳細ポップアップにデプロイへのリンクがあります。

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Automanage の失敗の詳細ポップアップ。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-machine"></a>失敗したマシンを含むリソース グループのデプロイを確認する
失敗時のポップアップには、オンボードに失敗したマシンを含むリソース グループ内のデプロイへのリンクが含まれます。 ポップアップには、デプロイのフィルター処理に使用できるプレフィックス名も含まれています。 デプロイのリンクをクリックすると、デプロイ ブレードが表示され、そこでデプロイをフィルター処理して、お使いのマシンへの Automanage デプロイを表示することができます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-machine"></a>失敗したマシンを含むサブスクリプションのデプロイを確認する
リソース グループのデプロイにエラーが表示されない場合は、次の手順として、オンボードに失敗したマシンを含むサブスクリプションのデプロイを確認します。 失敗のポップアップの **[Deployments for subscription]\(サブスクリプションのデプロイ\)** リンクをクリックし、**Automanage-DefaultResourceGroup** フィルターを使用して、デプロイをフィルター処理します。 失敗ブレードのリソース グループ名を使用して、デプロイをフィルター処理します。 デプロイ名の末尾には、リージョン名が付けられます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースにリンクされているサブスクリプションのデプロイを確認する
失敗したマシンを含むリソース グループまたはサブスクリプションに失敗したデプロイが見つからない場合、および失敗したマシンが別のサブスクリプションの Log Analytics ワークスペースに接続されている場合は、Log Analytics ワークスペースにリンクされているサブスクリプションにアクセスして、失敗したデプロイがないか確認します。

## <a name="common-deployment-errors"></a>一般的なデプロイのエラー

エラー |  対応策
:-----|:-------------|
Automanage アカウントのアクセス許可が不十分なエラー | このエラーは、新しい Automanage アカウントを含むサブスクリプションを新しいテナントに最近移動した場合に起こる可能性があります。 このエラーを解決する手順については、[こちら](./repair-automanage-account.md)を参照してください。
ワークスペース リージョンがリージョン マッピング要件に一致しない | マシンが現在リンクされている Log Analytics ワークスペースが、サポートされる Automation リージョンにマップされていないため、Automanage がマシンをオンボードできませんでした。 既存の Log Analytics ワークスペースと Automation アカウントが、[サポートされているリージョン マッピング](../automation/how-to/region-mappings.md)に配置されていることを確認します。
"'System deny assignment created by managed application' (マネージド アプリケーションによって作成されたシステム拒否割り当て) という名前の拒否割り当てによりアクセスが拒否されました" | リソースに対して [denyAssignment](../role-based-access-control/deny-assignments.md) が作成されたため、Automanage がリソースへアクセスできませんでした。 この denyAssignment は、[ブループリント](../governance/blueprints/concepts/resource-locking.md)または[マネージド アプリケーション](../azure-resource-manager/managed-applications/overview.md)のいずれかによって作成された可能性があります。
"OS Information: Name='(null)', ver='(null)', agent status='Not Ready'. (OS 情報: 名前 ='(null)'、バージョン ='(null)'、エージェントの状態 ='準備中'。)" | [サポートされる最小エージェント バージョン](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)が実行されていること、エージェントが実行されていること ([linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) と [windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent))、およびエージェントが最新の状態であること ([linux](../virtual-machines/extensions/update-linux-agent.md) と [windows](../virtual-machines/extensions/agent-windows.md)) を確認します。
"Unable to determine the OS for the VM OS Name:, ver . (VM OS 名前:、バージョン の OS を特定できません。) Please check that the VM Agent is running, the current status is Ready. (VM エージェントが実行中であること、現在の状態が [準備完了] であることを確認してください。)" | [サポートされる最小エージェント バージョン](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)が実行されていること、エージェントが実行されていること ([linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) と [windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent))、およびエージェントが最新の状態であること ([linux](../virtual-machines/extensions/update-linux-agent.md) と [windows](../virtual-machines/extensions/agent-windows.md)) を確認します。
"VM has reported a failure when processing extension 'IaaSAntimalware' (拡張機能 'IaaSAntimalware' の処理中に VM によってエラーがレポートされました)" | VM に別のマルウェア対策またはウイルス対策オファリングが既にインストールされていないことを確認します。 それでも解決しない場合は、サポートにご連絡ください。
ASC ワークスペース: Automanage は現在、"_この場所_" では Log Analytics サービスをサポートいません。 | [サポートされているリージョン](./automanage-virtual-machines.md#supported-regions)に VM があることを確認してください。
ポリシー違反に起因してテンプレートのデプロイが失敗しました。 詳細をご覧ください。 | Automanage への VM のオンボードを妨げるポリシーがあります。 Automanage にオンボードする VM が含まれているサブスクリプションまたはリソース グループに適用されているポリシーを確認します。
"割り当てに失敗しました。入手できる情報は他にありません。" | Microsoft Azure サポートのケースを開いてください。

## <a name="next-steps"></a>次のステップ

* [Azure Automanage の詳細情報](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for machines を有効にする](quick-create-virtual-machines-portal.md)