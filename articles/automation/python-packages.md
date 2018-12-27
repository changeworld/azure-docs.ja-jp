---
title: Azure Automation で Python 2 パッケージを管理する
description: この記事では、Azure Automation での Python 2 パッケージの管理方法について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5974a8e622ca0969b2a7b5ee9500766ac95398c9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987223"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation で Python 2 パッケージを管理する

Azure Automation では、Azure および Linux Hybrid Runbook Worker で Python 2 Runbook を実行することができます。 Runbook の簡略化のために、Python パッケージを使用して必要なモジュールをインポートすることができます。 この記事では、Azure Automation での Python パッケージの管理および使用方法について説明します。

## <a name="import-packages"></a>パッケージをインポートする

Automation アカウントの **[共有リソース]** で **[Python 2 packages]\(Python 2 パッケージ\)** を選択します。 **[+ Python 2 パッケージを追加する]** をクリックします。

![Python パッケージを追加する](media/python-packages/add-python-package.png)

**[Python 2 パッケージの追加]** ページで、アップロードするローカル パッケージを選択します。 パッケージは `.whl` ファイルまたは `.tar.gz` ファイルを指定できます。 選択したら、**[OK]** をクリックしてパッケージをアップロードします。

![Python パッケージを追加する](media/python-packages/upload-package.png)

パッケージがインポートされた後、Automation アカウントの **[Python 2 packages]\(Python 2 パッケージ\)** ページにリストされます。 パッケージを削除する必要がある場合は、そのパッケージを選択し、パッケージ ページの **[削除]** を選びます。

![パッケージ リスト](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Runbook でパッケージを使用する

パッケージをインポートしたら、Runbook で使用することができます。 次の例では、[Azure Automation ユーティリティ パッケージ](https://github.com/azureautomation/azure_automation_utility)を使用します。 このパッケージにより、Azure Automation で Python が使いやすくなります。 パッケージを使用するには、GitHub リポジトリの指示に従って、Runbook に追加します。その場合、`from azure_automation_utility import get_automation_runas_credential` などを使用して、RunAs アカウントを取得するための関数をインポートします。

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Runbook をオフラインで開発してテストする

Python 2 Runbook をオフラインで開発してテストするには、GitHub の [Azure Automation Python のエミュレートされた資産](https://github.com/azureautomation/python_emulated_assets)モジュールを使用できます。 このモジュールを使用すると、資格情報、変数、接続、および証明書などの共有リソースを参照できます。

## <a name="next-steps"></a>次の手順

Python 2 Runbook の使用を開始する場合は、[初めての Python2 Runbook](automation-first-runbook-textual-python2.md) に関するページを参照してください。