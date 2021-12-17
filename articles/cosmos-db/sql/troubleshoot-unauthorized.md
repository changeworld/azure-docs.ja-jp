---
title: Azure Cosmos DB の未承認の例外をトラブルシューティングする
description: 未承認の例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f0a42b376f9a7736056c712b9a631f0cabc1f575
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114185"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Azure Cosmos DB の未承認の例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 401: HTTP 要求で見つかった MAC 署名は、計算された署名と同じではありません。
"HTTP 要求で見つかった MAC 署名は、計算された署名と同じではありません" という 401 エラー メッセージを受け取った場合は、原因として次のシナリオが考えられます。

以前の SDK では、この例外は正しい 401 の未承認の例外ではなく、無効な JSON 例外として表示される場合があります。 新しい SDK はこのシナリオを正しく処理し、有効なエラー メッセージを表示します。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、未承認の例外の既知の原因と解決方法が含まれています。

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>キーが正しくローテーションされなかったことが最も一般的なシナリオです。
401 MAC 署名は、キーのローテーションの直後に見られ、最終的には変更なしに停止します。 

#### <a name="solution"></a>解答:
キーがローテーションされたが、[ベスト プラクティス](../secure-access-to-data.md#key-rotation)に従っていなかった。 Azure Cosmos DB アカウントのキーのローテーションには、Azure Cosmos DB アカウントのサイズに応じて、数秒から数日かかる場合があります。

### <a name="the-key-is-misconfigured"></a>キーが正しく構成されていない 
401 MAC 署名の問題は一貫性があり、そのキーを使用するすべての呼び出しで発生します。

#### <a name="solution"></a>解答:
キーがアプリケーションで正しく構成されておらず、アカウントに対して間違ったキーを使用しているか、またはキー全体がコピーされませんでした。

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>アプリケーションが書き込み操作に読み取り専用キーを使用している
401 MAC 署名の問題が作成や置き換えなどの書き込み操作でのみ発生し、読み取り要求は成功します。

#### <a name="solution"></a>解決方法:
操作が正常に完了できるようにするために、読み取り/書き込みキーを使用するようにアプリケーションを切り替えます。

### <a name="race-condition-with-create-container"></a>コンテナーの作成での競合状態
401 MAC 署名の問題がコンテナーの作成の直後に確認されます。 この問題が発生するのは、コンテナーの作成が完了するまでに限られています。

#### <a name="solution"></a>解答:
コンテナーの作成で競合状態があります。 コンテナーの作成が完了する前に、アプリケーション インスタンスがコンテナーにアクセスしようとしています。 この競合状態の最も一般的なシナリオは、アプリケーションが実行されているときに、コンテナーが削除され、同じ名前で再作成される場合です。 SDK は新しいコンテナーを使用しようとしますが、コンテナーの作成はまだ進行中であるため、キーがありません。

### <a name="bulk-mode-enabled"></a>[Bulk mode enabled]\(一括モードを有効にする\) 
[[Bulk mode enabled]\(一括モードを有効にする\)](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) を使用すると、読み取りと書き込みの操作が最適なネットワーク パフォーマンスのために最適化され、専用の Bulk API 通じてバックエンドに送信されます。 [Bulk mode enabled]\(一括モードを有効にする\) を使用して読み取り操作を実行しているときの 401 エラーは、多くの場合、アプリケーションで[読み取り専用キー](../secure-access-to-data.md#primary-keys)を使用していることを意味します。

#### <a name="solution"></a>解決策
[Bulk mode enabled]\(一括モードを有効にする\) を使用して操作を実行するときは、読み取り/書き込みキーを使用するか、または書き込みアクセス権を持つ承認メカニズムを使用します。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。