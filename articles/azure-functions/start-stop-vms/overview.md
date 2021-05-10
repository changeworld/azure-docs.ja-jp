---
title: Start/Stop VMs v2 (プレビュー) の概要
description: この記事では、スケジュールに従って Azure Resource Manager とクラシックの VM を開始または停止する、Start/Stop VMs (プレビュー) 機能のバージョン 2 について説明します。
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: 44bfbaa8b18ebeab3b74bc696a16fc4cfb6c08ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220936"
---
# <a name="startstop-vms-v2-preview-overview"></a>Start/Stop VMs v2 (プレビュー) の概要

Start/Stop VMs v2 (プレビュー) 機能は、複数のサブスクリプションにわたって Azure 仮想マシン (VM) を開始または停止します。 これは、ユーザー定義のスケジュールで Azure VM を開始または停止し、[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) を使用して分析情報を提供し、[アクション グループ](../../azure-monitor/alerts/action-groups.md)を使用してオプションの通知を送信します。 この機能を使用すると、ほとんどのシナリオで Azure Resource Manager VM とクラシック VM の両方を管理できます。

この新しいバージョンの Start/Stop VMs v2 (プレビュー) により、VM のコストを最適化する必要がある顧客向けに、分散型の低コストなオートメーション オプションが提供されます。 これは Azure Automation で使用できる[元のバージョン](../../automation/automation-solution-vm-management.md)と同じ機能をすべて提供しますが、より新しい Azure のテクノロジを活用するように設計されています。

## <a name="overview"></a>概要

Start/Stop VMs v2 (プレビュー) は再設計されており、[以前のバージョン](../../automation/automation-solution-vm-management.md)で必要な Azure Automation および Azure Monitor のログに依存していません。 このバージョンは、VM の開始および停止の実行を処理するために [Azure Functions](../../azure-functions/functions-overview.md) を利用します。

マネージド ID が、この Azure Functions アプリケーション用に Azure Active Directory (Azure AD) で作成されます。これにより、Start/Stop VMs v2 (プレビュー) がロジック アプリや Azure VM などの Azure AD で保護された他のリソースに簡単にアクセスできます。 Azure AD のマネージド ID について詳しくは、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。

次の表に示す HTTP トリガー エンドポイント関数が、この機能に含まれるスケジュールおよびシーケンスのシナリオをサポートするために作成されます。

|Name |トリガー |[説明] |
|-----|--------|------------|
|AlertAvailabilityTest |Timer |この関数は、可用性テストを実行して、プライマリ関数 **AutoStopVM** が常に使用可能であることを確認します。|
|AutoStop |HTTP |この関数では、ロジック アプリから呼び出されるエントリ ポイント関数である **AutoStop** シナリオがサポートされています。|
|AutoStopAvailabilityTest |Timer |この関数は、可用性テストを実行して、プライマリ関数 **AutoStop** が常に使用可能であることを確認します。|
|AutoStopVM |HTTP |この関数は、アラート条件が true である場合に VM アラートによって自動的にトリガーされます。|
|CreateAutoStopAlertExecutor |キュー |この関数は、**AutoStop** 関数からペイロード情報を取得して、VM に関するアラートを作成します。|
|スケジュール済み |HTTP |この関数が対象とするシナリオは、スケジュール設定とシーケンス設定の両方です (ペイロード スキーマによって区別されます)。 これは、ロジック アプリから呼び出されたエントリ ポイント関数であり、VM の開始または停止の操作を処理するペイロードを取得します。 |
|ScheduledAvailabilityTest |Timer |この関数は、可用性テストを実行して、プライマリ関数 **Scheduled** が常に使用可能であることを確認します。|
|VirtualMachineRequestExecutor |キュー |この関数は、VM で実際の開始および停止の操作を実行します。|
|VirtualMachineRequestOrchestrator |キュー |この関数は、**Scheduled** 関数からペイロード情報を取得し、VM の開始と停止の要求を調整します。|

たとえば、**Scheduled** HTTP トリガー関数は、スケジュールと シーケンスのシナリオを処理するために使用されます。 同様に、**AutoStop** HTTP トリガー関数によって、自動停止シナリオが処理されます。

