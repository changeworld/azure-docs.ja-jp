---
title: Azure リレー API の概要 | Microsoft Docs
description: この記事では、使用可能な Azure Relay API (.NET Standard、.NET Framework、Node.js など) の概要について説明します。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: ad7226b5c5badfddf9f436a1229a48f729485821
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204210"
---
# <a name="available-relay-apis"></a>使用可能なリレー API

## <a name="runtime-apis"></a>ランタイム API

現在使用できるリレー ランタイム クライアントの一覧を次の表に示します。

各ランタイム ライブラリの詳細については、「[追加情報](#additional-information)」セクションをご覧ください。

| 言語/プラットフォーム | 使用可能な機能 | クライアント パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | ハイブリッド接続 | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF リレー | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 該当なし |
| Node | ハイブリッド接続 | [WebSocket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSocket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP 要求: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>関連情報

#### <a name="net"></a>.NET

.NET エコシステムには複数のランタイムがあるため、リレーに対して複数の .NET ライブラリが存在します。 .NET Standard ライブラリは、.NET Core または .NET Framework のいずれかを使用して実行できますが、.NET Framework ライブラリについては、.NET Framework 環境でのみ実行できます。 .NET Framework の詳細については、「[フレームワークのバージョン](/dotnet/articles/standard/frameworks)」を参照してください。

.NET Framework ライブラリは WCF プログラミング モデルのみをサポートし、WCF `net.tcp` トランスポートに基づいた専用のバイナリ プロトコルに依存します。 このプロトコルとライブラリは、既存のアプリケーションとの下位互換性のために維持されています。

.NET Standard ライブラリは、HTTP と Websocket に基づいて構築されたハイブリッド接続リレーのためのオープン プロトコル定義に基づいています。 このライブラリでは、Websocket 上のストリーム抽象化と、HTTP 要求に応答するための単純な要求応答 API ジェスチャがサポートされています。 [Web API](https://github.com/Azure/azure-relay-dotnet) サンプルは、Web サービスのためにハイブリッド接続を ASP.NET Core と統合する方法を示しています。

#### <a name="nodejs"></a>Node.js

上の表に一覧表示されているハイブリッド接続モジュールは、既存の Node.js モジュールを、ローカル ネットワーク スタックの代わりに Azure Relay サービスでリッスンする代替実装で置き換えるか、または修正します。

`hyco-https` モジュールは、コアの Node.js モジュール `http` と `https` を修正し、部分的にオーバーライドすることにより、これらのコア モジュールに依存する多くの既存の Node.js モジュールおよびアプリケーションと互換性のある HTTPS リスナー実装を提供します。

`hyco-ws` および `hyco-websocket` モジュールは、Node.js の一般的な `ws` および `websocket` モジュールを修正することにより、いずれかのモジュールに依存するモジュールやアプリケーションがハイブリッド接続リレーの背後で動作できるようにする代替リスナー実装を提供します。

これらのモジュールに関する詳細は、[azure-relay-node](https://github.com/Azure/azure-relay-node) GitHub リポジトリで見つけることができます。

## <a name="next-steps"></a>次のステップ

Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)
