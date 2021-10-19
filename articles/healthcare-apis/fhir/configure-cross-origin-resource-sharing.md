---
title: FHIR サービスでクロスオリジン リソース共有を構成する
description: この記事では、FHIR サービスでクロスオリジン リソース共有を構成する方法について説明します
author: matjazl
ms.author: zxue
ms.date: 08/03/2021
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: e322b1fb848f9156b1d00d2d1ae051ef62536c4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778888"
---
# <a name="configure-cross-origin-resource-sharing-in-fhir-service"></a>FHIR サービスでクロスオリジン リソース共有を構成する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

Azure Healthcare API (ここでは FHIR サービスと呼ばれる) の FHIR サービスは、クロスオリジン リソース共有 [(CORS) をサポートしています](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 CORS を使用すると、あるドメイン (オリジン) のアプリケーションが異なるドメインのリソースにアクセスできる ("ドメイン間要求" と呼ばれます) ように、設定を構成することができます。

CORS は、別のドメインへの RESTful API 呼び出しを行う必要のあるシングルページ アプリでよく使用されます。

FHIR サービスで CORS 設定を構成するには、次の設定を指定します。

- **[配信元] (Access-Control-Allow-Origin)** 。 FHIR サービスに対してクロスオリジン要求を行うドメインの一覧。 各ドメイン (オリジン) は別々の行に入力する必要があります。 アスタリスク (*) を入力すると任意のドメインからの呼び出しを許可できますが、これにはセキュリティ上のリスクがあるためお勧めしません。

- **[ヘッダー] (Access-Control-Allow-Headers)** 。 オリジン要求に含められるヘッダーの一覧。 すべてのヘッダーを許可するには、アスタリスク (*) を入力します。

- **[方法] (Access-Control-Allow-Methods)** 。 API 呼び出しで許可されるメソッド (PUT、GET、POST など)。 すべてのメソッドを選択するには、 **[すべて選択]** を選択します。

- **[最長有効期間] (Access-Control-Max-Age)** 。 Access-Control-Allow-Headers および Access-Control-Allow-Methods に対するプリフライト要求の結果をキャッシュするための値 (秒数)。

- **[Allow Credentials]\(資格情報の許可\) (Access-Control-Allow-Credentials)** 。 CORS 要求には、[クロスサイト リクエスト フォージェリ (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻撃を防ぐために、通常 Cookie は含まれていません。 この設定を選択した場合、Cookie などの資格情報を要求に含めることができます。 [配信元] に既にアスタリスク (*) を設定している場合は、この設定を構成できません。

![クロスオリジン リソース共有 (CORS) の設定](media/cors/cors.png)

>[!NOTE]
>別のドメインのオリジンに異なる設定を指定することはできません。 すべての設定 ( **[ヘッダー]** 、 **[方法]** 、 **[最長有効期間]** 、および **[Allow credentials]\(資格情報の許可\)** ) が、[配信元] 設定に指定されたすべてのオリジンに適用されます。