この機能をサポートするには、キューベースのトリガー関数が必要です。 すべてのタイマーベースのトリガーは、可用性テストの実行と、システムの正常性の監視に使用されます。

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) は、JSON ペイロードを使用して関数を呼び出すことにより、VM の開始と停止のスケジュールを構成および管理するために使用されます。 これは既定で、最初のデプロイ中に、次のシナリオ向けに合計 5 つの Logic Apps を作成します。

- Scheduled - 開始と停止のアクションは、Azure Resource Manager とクラシックの VM に対して指定したスケジュールに基づきます。 **ststv2_vms_Scheduled_start** と **ststv2_vms_Scheduled_stop** により、スケジュール設定された開始と停止が構成されます。

- Sequenced - 開始と停止のアクションは、事前定義されたシーケンス処理タグの付いた VM を対象とするスケジュールに基づきます。 サポートされているのは、**sequencestart** および **sequencestop** の 2 つの名前付きタグのみです。 **ststv2_vms_Sequenced_start** と **ststv2_vms_Sequenced_stop** により、シーケンスされた開始と停止が構成されます。

    > [!NOTE]
    > このシナリオでサポートされるのは、Azure Resource Manager の VM のみです。

- AutoStop - この機能は、CPU 使用率に基づいて Azure Resource Manager とクラシックの両方の VM に対して停止アクションを実行するためにのみ使用されます。 また、これは、スケジュールに基づいた "*アクションの実行*" にもなり得ます。これにより、VM に関するアラートが作成され、条件に基づいて、停止アクションを実行するためにアラートがトリガーされます。 **ststv2_vms_AutoStop** により、自動停止機能が構成されます。

開始と停止の各アクションでは、1 つ以上のサブスクリプション、リソース グループ、または VM の一覧の割り当てがサポートされています。

Functions で必要とされる Azure Storage アカウントも、次の 2 つの目的で Start/Stop VMs (プレビュー) によって使用されます。

   - Azure Table Storage を使用して、実行操作のメタデータ (つまり、VM の開始または停止アクション) を格納する。

   - Azure Queue Storage を使用して、Azure Functions キューベースのトリガーをサポートする。

関数アプリの実行からのトレース ログであるすべての利用統計情報は、接続されている Application Insights インスタンスに送信されます。 Application Insights に格納されている利用統計情報は、共有 [Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)に表示される事前定義済みの視覚化のセットから表示できます。

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Start/Stop VMs 共有状態ダッシュボード":::

また、VM で実行されたアクションの結果として電子メール通知が送信されます。

## <a name="new-releases"></a>新しいリリース

Start/Stop VMs v2 (プレビュー) の新しいバージョンがリリースされると、手動で再デプロイしなくてもインスタンスは自動更新されます。

## <a name="supported-scoping-options"></a>サポートされているスコープのオプション

### <a name="subscription"></a>サブスクリプション

サブスクリプションに対するスコープは、サブスクリプション全体のすべての VM で開始および停止アクションを実行する必要がある場合に使用できます。また、必要に応じて複数のサブスクリプションを選択できます。  

除外する VM の一覧を指定することもでき、それらはアクションから無視されます。 また、ワイルドカード文字を使用して、同時に無視できるすべての名前を指定することもできます。

### <a name="resource-group"></a>Resource group

リソース グループに対するスコープは、1 つ以上のサブスクリプションにわたって、1 つ以上のリソース グループ名を指定することで、そのすべての VM で開始および停止アクションを実行する必要がある場合に使用できます。

除外する VM の一覧を指定することもでき、それらはアクションから無視されます。 また、ワイルドカード文字を使用して、同時に無視できるすべての名前を指定することもできます。

### <a name="vmlist"></a>VMList

VM の一覧の指定は、複数のサブスクリプションにわたって、特定の仮想マシンのセットに対して開始および停止アクションを実行する必要がある場合に使用できます。 このオプションでは、除外する VM の一覧の指定はサポートされていません。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

- アカウントには、サブスクリプションで[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)のアクセス許可が付与しておきます。

- Start/Stop VMs v2 (プレビュー) は、Azure Functions の「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions)」ページに示されている Azure グローバルおよび米国政府機関のクラウドのリージョンすべてで利用できます。

## <a name="next-steps"></a>次の手順

この機能をデプロイするには、[Start/Stop VMs のデプロイ](deploy.md) (プレビュー) に関する記事を参照してください。