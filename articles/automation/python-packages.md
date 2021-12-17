---
title: Azure Automation で Python 2 パッケージを管理する
description: この記事では、Azure Automation での Python 2 パッケージの管理方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 302ed99a2032200d770f371d24388c92a28a97e6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470813"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Azure Automation で Python 2 パッケージを管理する

この記事では、Azure サンドボックス環境と Hybrid Runbook Worker で実行されている Azure Automation で Python 2 パッケージをインポート、管理、使用する方法について説明します。 Runbook を簡略化するために、Python パッケージを使用して必要なモジュールをインポートすることができます。

Python 3 パッケージの管理については、「[Azure Automation で Python 3 パッケージを管理する](./python-3-packages.md)」をご覧ください。

## <a name="import-packages"></a>パッケージをインポートする

1. Automation アカウントの **[共有リソース]** で **[Python パッケージ]** を選択します。 **[+ Python パッケージの追加]** をクリックします。

    :::image type="content" source="media/python-packages/add-python-package.png" alt-text="[Python パッケージ] ページのスクリーンショット。左側のメニューに [Python パッケージ] が表示され、[Python パッケージの追加] が強調表示されています。":::

2. **[Python パッケージの追加]** ページで、アップロードするローカル パッケージを選択します。 パッケージは、 **.whl** または **.tar.gz** ファイルの場合があります。 
3. 名前を入力し、 **[ランタイム バージョン]** として 2.x.x を選択します
4. **[インポート]** を選択します。

   :::image type="content" source="media/python-packages/upload-package.png" alt-text="アップロードされた tar.gz ファイルが選択されている [Python パッケージの追加] ページを示すスクリーンショット。":::

パッケージがインポートされた後、そのパッケージはお使いの Automation アカウントの **[Python パッケージ]** ページに一覧表示されます。 パッケージを削除するには、パッケージを選択し、 **[削除]** をクリックします。

:::image type="content" source="media/python-packages/package-list.png" alt-text="パッケージがインポートされた後の [Python 2.7.x パッケージ] ページを示すスクリーンショット。":::

## <a name="import-packages-with-dependencies"></a>依存関係があるパッケージをインポートする

Azure Automation ではインポート プロセス中に Python パッケージの依存関係を解決しません。 すべての依存関係を含め、パッケージをインポートする方法は 2 つあります。 Automation アカウントにパッケージをインポートするには、次の手順の 1 つのみを使用する必要があります。

### <a name="manually-download"></a>手動ダウンロード

[Python2.7](https://www.python.org/downloads/release/latest/python2) および [pip](https://pip.pypa.io/en/stable/) がインストールされている Windows 64 ビット マシン上で、次のコマンドを実行してパッケージとそのすべての依存関係をダウンロードします。

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

パッケージがダウンロードされたら、それらをAutomation アカウントにインポートできます。

### <a name="runbook"></a>Runbook

 Runbook を取得するには、Azure Automation GitHub 組織から Automation アカウント、[pypi から Azure Automation アカウントに Python 2 パッケージをインポート](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account)します。 [実行設定] が **[Azure]** に設定されていることを確認し、パラメーターを指定して Runbook を開始します。 Automation アカウントが機能するためには、Runbook に実行アカウントが必要です。 各パラメーターについて、次の一覧と画像に示すように、必ずスイッチを指定して開始してください。

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="右側に [Runbook を開始する] ウィンドウが表示されている import_py2package_from_pypi の [概要] ページを示すスクリーンショット。":::

Runbook では、ダウンロードするパッケージを指定できます。 たとえば、`Azure` パラメーターを使用すると、すべての Azure モジュールとすべての依存関係 (約 105 個) がダウンロードされます。

Runbook が完了した後、Automation アカウントの **[共有リソース]** の下にある **[Python パッケージ]** を調べれば、パッケージが正しくインポートされていることを確認できます。

## <a name="use-a-package-in-a-runbook"></a>Runbook でパッケージを使用する

パッケージがインポートされたら、それを Runbook で使用できます。 次の例では、[Azure Automation ユーティリティ パッケージ](https://github.com/azureautomation/azure_automation_utility)を使用します。 このパッケージにより、Azure Automation で Python が使いやすくなります。 パッケージを使用するには、GitHub リポジトリの指示に従って、Runbook に追加します。 たとえば、`from azure_automation_utility import get_automation_runas_credential` を使用して、実行アカウントを取得するための関数をインポートできます。

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

> [!NOTE]
> Python `automationassets` パッケージは、pypi.org で使用できないので、Windows コンピューターにインポートできません。

## <a name="develop-and-test-runbooks-offline"></a>Runbook をオフラインで開発してテストする

Python 2 Runbook をオフラインで開発してテストするには、GitHub の [Azure Automation Python のエミュレートされた資産](https://github.com/azureautomation/python_emulated_assets)モジュールを使用できます。 このモジュールを使用すると、資格情報、変数、接続、および証明書などの共有リソースを参照できます。

## <a name="next-steps"></a>次のステップ

Python Runbook を準備するには、「[Python Runbook を作成する](./learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。