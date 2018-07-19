---
title: 'Python: Azure Data Lake Store に対するアカウント管理操作 | Microsoft Docs'
description: Python SDK を使用して Data Lake Store に対するアカウント管理操作を行う方法について説明します。
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: e5b04a4cfbf26011753715f02baea689ec3065b6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012050"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Python を使用した Azure Data Lake Store に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure Data Lake Store 用 Python SDK を使用して、Data Lake Store アカウントの作成、Data Lake Store アカウントの一覧表示などの基本的なアカウント管理操作を実行する方法を説明します。Python を使用して Data Lake Store に対するファイルシステム操作を実行する方法については、[Python を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-python.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure リソース グループ**。 手順については、[Azure リソース グループの作成](../azure-resource-manager/resource-group-portal.md)に関するページを参照してください。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Store を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Azure Data Lake Store アカウント管理操作が含まれています。 このモジュールの詳細については、[Azure Data Lake Store 管理モジュール リファレンス](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)を参照してください。
* `azure-datalake-store` モジュール。これには、Azure Data Lake Store ファイルシステム操作が含まれています。 このモジュールの詳細については、[Azure Data Lake Store ファイルシステム モジュール リファレンス](http://azure-datalake-store.readthedocs.io/en/latest/)を参照してください。

モジュールをインストールするには、次のコマンドを使用します。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

1. 任意の IDE で、**mysample.py** などの新しい Python アプリケーションを作成します。

2. 必要なモジュールをインポートするには、次のスニペットを追加します

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py に対する変更を保存します。

## <a name="authentication"></a>Authentication

このセクションでは、Azure AD で認証を行うさまざまな方法について説明します。 次の方法を使用できます。

* アプリケーションのエンドユーザー認証については、「[End-user authentication with Data Lake Store using Python (Python を使用した Data Lake Store に対するエンドユーザー認証)](data-lake-store-end-user-authenticate-python.md)」を参照してください。
* アプリケーションのサービス間認証については、「[Service-to-service authentication with Data Lake Store using Python (Python を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-python.md)」を参照してください。

## <a name="create-client-and-data-lake-store-account"></a>クライアントと Data Lake Store アカウントを作成する

次のスニペットは、まず Data Lake Store アカウントのクライアントを作成します。 これは、クライアント オブジェクトを使用して、Data Lake Store アカウントを作成します。 最後に、スニペットは、ファイル システム クライアント オブジェクトを作成します。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Data Lake Store アカウントを一覧表示する

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Data Lake Store アカウントを削除する

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>次の手順
* [Python を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-python.md)。

## <a name="see-also"></a>関連項目

* [Azure Data Lake Store Python (ファイルシステム) リファレンス](http://azure-datalake-store.readthedocs.io/en/latest)
* [Azure Data Lake Store と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)
