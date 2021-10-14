---
title: Azure Automation で Python 3 パッケージを管理する
description: この記事では、Azure Automation での Python 3 パッケージ (プレビュー) の管理方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 08/25/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: d3ec338b6d6edac2c56c8b42f877a1095aace2a0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349616"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation で Python 3 パッケージ (プレビュー) を管理する

Azure Automation を使用すると、Azure Sandbox 環境および Linux Hybrid Runbook Worker で Python 3 Runbook (プレビュー) を実行することができます。 Runbook の簡略化のために、Python パッケージを使用して必要なモジュールをインポートすることができます。 Azure パッケージ 4.0.0 が Python 3 Automation 環境に既定でインストールされます。 Automation アカウントに Python パッケージをインポートすることで、既定のバージョンをオーバーライドできます。 Automation アカウントにインポートされたバージョンが優先されます。 1 つのパッケージをインポートするには、「[パッケージのインポート](#import-a-package)」を参照してください。 複数のパッケージを含むパッケージをインポートするには、「[依存関係を含むパッケージのインポート](#import-a-package-with-dependencies)」を参照してください。 この記事では、Azure Automation での Python 3 パッケージ (プレビュー) の管理および使用方法について説明します。

## <a name="packages-as-source-files"></a>ソース ファイルとしてのパッケージ

Azure Automation では、Python コードのみを含み、他の言語拡張や他の言語のコードを含まない Python パッケージのみがサポートされます。 ただし、Azure Sandbox 環境には C/C++ バイナリに必要なコンパイラがない場合があるため、代わりに[ホイール ファイル](https://pythonwheels.com/)を使用することが推奨されます。 [Python Package Index](https://pypi.org/) (PyPI) は、Python プログラミング言語用のソフトウェアのリポジトリです。 PyPI から Automation アカウントにインポートするために Python 3 パッケージを選択する場合は、次のファイル名の部分に注意してください。

| ファイル名の部分 | 説明 |
|---|---|
|cp38|Automation では、クラウド ジョブ用に、**Python 3.8** がサポートされています。|
|amd64|Azure サンドボックス プロセスは **Windows 64 ビット** アーキテクチャです。|

たとえば、pandas をインポートする場合は、`pandas-1.2.3-cp38-win_amd64.whl` のような名前のホイール ファイルを選択できます。

PyPI で使用できる一部の Python パッケージでは、ホイール ファイルが提供されていないものもあります。 この場合は、ソース (.zip または .tar.gz ファイル) をダウンロードし、`pip` を使用してホイール ファイルを生成します。 たとえば、Python 3.8. x とホイール パッケージがインストールされている 64 ビット コンピューターを使用して、次の手順を実行します。

1. ソースファイル `pandas-1.2.4.tar.gz` をダウンロードします。
1. pip を実行して、`pip wheel --no-deps pandas-1.2.4.tar.gz` コマンドでホイール ファイルを取得します。

## <a name="import-a-package"></a>パッケージのインポート

Automation アカウントの **[共有リソース]** で **[Python パッケージ]** を選択します。 次に **[+ Python パッケージの追加]** を選択します。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="左側のメニューに Python 3 パッケージが表示され、[Python 2 パッケージを追加する] が強調表示されている [Python 3 パッケージ] ページのスクリーンショット。":::

**[Python パッケージの追加]** ページで、**バージョン** として **Python 3** を選択し、アップロードするローカル パッケージを選択します。 パッケージは、 **.whl** または **.tar.gz** ファイルの場合があります。 パッケージを選択したら、 **[OK]** を選択してアップロードします。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="アップロードされた tar.gz ファイルが選択されている [Python 3 パッケージの追加] ページを示すスクリーンショット。":::

パッケージがインポートされると、Automation アカウントの [Python パッケージ] ページの **[Python 3 パッケージ (プレビュー)]** タブの下に一覧表示されます。パッケージを削除する必要がある場合は、パッケージを選択し、 **[削除]** を選択します。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="パッケージがインポートされた後の [Python 3 パッケージ] ページを示すスクリーンショット。":::

### <a name="import-a-package-with-dependencies"></a>依存関係を含むパッケージのインポート

Python 3 のパッケージとその依存関係をインポートするには、次の Python スクリプトを Python 3 Runbook にインポートしてから実行します。

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>スクリプトを Runbook にインポートする
Runbook のインポートの詳細については、「[Azure portal から Runbook をインポートする](manage-runbooks.md#import-a-runbook-from-the-azure-portal)」を参照してください。 GitHub から、インポートを実行する前にポータルからアクセスできるストレージにファイルをコピーします。

**[Runbook のインポート]** ページでは、スクリプトの名前に合わせて Runbook 名が既定で設定されます。 フィールドにアクセスできる場合は、名前を変更できます。 **[Runbook の種類]** は、既定で **[Python 2]** になっている場合があります。 その場合は、必ず **[Python 3]** に変更してください。

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Python 3 Runbook のインポート ページを示しているスクリーンショット。":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Runbook を実行してパッケージと依存関係をインポートする

Runbook を作成して発行したら、実行してパッケージをインポートします。 Runbook の実行の詳細については、「[Azure Automation で Runbook を開始する](start-runbooks.md)」を参照してください。

スクリプト (`import_py3package_from_pypi.py`) には、次のパラメーターが必要です。

| パラメーター | 説明 |
|---------------|-----------------|
|subscription_id | Automation アカウントのサブスクリプション ID |
| resource_group | Automation アカウントが定義されているリソース グループの名前 |
| automation_account | Automation アカウント名 |
| module_name | `pypi.org` からインポートするモジュールの名前 |

Runbook でのパラメーターの使用の詳細については、「[Runbook のパラメーターを使用する](start-runbooks.md#work-with-runbook-parameters)」を参照してください。

## <a name="use-a-package-in-a-runbook"></a>Runbook でパッケージを使用する

パッケージがインポートされたら、それを Runbook で使用できます。 Azure サブスクリプションのすべてのリソース グループを一覧表示するには、次のコードを追加します。

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

> [!NOTE]
> Python `automationassets` パッケージは、pypi.org で使用できないので、Windows コンピューターにインポートできません。

## <a name="identify-available-packages-in-sandbox"></a>サンドボックスで使用可能なパッケージを特定する

次のコードを使用して、既定のインストール済みモジュールのリストを表示します。

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

## <a name="next-steps"></a>次のステップ

Python Runbook を準備するには、「[Python Runbook を作成する](learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。
