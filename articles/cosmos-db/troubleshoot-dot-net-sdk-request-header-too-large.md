---
title: Azure Cosmos DB での要求ヘッダーが大きすぎる問題または 400 Bad Request のトラブルシューティング
description: 要求ヘッダーが大きすぎる例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293922"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB の要求ヘッダーが大きすぎるメッセージを診断してトラブルシューティングする
要求ヘッダーが大きすぎるメッセージは、HTTP エラー コード 400 でスローされます。 このエラーは、要求ヘッダーのサイズが大きくなりすぎて、最大許容サイズを超えている場合に発生します。 最新バージョンの SDK を使用することをお勧めします。 バージョン 3.x または 2.x では、ヘッダー サイズのトレースが例外メッセージに追加されるため。少なくともこれらのバージョンを使用してください。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
要求ヘッダーが大きすぎるメッセージは、セッションまたは継続トークンが大きすぎる場合に発生します。 次のセクションでは、各カテゴリの問題の原因と解決方法について説明します。

### <a name="1-session-token-too-large"></a>1.セッション トークンが大きすぎる

#### <a name="cause"></a>原因:
400 Bad Request が発生する最も可能性の高い原因は、セッション トークンが大きくなっていることです。 次の文に該当する場合は、セッション トークンが大きすぎることが確認されます。

* このエラーは、継続トークンがないポイント操作 (作成、読み取り、更新など) で発生します。
* この例外は、アプリケーションに変更を加えずに開始されました。 セッション トークンは、コンテナー内のパーティション数が増加するにつれて増大します。 データ量が増加したり、スループットが増加したりすると、パーティションの数が増加します。

#### <a name="temporary-mitigation"></a>一時的な軽減策: 
クライアント アプリケーションを再起動して、すべてのセッション トークンをリセットします。 最終的に、問題の原因となったセッション トークンは以前のサイズに戻ります。 そのため、この問題を完全に回避するには、次のセクションの解決方法を使用します。

#### <a name="solution"></a>解決方法:
1. [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) または [.NET V2](performance-tips.md) パフォーマンスのヒントに関する記事のガイダンスに従って、TCP プロトコルで直接接続モードが使用されるようにアプリケーションを変換します。 HTTP プロトコルと同様に、TCP プロトコルを使用した直接モードではヘッダーサイズの制限がないため、この問題は回避されます。 最新バージョンの SDK を使用してください。このバージョンには、サービスの相互運用性がない場合のクエリ操作に対する修正が含まれています。
2. TCP プロトコルを使用した直接接続モードがワークロードのオプションでない場合は、[クライアントの整合性レベル](how-to-manage-consistency.md)を変更することで軽減してください。 セッション トークンは、Azure Cosmos DB の既定の整合性レベルであるセッションの整合性にのみ使用されます。 その他の整合性レベルでは、セッション トークンは使用されません。

### <a name="2-continuation-token-too-large"></a>2.継続トークン ヘッダーが大きすぎる

#### <a name="cause"></a>原因:
継続トークンが使用されているクエリ操作で、400 Bad Request が発生しています。 継続トークンが大きくなりすぎたり、さまざまなクエリの継続トークンのサイズが異なったりすると発生します。
    
#### <a name="solution"></a>解決方法:
1. [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) または [.NET V2](performance-tips.md) パフォーマンスのヒントに関する記事のガイダンスに従って、TCP プロトコルで直接接続モードが使用されるようにアプリケーションを変換します。 HTTP プロトコルと同様に、TCP プロトコルを使用した直接モードではヘッダーサイズの制限がないため、この問題は回避されます。 
3. TCP プロトコルを使用した直接接続モードがワークロードのオプションでない場合は、`ResponseContinuationTokenLimitInKb` オプションを設定してみてください。 このオプションは、v2 の `FeedOptions` または v3 の `QueryRequestOptions` で見つかります。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する
