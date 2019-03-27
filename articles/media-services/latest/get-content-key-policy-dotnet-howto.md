---
title: Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する - Azure | Microsoft Docs
description: このトピックでは、Media Services v3 .NET SDK を使用して既存のポリシーから署名キーを取得する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322485"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>既存のポリシーから署名キーを取得する

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API は、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 キーは常に、null または空であるか、応答から削除されます。 シークレットまたは資格情報を取得するには、別のアクション メソッドを呼び出す必要があります。 別のアクションを使用すれば、シークレットが取得/表示される API もあればそうでない API もある場合に、異なる RBAC セキュリティ アクセス許可を設定できます。 RBAC を使用してアクセスを管理する方法の詳細については、[RBAC を使用したアクセスの管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)に関するページを参照してください。

この例には以下のようなものがあります 

* StreamingLocator の Get で ContentKey の値が返されない。 
* ContentKeyPolicy の Get で制限キーが返されない。 
* ジョブの HTTP 入力 URL の (署名を削除する) URL に含まれているクエリ文字列部分が返されない。

この記事の例では、.NET を使用して既存のポリシーから署名キーを取得する方法を示します。 
 
## <a name="download"></a>ダウンロード 

次のコマンドを使って、.NET サンプルが含まれる GitHub リポジトリを、ご利用のコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
シークレットを使用した ContentKeyPolicy の例は、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

## <a name="get-contentkeypolicy-with-secrets"></a>シークレットを使用した ContentKeyPolicy を取得する 

キーを取得するには、次の例に示すように **GetPolicyPropertiesWithSecretsAsync** を使用します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>次の手順

[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md) 
