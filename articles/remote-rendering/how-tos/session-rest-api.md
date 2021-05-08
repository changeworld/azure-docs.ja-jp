---
title: セッション管理 REST API
description: セッションを管理する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950027"
---
# <a name="use-the-session-management-rest-api"></a>セッション管理 REST API を使用する

Azure Remote Rendering 機能を使用するには、"*セッション*" を作成する必要があります。 各セッションは、クライアント デバイスが接続できる、Azure で割り当てられるサーバーに対応します。 デバイスが接続すると、サーバーは要求されたデータをレンダリングし、結果をビデオ ストリームとして提供します。 セッションの作成時に、実行元の[サーバーの種類](../reference/vm-sizes.md)を選択します。これにより、価格が決まります。 セッションが不要になったら、停止する必要があります。 手動で停止しない場合、セッションの "*リース時間*" が期限切れになると、自動的にシャットダウンされます。

## <a name="rest-api-reference"></a>REST API リファレンス

REST API リファレンスは[こちら](/rest/api/mixedreality/2021-01-01preview/remoterendering)で参照できます。Swagger の定義は[こちら](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)で参照できます。
*Scripts* フォルダー内の [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)に、サービスの使用方法を示す *RenderingSession.ps1* という名前の PowerShell スクリプトが提供されています。 スクリプトとその構成については、次を参照してください: [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)。
[.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) と [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md) 用の SDK も用意されています。

> [!IMPORTANT]
> 待機時間は、リモート レンダリングを使用する場合の重要な要素です。 最適なエクスペリエンスを実現するために、最も近いリージョンにセッションを作成します。 [Azure 待機時間テスト](https://www.azurespeed.com/Azure/Latency)を使用して、最も近いリージョンを特定できます。

> [!IMPORTANT]
> クライアント デバイスがレンダリング セッションに接続するために、ARR ランタイム SDK が必要です。 これらの SDK は、[.NET](/dotnet/api/microsoft.azure.remoterendering) と [C++](/cpp/api/remote-rendering/) で利用できます。 サービスへの接続とは別に、これらの SDK を使用してセッションを開始および停止することもできます。

## <a name="next-steps"></a>次のステップ

* [認証に Azure フロントエンド API を使用する](frontend-apis.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)