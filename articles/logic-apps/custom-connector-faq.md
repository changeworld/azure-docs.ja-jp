---
title: "カスタム コネクタに関する FAQ - Azure Logic Apps | Microsoft Docs"
description: "要件やトリガーなど、カスタム コネクタの作成に関する FAQ"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>FAQ: カスタム コネクタ

## <a name="requirements"></a>必要条件

**Q:** REST API を使用せずにコネクタを作成できますか? </br>
**A:** いいえ。コネクタを作成するには、サービスのための安定した HTTP REST API をサポートする必要があります。 

**Q:** コネクタを作成する際にどのようなツールを使用できますか? </br>
**A:** Azure には、API としてサービスを公開するために使用できる機能やサービス (ホスト用の Azure App Service や API Management など) があります。

**Q:** サポートされている認証の種類を教えてください。 </br>
**A:** 次の認証標準がサポートされています。

* [OAuth 2.0](https://oauth.net/2/) ([Azure Active Directory](https://azure.microsoft.com/develop/identity/)、または Dropbox、GitHub、SalesForce などの特定のサービスを含む)
* 汎用 OAuth 2.0
* [基本認証](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [API キー](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>トリガー

**Q:** Webhook を使用せずにトリガーを作成することはできますか? </br>
**A:** いいえ。Azure Logic Apps と Microsoft Flow のカスタム コネクタでサポートされるのは、Webhook ベースのトリガーだけです。 他の実装パターンが必要な場合は、API の詳細を添えて [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) までお問い合わせください。

## <a name="certification"></a>認定

**Q:** Microsoft パートナーや独立系ソフトウェア ベンダー (ISV) でない場合でも コネクタを作成できますか? </br>
**A:** はい。組織内部で使用するコネクタを登録できます。ただし、コネクタを認定して一般公開する場合は、基になるサービスを所有しているか、API を使用する明示的な権限を示す必要があります。

## <a name="other"></a>その他

**Q:** API で動的ホストを使用しています。 OpenAPI でこれらの API を実装するにはどうすればよいですか? </br>
**A:** カスタム コネクタでは動的ホストをサポートしていません。 代わりに、開発とテストには静的ホストを使用します。 コネクタを認定する場合は、動的実装について Microsoft 連絡先担当者にお問い合わせください。

**Q:** Postman Collection V2 はサポートされていますか? </br>
**A:** いいえ。現在サポートされているのは Postman Collection V1 だけです。

**Q:** OpenAPI 3.0 はサポートされていますか? </br>
**A:** いいえ。現在サポートされているのは OpenAPI 2.0 だけです。