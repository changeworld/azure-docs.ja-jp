---
title: Azure API for FHIR でのクロスオリジン リソース共有の構成
description: この記事では、Azure API for FHIR でのクロスオリジン リソース共有の構成方法について説明します
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.openlocfilehash: c6bb39508055926323dad3dd6b50f9d60fdecdab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233711"
---
# <a name="configure-cross-origin-resource-sharing-in-the-azure-api-for-fhir"></a>Azure API for FHIR でのクロスオリジン リソース共有の構成

FHIR (高速ヘルスケア相互運用性リソース) 向け Azure API では、[クロスオリジン リソース共有 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) をサポートしています。 CORS を使用すると、あるドメイン (オリジン) のアプリケーションが異なるドメインのリソースにアクセスできる ("ドメイン間要求" と呼ばれます) ように、設定を構成することができます。

CORS は、別のドメインへの RESTful API 呼び出しを行う必要のあるシングルページ アプリでよく使用されます。

Azure API for FHIR で CORS 設定を構成するには、次の設定を指定します。

- **[配信元] (Access-Control-Allow-Origin)** 。 Azure API for FHIR へのクロスオリジン要求が許可されるドメインの一覧。 各ドメイン (オリジン) は別々の行に入力する必要があります。 アスタリスク (*) を入力すると任意のドメインからの呼び出しを許可できますが、これにはセキュリティ上のリスクがあるためお勧めしません。

- **[ヘッダー] (Access-Control-Allow-Headers)** 。 オリジン要求に含められるヘッダーの一覧。 すべてのヘッダーを許可するには、アスタリスク (*) を入力します。

- **[方法] (Access-Control-Allow-Methods)** 。 API 呼び出しで許可されるメソッド (PUT、GET、POST など)。 すべてのメソッドを選択するには、 **[すべて選択]** を選択します。

- **[最長有効期間] (Access-Control-Max-Age)** 。 Access-Control-Allow-Headers および Access-Control-Allow-Methods に対するプリフライト要求の結果をキャッシュするための値 (秒数)。

- **[Allow Credentials]\(資格情報の許可\) (Access-Control-Allow-Credentials)** 。 CORS 要求には、[クロスサイト リクエスト フォージェリ (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻撃を防ぐために、通常 Cookie は含まれていません。 この設定を選択した場合、Cookie などの資格情報を要求に含めることができます。 [配信元] に既にアスタリスク (*) を設定している場合は、この設定を構成できません。

![クロスオリジン リソース共有 (CORS) の設定](media/cors/cors.png)

>[!NOTE]
>別のドメインのオリジンに異なる設定を指定することはできません。 すべての設定 ( **[ヘッダー]** 、 **[方法]** 、 **[最長有効期間]** 、および **[Allow credentials]\(資格情報の許可\)** ) が、[配信元] 設定に指定されたすべてのオリジンに適用されます。