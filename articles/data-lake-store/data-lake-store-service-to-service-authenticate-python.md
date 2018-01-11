---
title: "サービス間認証: Azure Active Directory を使用した Python と Data Lake Store | Microsoft Docs"
description: "Azure Active Directory を使用した Data Lake Store でのサイト間認証に Python を使用する方法について説明します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 9f23a5e24067dab29a21a44404a912588b46d3b0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Data Lake Store での Python を使用したサービス間認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

この記事では、Python SDK を使用して、Azure Data Lake Store に対するサービス間認証を行う方法について説明します。 Python を使用した Data Lake Store に対するエンド ユーザー認証については、「[End-user authentication with Data Lake Store using Python (Data Lake Store での Python を使用したエンドユーザー認証)](data-lake-store-end-user-authenticate-python.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

* **Python**。 Python は、[ここ](https://www.python.org/downloads/)からダウンロードできます。 この記事では、Python 3.6.2 を使用します。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Store に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Data Lake Store を操作するには、3 つのモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールなどが含まれています。
* `azure-mgmt-datalake-store` モジュール。これには、Azure Data Lake Store アカウント管理操作が含まれています。 このモジュールの詳細については、[Azure Data Lake Store 管理モジュール リファレンス](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)を参照してください。
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
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py に対する変更を保存します。

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>クライアント シークレットによるサービス間認証を使用したアカウント管理

Data Lake Store アカウントの作成、削除などのアカウント管理操作のために Azure AD で認証する場合、このスニペットを使用します。次のスニペットは、既存の Azure AD "Web アプリ" アプリケーションのアプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>クライアント シークレットによるサービス間認証を使用したファイルシステム操作

フォルダーの作成、ファイルのアップロードなど、Data Lake Store でのファイルシステム操作のために Azure AD で認証する場合、次のスニペットを使用します。次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレットを使用して、アプリケーションを非対話的に認証するために使用できます。 これは、既存の Azure AD "Web アプリ" アプリケーションと共に使用します。

    adlCreds = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE', resource = 'https://datalake.azure.net/')

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>次のステップ
この記事では、サービス間認証を使って、Python を使用して Azure Data Lake Store で認証する方法を説明しました。 これで、Python を使用して Azure Data Lake Store を使用する方法について説明した次の記事に進めるようになりました。

* [Python を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-python.md)
* [Python を使用した Data Lake Store に対するデータ操作](data-lake-store-data-operations-python.md)


