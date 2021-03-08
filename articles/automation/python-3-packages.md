---
title: Azure Automation で Python 3 パッケージを管理する
description: この記事では、Azure Automation での Python 3 パッケージ (プレビュー) の管理方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734303"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Azure Automation で Python 3 パッケージ (プレビュー) を管理する

Azure Automation を使用すると、Azure および Linux Hybrid Runbook Worker で Python 3 Runbook (プレビュー) を実行することができます。 Runbook の簡略化のために、Python パッケージを使用して必要なモジュールをインポートすることができます。 この記事では、Azure Automation での Python 3 パッケージ (プレビュー) の管理および使用方法について説明します。

## <a name="import-packages"></a>パッケージをインポートする

Automation アカウントの **[共有リソース]** で **[Python パッケージ]** を選択します。 **[+ Python パッケージの追加]** を選択します。

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="左側のメニューに Python 3 パッケージが表示され、[Python 2 パッケージを追加する] が強調表示されている [Python 3 パッケージ] ページのスクリーンショット。":::

**[Python パッケージの追加]** ページで、**バージョン** に Python 3 を選択し、アップロードするローカル パッケージを選択します。 パッケージは、 **.whl** または **.tar.gz** ファイルの場合があります。 パッケージを選択したら、 **[OK]** を選択してアップロードします。

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="アップロードされた tar.gz ファイルが選択されている [Python 3 パッケージの追加] ページを示すスクリーンショット。":::

パッケージがインポートされると、Automation アカウントの [Python パッケージ] ページの **[Python 3 パッケージ (プレビュー)]** タブの下に一覧表示されます。パッケージを削除する必要がある場合は、パッケージを選択し、 **[削除]** をクリックします。

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="パッケージがインポートされた後の [Python 3 パッケージ] ページを示すスクリーンショット。":::

## <a name="import-packages-with-dependencies"></a>依存関係があるパッケージをインポートする

Azure Automation ではインポート プロセス中に Python パッケージの依存関係が解決されません。 ただし、すべての依存関係を含めてパッケージをインポートする方法があります。

### <a name="manually-download"></a>手動ダウンロード

[Python 3.8](https://www.python.org/downloads/release/python-380/) および [pip](https://pip.pypa.io/en/stable/) がインストールされている Windows 64 ビット マシン上で、次のコマンドを実行してパッケージとそのすべての依存関係をダウンロードします。

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

パッケージがダウンロードされたら、それらを Automation アカウントにインポートできます。

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

## <a name="next-steps"></a>次のステップ

Python Runbook を準備するには、「[Python Runbook を作成する](learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。
