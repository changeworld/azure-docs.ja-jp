---
title: "サービス間認証: REST API から Azure Active Directory を使用して Data Lake Store に対する認証を行う | Microsoft Docs"
description: "REST API から Azure Active Directory を使用して Data Lake Store に対するサービス間認証を行う方法について説明します"
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
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 754e65c4bcf8574a16b9620e2f21938ecc62b735
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Data Lake Store に対する REST API を使用したサービス間認証
> [!div class="op_single_selector"]
> * [Java の使用](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK の使用](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python の使用](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API の使用](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

この記事では、REST API を使用して、Azure Data Lake Store に対するサービス間認証を行う方法について説明します。 REST API を使用した Data Lake Store に対するエンド ユーザー認証については、「[End-user authentication with Data Lake Store using REST API](data-lake-store-end-user-authenticate-rest-api.md)」(REST API を使用した Data Lake Store に対するエンドユーザー認証) を参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Active Directory "Web" アプリケーションを作成します**。 [Data Lake Store に対する Azure Active Directory を使用したサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)の手順を完了している必要があります。

## <a name="service-to-service-authentication"></a>サービス間認証
このシナリオでは、操作を実行するための独自の資格情報をアプリケーションが提供します。 そのために、次のスニペットに示すように POST 要求を発行する必要があります。 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

この要求の出力には、後で REST API 呼び出しで渡す認証トークン (以下の出力の `access-token` で示される) が取り込まれます。 テキスト ファイルに認証トークンを保存します。このファイルは、Data Lake Store に対して REST 呼び出しを行うときに必要になります。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

この記事では、 **非対話型** のアプローチを使用します。 非対話型 (サービス間呼び出し) の詳細については、 [資格情報を使用したサービス間呼び出し](https://msdn.microsoft.com/library/azure/dn645543.aspx)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ
この記事では、Azure Data Lake Store での認証に REST API を使用してサービス間認証を使用する方法を学習しました。 これで、REST API を使用して Azure Data Lake Store を使用する方法について説明した次の記事に進めるようになりました。

* [REST API を使用した Data Lake Store に対するアカウント管理操作](data-lake-store-get-started-rest-api.md)
* [REST API を使用した Data Lake Store に対するデータ操作](data-lake-store-data-operations-rest-api.md)


