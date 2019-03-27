---
title: Azure Media Services v3 SDK - Azure
description: この記事では、SDK/ツールを使用して Media Services v3 API での開発を始める方法の概要を示します。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: b9c89b6cb6dbc6cb217be671eb0e52c0416811ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889847"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>SDK/ツールを使用して Media Services v3 API での開発を始める

開発者は、Media Services の [REST API](https://aka.ms/ams-v3-rest-ref) または REST API と対話できるクライアント ライブラリを使って、カスタム メディア ワークフローを簡単に作成、管理、メンテナンスできます。 [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API は、OpenAPI 仕様 (旧称 Swagger) に基づいています。

このトピックでは、SDK、ツール、ドキュメントへのリンクを提供します。 また、さまざまな開発環境に役立つ情報も提供します。

## <a name="prerequisites"></a>前提条件

Media Services に対する開発を始めるには、次のものが必要です。

- 有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- [Media Services アカウントを作成する](create-account-cli-how-to.md)

## <a name="start-developing"></a>開発を始める

Azure Media Services では、次の SDK/ツールがサポートされています 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) は、Media Services について学習したい Windows ユーザーが使用できるツールです。 AMSE は、Media Services で VOD およびライブ コンテンツをアップロード、ダウンロード、エンコード、ストリーミングする Winforms/C# アプリケーションです。 AMSE ツールは、コードを記述しないで Media Services をテストしたいクライアント用です。 AMSE コードは、Media Services による開発を希望するお客様用のリソースとして提供されています。

AMSE はオープン ソース プロジェクトであり、サポートはコミュニティによって提供されます (問題は https://github.com/Azure/Azure-Media-Services-Explorer/issues) に報告できます)。 このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。 詳しくは、「[Code of Conduct FAQ (倫理規定についてよくある質問)](https://opensource.microsoft.com/codeofconduct/faq/)」を参照するか、opencode@microsoft.com 宛てに質問またはコメントをお送りください。

## <a name="rest"></a>REST

Media Services REST API を使用した開発を始めるには、REST クライアントをインストールします。 たとえば、**Postman**、REST プラグインを使用する **Visual Studio Code**、**Telerik Fiddler** などです。 ここの Media Services v3 サンプルでは [Postman](media-rest-apis-with-postman.md) を使用しています。

Media Services の [REST API リファレンス](https://aka.ms/ams-v3-rest-ref) ドキュメントを調べて、以下の例を確認してください。

- [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
- [Media Services アカウントにファイルをアップロードする - REST](upload-files-rest-how-to.md)
- [Media Services を使用してフィルターを作成する - REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager ベースの REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Media Services の [Azure CLI リファレンス](https://aka.ms/ams-v3-cli-ref) ドキュメントを調べて、以下の例を確認してください。

- [メディア占有ユニットのスケーリング - CLI](media-reserved-units-cli-how-to.md)
- [Media Services アカウントを作成する - CLI](./scripts/cli-create-account.md) 
- [アカウントの資格情報をリセットする - CLI](./scripts/cli-reset-account-credentials.md)
- [資産を作成する - CLI](./scripts/cli-create-asset.md)
- [ファイルをアップロードする - CLI](./scripts/cli-upload-file-asset.md)
- [トランスフォームを作成する - CLI](./scripts/cli-create-transform.md)
- [ジョブを作成する - CLI](./scripts/cli-create-jobs.md)
- [EventGrid を作成する - CLI](./scripts/cli-create-event-grid.md)
- [資産を公開する - CLI](./scripts/cli-publish-asset.md)
- [フィルター - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Media Services の [.NET リファレンス](https://aka.ms/ams-v3-dotnet-ref) ドキュメントを調べて、以下の例を確認してください。

- [チュートリアル:ビデオのアップロード、エンコード、ストリーミング - .NET](stream-files-tutorial-with-api.md) 
- [チュートリアル:Media Services v3 によるライブ ストリーミング - .NET](stream-live-tutorial-with-api.md)
- [チュートリアル:Media Services v3 でビデオを分析する - .NET](analyze-videos-tutorial-with-api.md)
- [ローカル ファイルからジョブの入力を作成する - .NET](job-input-from-local-file-how-to.md)
- [HTTPS URL からジョブの入力を作成する - .NET](job-input-from-http-how-to.md)
- [カスタム変換を使用してエンコードする - .NET](customize-encoder-presets-how-to.md)
- [AES-128 動的暗号化とキー配信サービスを使用する - .NET](protect-with-aes128.md)
- [DRM 動的暗号化とライセンス配信サービスを使用する - .NET](protect-with-drm.md)
- [既存のポリシーから署名キーを取得する - .NET](get-content-key-policy-dotnet-howto.md)
- [Media Services を使用してフィルターを作成する - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Azure Functions v2 と Media Services v3 の高度なビデオ オンデマンドの例](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Media Services の [Java リファレンス](https://aka.ms/ams-v3-java-ref) ドキュメントを確認してください。

## <a name="nodejs"></a>Node.js

Media Services の [Node.js リファレンス](https://aka.ms/ams-v3-nodejs-ref) ドキュメントを調べて、node.js で Media Services API を使用する方法が示されている[サンプル](https://github.com/Azure-Samples/media-services-v3-node-tutorials)をご覧ください。

## <a name="python"></a>Python

Media Services の [Python リファレンス](https://aka.ms/ams-v3-python-ref) ドキュメントを確認してください。

## <a name="go"></a>Go

Media Services の [Go リファレンス](https://aka.ms/ams-v3-go-ref) ドキュメントを確認してください。

## <a name="next-steps"></a>次の手順

- [アカウントの作成 - CLI](create-account-cli-how-to.md)
- [API へのアクセス - CLI](access-api-cli-how-to.md)

