---
title: Computer Vision API のサブスクリプション キーを取得する
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services での Computer Vision API の呼び出しのためにサブスクリプション キーを取得する方法を説明します。
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.openlocfilehash: db4d589bb0c7611e632a90f2174ad8e9c415bf6a
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985424"
---
# <a name="how-to-obtain-subscription-keys"></a>サブスクリプション キーを取得する方法

Computer Vision サービスには特別なサブスクリプション キーが必要です。 Computer Vision API を呼び出すたびに、サブスクリプション キーが必要です。 このキーは、クエリ文字列パラメーターによって渡すか、要求ヘッダー内で指定する必要があります。

サブスクリプション キーにサインアップする場合は、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 サインアップは無料です。 これらのサービスの料金は変更されることがあります。

>[!NOTE]
サブスクリプション キーが有効になるのは、次の [Microsoft Azure リージョン](https://azure.microsoft.com/regions/)のいずれか 1 つでのみとなります。 

| リージョン | Address |
|---|---|
| 米国西部 | westus.api.cognitive.microsoft.com |
| 米国東部 2 | eastus2.api.cognitive.microsoft.com |
| 米国中西部 | westcentralus.api.cognitive.microsoft.com |
| 西ヨーロッパ | westeurope.api.cognitive.microsoft.com |
| 東南アジア | southeastasia.api.cognitive.microsoft.com |

Computer Vision の無料試用版を使用してサインアップする場合、サブスクリプション キーは**米国中西部**リージョンで有効です (`https://westcentralus.api.cognitive.microsoft.com/`)。 これが最も一般的なケースです。 ただし、[https://azure.microsoft.com/](https://azure.microsoft.com/) Web サイト経由で Microsoft Azure アカウントを使用して Computer Vision にサインアップする場合は、上記のリージョン リストから無料試用版のリージョンを指定します。

たとえば、Microsoft Azure アカウントで Computer Vision にサインアップし、リージョンとして `westus` を指定する場合、REST API 呼び出しで `westus` リージョンを使用する必要があります (`https://westus.api.cognitive.microsoft.com/`)。

無料試用版キーを取得した後、ご自分のサブスクリプション キーのリージョンを忘れた場合は、[https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/) でリージョンを見つけることができます。

### <a name="related-links"></a>関連リンク:

* [Azure Cognitive Services APIs の価格オプション](https://azure.microsoft.com/pricing/details/cognitive-services/)
