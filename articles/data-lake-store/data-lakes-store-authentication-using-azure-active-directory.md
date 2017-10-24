---
title: "Data Lake Store での Azure Active Directory を使用した認証 | Microsoft Docs"
description: "Data Lake Store で Azure Active Directory を使用して認証する方法について説明します"
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
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: 6130f1a7fd58a50fe5c0133452a945e30899b654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Data Lake Store での Azure Active Directory を使用した認証

Azure Data Lake Store では、認証するために Azure Active Directory を使用します。 Azure Data Lake Store と組み合わせて動作するアプリケーションを作成する前に、Azure Active Directory (Azure AD) でアプリケーションを認証する方法を決めておく必要があります。 

## <a name="authentication-options"></a>認証オプション

* **エンドユーザー認証** - Data Lake Store での認証に、エンド ユーザーの Azure 資格情報が使用されます。 Data Lake Store と組み合わせて動作するように作成されたアプリケーションでは、これらのユーザー資格情報を求めるメッセージが表示されます。 そのため、この認証メカニズムは*対話型*であり、アプリケーションはログインしたユーザーのコンテキストで実行されます。 詳細と手順については、[Data Lake Store でのエンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)に関するページをご覧ください。

* **サービス間認証** - Data Lake Store でアプリケーションが自身を認証するようにするには、このオプションを使用します。 このような場合は、Azure Active Directory (AD) アプリケーションを作成して、Data Lake Store での認証に、Azure AD アプリケーションのキーを使用します。 そのため、この認証メカニズムは*非対話型*です。 詳細と手順については、[Data Lake Store でのサービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)に関するページをご覧ください。

次の表は、Data Lake Store でエンドユーザー認証メカニズムとサービス間認証メカニズムがどのようにサポートされているかを示しています。 表の見方は次のとおりです。

* ✔* マークは、その認証オプションがサポートされており、認証オプションの使用方法を説明する記事へのリンクがあることを表します。 
* ✔ マークは、その認証オプションがサポートされていることを表します。 
* 空欄は、その認証オプションがサポートされていないことを表します。


|右の項目とともに以下の認証オプションを使用                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST ()     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|エンドユーザー (MFA なし**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(非推奨)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|エンドユーザー (MFA あり)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|サービス間 (クライアント キーを使用)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|サービス間 (クライアント証明書を使用) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-certificate-for-account-management)**     |    ✔    |

<i>* <b>✔\*</b> マークをクリックしてください。リンク先に移動します。</i><br>
<i>** MFA は、多要素認証 (Multi-Factor Authentication) の意味です</i>

Azure Active Directory を認証に使用する方法の詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/active-directory-authentication-scenarios.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)
* [サービス間認証](data-lake-store-service-to-service-authenticate-using-active-directory.md)


