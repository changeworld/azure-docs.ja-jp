---
title: SQL Insights 用の Resource Manager テンプレートのサンプル
description: SQL Insights をデプロイおよび構成するための Azure Resource Manager テンプレートのサンプル。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: c82a46fa249bd55207e3fc20fbe991c0d7d24bf9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958152"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>SQL Insights 用の Resource Manager テンプレートのサンプル
この記事には、Azure で実行されている SQL を監視できるように SQL Insights を有効にするためのサンプル [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/syntax.md)が含まれています。  サポートされている SQL のオファリングとバージョンの詳細については、[SQL Insights のドキュメント](sql-insights-overview.md)を参照してください。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>SQL Insights 監視プロファイルを作成する
次のサンプルでは、SQL Insights 監視プロファイルを作成します。このプロファイルには、収集する SQL 監視データやデータ収集の頻度が含まれます。また、データの送信先となるワークスペースも指定します。


### <a name="template-file"></a>テンプレート ファイル

[GitHub でテンプレート ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)を確認します。

### <a name="parameter-file"></a>パラメーター ファイル

[GitHub でパラメーター ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)を確認します。


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>SQL Insights 監視プロファイルに監視 VM を追加する
監視プロファイルを作成したら、Azure 仮想マシンを割り当てる必要があります。この VM はその構成で指定した SQL リソースからリモートでデータを収集するように構成します。  詳細については、SQL Insights の有効化に関するドキュメントを参照してください。

次のサンプルでは、指定された SQL リソースからデータを収集するように監視 VM を構成します。


### <a name="template-file"></a>テンプレート ファイル

[GitHub でテンプレート ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)を確認します。

### <a name="parameter-file"></a>パラメーター ファイル

[GitHub でパラメーター ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)を確認します。


## <a name="create-an-alert-rule-for-sql-insights"></a>SQL Insights のアラート ルールを作成する
次の例では、指定された監視プロファイルのスコープ内にある SQL リソースを対象にアラート ルールを作成します。  このアラート ルールは、アラート UI コンテキスト パネルの SQL Insights UI に表示されます。  

パラメーター ファイルには、Microsoft が SQL Insights で提供するいずれかのアラート テンプレートの値が含まれています。これを変更して、SQL 用に収集した他のデータについてアラートを通知することができます。  テンプレートでは、アラート ルールに対応したアクション グループは指定されていません。


#### <a name="template-file"></a>テンプレート ファイル

[GitHub でテンプレート ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)を確認します。

### <a name="parameter-file"></a>パラメーター ファイル

[GitHub でパラメーター ファイル](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)を確認します。





## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](../resource-manager-samples.md)。
* [SQL Insights の詳細情報](sql-insights-overview.md)。