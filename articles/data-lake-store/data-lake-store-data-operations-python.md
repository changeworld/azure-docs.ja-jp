---
title: 'Python: Azure Data Lake Store に対するファイルシステム操作 | Microsoft Docs'
description: Python SDK を使用して Data Lake Store のファイル システムを操作する方法について説明します。
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: fa1c42a7bb9a06b2ea790e883ec7da6caa41d6b3
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011960"
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-python"></a>Python を使用した Azure Data Lake Store に対するファイルシステム操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

この記事では、Python SDK を使用して Azure Data Lake Store に対するファイルシステム操作を実行する方法について説明します。 Python を使用して Data Lake Store に対するアカウント管理操作を実行する方法については、[Python を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-python.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Store アカウント**。 「[Azure Portal で Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)」の手順に従ってください。

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

2. 必要なモジュールをインポートする次の行を追加します。

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

## <a name="create-filesystem-client"></a>ファイルシステム クライアントを作成する

次のスニペットは、まず Data Lake Store アカウントのクライアントを作成します。 これは、クライアント オブジェクトを使用して、Data Lake Store アカウントを作成します。 最後に、スニペットは、ファイル システム クライアント オブジェクトを作成します。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>ディレクトリを作成する

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>ファイルをアップロードする


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>ファイルをダウンロードする

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>ディレクトリを削除する

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>次の手順
* [Python を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-python.md)。

## <a name="see-also"></a>関連項目

* [Azure Data Lake Store Python (ファイルシステム) リファレンス](http://azure-datalake-store.readthedocs.io/en/latest)
* [Azure Data Lake Store と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)
