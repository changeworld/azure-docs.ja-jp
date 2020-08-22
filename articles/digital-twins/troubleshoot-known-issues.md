---
title: 既知の問題 - Azure Digital Twins
description: Azure Digital Twinds の既知の問題を認識し、軽減するのに役立ちます。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044140"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins の既知の問題

この記事では、Azure Digital Twins に関連する既知の問題について説明します。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell の "400 Client Error:Bad Request"

Cloud Shell のコマンドが断続的に失敗して、エラー "400 Client Error:Bad Request for url: http://localhost:50342/oauth2/token" を受け取り、その後に完全なスタック トレースが示されることがあります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

これは、`az login` コマンドを再実行し、その後のログイン手順を完了することによって解決できます。

この後、コマンドを再実行できるようになります。

### <a name="possible-causes"></a>考えられる原因

これは Cloud Shell の既知の問題の結果です。"[*Cloud Shell からのトークンの取得が断続的に失敗し、400 Client Error:Bad Request を受け取る*](https://github.com/Azure/azure-cli/issues/11749)"

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)