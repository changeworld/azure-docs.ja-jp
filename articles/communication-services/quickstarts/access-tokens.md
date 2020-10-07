---
title: クイックスタート - アクセス トークンを作成して管理する
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services 管理クライアント ライブラリを使用して、ID とアクセス トークンを管理する方法について説明します。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a76000ecacdf78196ec1b80a60940484f6421641
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945742"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>クイック スタート:アクセス トークンを作成して管理する

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Communication Services 管理クライアント ライブラリを使用してアクセス トークンをプロビジョニングして管理することによって、Azure Communication Services の使用を開始します。 アクセス トークンを使用すると、チャットと通話のクライアント ライブラリが Azure Communication Services に対して直接認証を行うことができます。 これらのトークンは、実装するサーバー側のトークン プロビジョニング サービスで生成されます。 その後これらは、クライアント デバイス上で Communication Services クライアント ライブラリを初期化するために使用されます。

このチュートリアルを通して画像に表示されている料金は、あくまでも例示のためのものであることに注意してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

アプリの出力に、完了した各アクションが表示されます。
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](./create-communication-resource.md#clean-up-resources)に関する記事を参照してください。


## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * ID を管理する
> * アクセス トークンを発行する
> * Communication Services 管理クライアント ライブラリを使用する


> [!div class="nextstepaction"]
> [音声通話をアプリに追加する](./voice-video-calling/getting-started-with-calling.md)

次のことも実行できます。

 - [認証について学習する](../concepts/authentication.md)
 - [チャットをアプリに追加する](./chat/get-started.md)
 - [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
