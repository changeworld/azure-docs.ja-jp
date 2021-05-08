---
title: Azure Application Gateway での相互認証の概要
description: この記事では、Application Gateway での相互認証の概要について説明します。
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230956"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Application Gateway での相互認証の概要 (プレビュー)

相互認証 (クライアント認証) を使用すると、Application Gateway がクライアントの送信要求を認証できます。 通常、クライアントのみが Application Gateway を認証していますが、相互認証を使用すると、クライアントと Application Gateway の両方で相互認証を行うことができます。 

> [!NOTE]
> TLS 1.2 は今後も必須となるため、相互認証で TLS 1.2 を使用することをお勧めします。 

## <a name="mutual-authentication"></a>相互認証

Application Gateway は、信頼されたクライアント CA 証明書を Application Gateway にアップロードできる、証明書ベースの相互認証をサポートします。ゲートウェイは、その証明書を使用して、ゲートウェイへの要求を送信するクライアントを認証します。 IoT のユース ケースが増加し、業界全体のセキュリティ要件が増加したため、相互認証を使用して、Application Gateway と通信できるクライアントを管理および制御することができます。 

相互認証を構成するには、SSL プロファイルのクライアント認証部分の一部として、信頼されたクライアント CA 証明書をアップロードする必要があります。 相互認証の構成を完了するには、SSL プロファイルをリスナーに関連付ける必要があります。 アップロードするクライアント証明書には、ルート CA 証明書が必ず含まれている必要があります。 証明書チェーンをアップロードすることもできますが、チェーンには、必要な数の中間 CA 証明書に加えて、ルート CA 証明書が含まれている必要があります。 

たとえば、クライアント証明書にルート CA 証明書、複数の中間 CA 証明書、およびリーフ証明書が含まれている場合、ルート CA 証明書とすべての中間 CA 証明書が 1 つのファイルで Application Gateway にアップロードされていることを確認します。 信頼されたクライアント CA 証明書を抽出する方法の詳細については、「[信頼されたクライアント CA 証明書を抽出する方法](./mutual-authentication-certificate-management.md)」をご覧ください。

ルート CA 証明書と中間 CA 証明書を含む証明書チェーンをアップロードする場合は、証明書チェーンを PEM または CER ファイルとしてゲートウェイにアップロードする必要があります。 

> [!NOTE] 
> 相互認証は、Standard_v2 SKU と WAF_v2 SKU でのみ使用できます。 

### <a name="certificates-supported-for-mutual-authentication"></a>相互認証でサポートされている証明書

Application Gateway では、次の種類の証明書がサポートされています。

- CA (証明機関) の証明書:CA 証明書は、証明機関 (CA) によって発行されたデジタル証明書です
- 自己署名 CA 証明書: クライアントのブラウザーではこれらの証明書は信頼されず、仮想サービスの証明書が信頼チェーンに含まれていないことがユーザーに警告されます。 自己署名 CA 証明書は、テストや、管理者がクライアントを制御していて、ブラウザーのセキュリティの警告を安全にバイパスできる環境に適しています。 運用環境のワークロードでは、自己署名 CA 証明書を使用しないでください。

相互認証を設定する方法の詳細については、「[Application Gateway で相互認証を構成する](./mutual-authentication-portal.md)」をご覧ください。

> [!IMPORTANT]
> 相互認証を使用する場合は、信頼されたクライアント CA 証明書チェーン全体を確実に Application Gateway にアップロードしてください。 

## <a name="additional-client-authentication-validation"></a>追加のクライアント認証の検証

### <a name="verify-client-certificate-dn"></a>クライアント証明書の DN を確認する

クライアント証明書の直接の発行者を確認し、Application Gateway のみがその発行者を信頼することを許可するオプションがあります。 このオプションは既定ではオフになっていますが、Portal、PowerShell、または Azure CLI で有効にすることができます。 

クライアント証明書の直接の発行者を確認するために Application Gateway を有効にする場合、アップロードされた証明書からクライアント証明書の発行者の DN を抽出する方法を次に示します。 
* **シナリオ 1:** 証明書チェーンにルート証明書、中間証明書、リーフ証明書が含まれる 
    * *中間証明書* のサブジェクト名は、Application Gateway によりクライアント証明書の発行者の DN として抽出され、検証されます。 
* **シナリオ 2:** 証明書チェーンにルート証明書、intermediate1 証明書、intermediate2 証明書、リーフ証明書が含まれる
    * *Intermediate2 証明書* のサブジェクト名は、クライアント証明書の発行者の DN として抽出され、検証されます。 
* **シナリオ 3:** 証明書チェーンにルート証明書、リーフ証明書が含まれる 
    * *ルート証明書* のサブジェクト名が抽出され、クライアント証明書の発行者の DN として使用されます。
* **シナリオ 4:** 同じファイル内の同じ長さの複数の証明書チェーン。 チェーン 1 にルート証明書、intermediate1 証明書、リーフ証明書が含まれる。 チェーン 2 にルート証明書、intermediate2 証明書、リーフ証明書が含まれる。 
    * *Intermediate1 証明書* のサブジェクト名は、クライアント証明書の発行者の DN として抽出されます。  
* **シナリオ 5:** 同じファイル内の異なる長さの複数の証明書チェーン。 チェーン 1 にルート証明書、intermediate1 証明書、リーフ証明書が含まれる。 チェーン 2 にルート証明書、intermediate2 証明書、intermediate3 証明書、リーフ証明書が含まれる。 
    * *Intermediate3 証明書* のサブジェクト名は、クライアント証明書の発行者の DN として抽出されます。 

> [!IMPORTANT]
> ファイルごとに証明書チェーンを 1 つだけアップロードすることをお勧めします。 これは、クライアント証明書の DN を確認する機能を有効にする場合に特に重要です。 1 つのファイルに複数の証明書チェーンをアップロードすると、シナリオ 4 または 5 で終了し、提示されたクライアント証明書が、Application Gateway がチェーンから抽出したクライアント証明書の発行者の DN と一致しない場合に、後で問題が発生する可能性があります。 

信頼されたクライアント CA 証明書チェーンを抽出する方法の詳細については、「[信頼されたクライアント CA 証明書チェーンを抽出する方法](./mutual-authentication-certificate-management.md)」をご覧ください。

## <a name="server-variables"></a>サーバー変数 

相互認証を使用すると、追加のサーバー変数を使用して、Application Gateway の背後にあるバックエンド サーバーにクライアント証明書に関する情報を渡すことができます。 使用可能なサーバー変数とその使用方法の詳細については、「[サーバー変数](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview)」をご覧ください。

## <a name="next-steps"></a>次の手順

相互認証について学習した後は、「[PowerShell で Application Gateway の認証を構成する」](./mutual-authentication-powershell.md)をご覧になり、相互認証を使用して Application Gateway を作成します。 

