---
title: 委任されたリソースを大規模に監視する
description: 管理下にある顧客テナント全体を対象に、スケーラブルな方法で効率よく Azure Monitor ログを使用する方法について説明します。
ms.date: 10/26/2020
ms.topic: how-to
ms.openlocfilehash: 3e5c98b3b62a8fbc953a29cf51ac527e5de21110
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735841"
---
# <a name="monitor-delegated-resources-at-scale"></a>委任されたリソースを大規模に監視する

サービス プロバイダーは、[Azure Lighthouse](../overview.md) に複数の顧客テナントをオンボードしている場合があります。 Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数のテナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

このトピックでは、管理下にある顧客テナント全体を対象に、スケーラブルな方法で [Azure Monitor ログ](../../azure-monitor/platform/data-platform-logs.md)を使用する方法について説明します。 このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

> [!NOTE]
> 管理テナントのユーザーには、委任された顧客サブスクリプションで [Log Analytics ワークスペースの管理に必要なロール](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions)が付与されていることを確認してください。

## <a name="create-log-analytics-workspaces"></a>Log Analytics ワークスペースの作成

データを収集するには、Log Analytics ワークスペースを作成する必要があります。 これらの Log Analytics ワークスペースは、Azure Monitor によって収集されたデータのための特別な環境です。 各ワークスペースには、独自のデータ リポジトリと構成があり、データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。

これらのワークスペースは、直接顧客テナントに作成することをお勧めします。 そうすることで、データを自分の環境にエクスポートするのではなく、各顧客のテナント内に留めることができます。 また、Log Analytics でサポートされるリソースまたはサービスの監視を一元化し、監視対象となるデータの種類について、より柔軟な運用が可能となります。

Log Analytics ワークスペースは、[Azure portal](../../azure-monitor/learn/quick-create-workspace.md)、[Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)、[Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) のいずれかを使用して作成できます。

> [!IMPORTANT]
> すべてのワークスペースが顧客テナントに作成されている場合でも、管理テナントのサブスクリプションには、Microsoft.Insights リソース プロバイダーも登録する必要があります。

## <a name="deploy-policies-that-log-data"></a>データのログ記録ポリシーをデプロイする

Log Analytics ワークスペースを作成したら、診断データが各テナントの適切なワークスペースに送信されるよう、顧客階層構造全体に [Azure Policy](../../governance/policy/index.yml) をデプロイできます。 実際にデプロイするポリシーは、監視したいリソースの種類によって異なる場合があります。

ポリシーの作成について詳しくは、「[チュートリアル: コンプライアンスを強制するポリシーの作成と管理」](../../governance/policy/tutorials/create-and-manage.md)を参照してください。 特定の種類のリソースを選んで監視するポリシーが簡単に作成できるスクリプトを[コミュニティ ツール](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator)を通じて入手できます。

デプロイするポリシーが決まったら、[委任されたサブスクリプションに対してそれらを大規模にデプロイする](policy-at-scale.md)ことができます。

## <a name="analyze-the-gathered-data"></a>生成されたデータを分析する

ポリシーをデプロイすると、各顧客テナントに作成した Log Analytics ワークスペースにデータがログされます。 管理下にある全顧客の分析情報を入手したければ、[Azure Monitor Workbooks](../../azure-monitor/platform/workbooks-overview.md) などのツールを使用して、複数のデータ ソースから情報を収集し、分析してください。 

## <a name="next-steps"></a>次のステップ

- 複数の Log Analytics ワークスペースに [Update Management ログを問い合わせる](../../automation/update-management/update-mgmt-query-logs.md)ことで、パッチ コンプライアンス レポートを追跡するこの [MVP ビルト サンプル ワークブック](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)を試してみます。 
- [Azure Monitor](../../azure-monitor/index.yml) について学習する。
- [Azure Monitor ログ](../../azure-monitor/platform/data-platform-logs.md)について学習する。
- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
