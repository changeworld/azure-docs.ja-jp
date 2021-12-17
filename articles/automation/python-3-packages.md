---
title: Azure Automation で Python 3 パッケージを管理する
description: この記事では、Azure Automation での Python 3 パッケージ (プレビュー) の管理方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 11/01/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: a33f4f9b6403d1f4aff88cadb57ae0f78f02038b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443753"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation で Python 3 パッケージ (プレビュー) を管理する

この記事では、Azure サンドボックス環境と Hybrid Runbook Workers で実行されている Azure Automation で Python 3 (プレビュー) パッケージをインポート、管理、使用する方法について説明します。Runbook を簡略化するために、Python パッケージを使用して必要なモジュールをインポートすることができます。 

Automation サービスで Python 3 Runbook をサポートするために、Automation アカウントには既定で Azure パッケージ 4.0.0 がインストールされています。 お使いの Automation アカウントに Python パッケージをインポートすることで、その既定のバージョンをオーバーライドできます。 お使いの Automation アカウントにインポートされたバージョンが優先されます。 1 つのパッケージをインポートするには、「[パッケージのインポート](#import-a-package)」を参照してください。 複数のパッケージを含むパッケージをインポートするには、「[依存関係を含むパッケージのインポート](#import-a-package-with-dependencies)」を参照してください。 

Python 2 パッケージの管理については、「[Azure Automation で Python 2 パッケージを管理する](./python-packages.md)」をご覧ください。

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

1. Automation アカウントの **[共有リソース]** で **[Python パッケージ]** を選択します。 次に **[+ Python パッケージの追加]** を選択します。

   :::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="[Python パッケージ] ページのスクリーンショット。左側のメニューに [Python パッケージ] が表示され、[Python パッケージの追加] が強調表示されています。":::

1. **[Python パッケージの追加]** ページで、アップロードするローカル パッケージを選択します。 パッケージは、 **.whl** または **.tar.gz** ファイルの場合があります。 
1. 名前を入力し、 **[ランタイム バージョン]** として Python 3.8.x (プレビュー) を選択します
1. **[インポート]** を選択します

   :::image type="content" source="media/python-3-packages/upload-package.png" alt-text="アップロードされた tar.gz ファイルが選択されている [Python 3.8.x パッケージの追加] ページを示すスクリーンショット。":::

パッケージがインポートされた後、そのパッケージはお使いの Automation アカウントの [Python パッケージ] ページに一覧表示されます。 パッケージを削除するには、パッケージを選択し、 **[削除]** をクリックします。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="パッケージがインポートされた後の [Python 3.8.x パッケージ] ページを示すスクリーンショット。":::

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
