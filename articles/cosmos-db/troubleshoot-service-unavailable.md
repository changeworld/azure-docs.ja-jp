---
title: Azure Cosmos DB サービス利用不可の例外をトラブルシューティングする
description: Cosmos DB サービス利用不可の例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293851"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Azure Cosmos DB サービス利用不可の例外を診断してトラブルシューティングする
SDK は Azure Cosmos DB サービスに接続できませんでした。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、サービス利用不可の例外の既知の原因と解決策が含まれています。

### <a name="1-the-required-ports-are-not-enabled"></a>1.必要なポートが有効になっていない
[必要なすべてのポート](performance-tips-dotnet-sdk-v3-sql.md#networking)が有効になっていることを確認します。

## <a name="if-an-existing-application-or-service-started-getting-503"></a>開始された既存のアプリケーションまたはサービスが 503 を受け取る場合

### <a name="1-there-is-an-outage"></a>1.障害がある
[[Azure Status]\(Azure の状態\)](https://status.azure.com/status) を確認して、進行中の問題があるか確認します。

### <a name="2-snat-port-exhaustion"></a>2.SNAT ポート不足
[SNAT ポート不足のガイド](troubleshoot-dot-net-sdk.md#snat)に従ってください。

### <a name="3-the-required-ports-are-being-blocked"></a>3.必要なポートがブロックされている
すべての[必要なポート](performance-tips-dotnet-sdk-v3-sql.md#networking)が有効になっていることを確認します。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する