---
title: ポリシーから署名キーを取得する .NET
description: このトピックでは、Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 05/25/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 373a97f26ee2fd2d448d142c89528576d3495510
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "110791794"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>既存のポリシーから署名キーを取得する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 詳細については、次を参照してください。詳細については、[Azure RBAC と Media Services アカウント](security-rbac-concept.md)に関するページをご覧ください。

この記事の例では、.NET を使用して既存のポリシーから署名キーを取得する方法を示します。 
 
## <a name="download"></a>ダウンロード 

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、ご利用のコンピューターにクローンします。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
シークレットを使用した ContentKeyPolicy の例は、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/main/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

## <a name="get-contentkeypolicy-with-secrets"></a>シークレットを使用した ContentKeyPolicy を取得する 

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>次のステップ

[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](architecture-design-multi-drm-system.md) 
