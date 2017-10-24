---
title: "REST API: Azure Data Lake Store に対するアカウント管理操作 | Microsoft Docs"
description: "Azure Data Lake Store と WebHDFS REST API を使用して、Data Lake Store に対するアカウント管理操作を実行します"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 6c43f2b341280731707e486ba6f22f11560102c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>REST API を使用した Azure Data Lake Store に対するアカウント管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

この記事では、REST API を使用して Data Lake Store に対するアカウント管理操作を実行する方法について説明します。 アカウント管理操作には、Data Lake Store アカウントの作成、Data Lake Store アカウントの削除などがあります。REST API を使用して Data Lake Store に対するファイルシステム操作を実行する方法については、[REST API を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-rest-api.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **[cURL](http://curl.haxx.se/)**。 この記事では、cURL を使用して、Data Lake Store アカウントに対して REST API 呼び出しを行う方法を説明します。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する方法
Azure Active Directory を使用した認証方法には 2 つあります。

* アプリケーションのエンドユーザー認証 (対話型) については、「[End-user authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するエンドユーザー認証)](data-lake-store-end-user-authenticate-rest-api.md)」を参照してください。
* アプリケーションのサービス間認証 (非対話型) については、「[Service-to-service authentication with Data Lake Store using .NET SDK (.NET SDK を使用した Data Lake Store に対するサービス間認証)](data-lake-store-service-to-service-authenticate-rest-api.md)」を参照してください。


## <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
この操作は、 [ここ](https://msdn.microsoft.com/library/mt694078.aspx)で定義されている REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

上記のコマンドで、\<`REDACTED`\> を以前に取得した承認トークンに置き換えます。 このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **input.json** ファイルに含まれています。 input.json ファイルの内容は、次のスニペットのようになります。

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store アカウントを削除する
この操作は、 [ここ](https://msdn.microsoft.com/library/mt694075.aspx)で定義されている REST API 呼び出しをベースにしています。

Data Lake Store アカウントを削除するには、次の cURL コマンドを使用します。 **\<yourstorename>** を実際の Data Lake Store 名に置き換えます。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

次のスニペットのような出力が表示されます。

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>次のステップ
* [REST API を使用した Data Lake Store に対するファイルシステム操作](data-lake-store-data-operations-rest-api.md)。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store REST API リファレンス](https://docs.microsoft.com/rest/api/datalakestore/)
* [Azure Data Lake Store と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)

