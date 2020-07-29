---
title: 既知の問題 - Azure Digital Twins
description: Azure Digital Twinds の既知の問題を認識し、軽減するのに役立ちます。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528311"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins の既知の問題

この記事では、Azure Digital Twins に関連する既知の問題について説明します。

## <a name="managing-event-routes-in-the-azure-portal"></a>Azure portal でのイベント ルートの管理

個人用の [**Microsoft アカウント (MSA)** ](https://account.microsoft.com/account/Account) ( *@outlook.com* アカウントなど) を使用してポータルにサインインした場合、ポータルでイベント ルートを管理しようとすると、自分のアクセス許可レベルに関係なく、画面に "*You need permission to view event routes (イベント ルートを表示するためのアクセス許可が必要です)* " というメッセージが表示されます。

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Azure Digital Twins インスタンスでイベント ルートを作成しようとした際のアクセス許可エラーに関する Azure portal のスクリーンショット":::

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

現在、ポータルでイベント ルートを管理できないユーザーも、Azure Digital Twins の API または CLI を使用してイベント ルートを管理できます。 この問題を軽減するには、イベント ルート管理のために、これらのツールのいずれかに切り替えることをお勧めします。

この手順については、[*方法:エンドポイントとルートの管理*](how-to-manage-routes.md)に関する記事を参照してください。

### <a name="possible-causes"></a>考えられる原因

個人用の [Microsoft アカウント (MSA)](https://account.microsoft.com/account/Account) ( *@outlook.com* アカウントなど) を使用してポータルにサインインしている。 現在、Azure portal でのイベント ルートの管理を利用できるのは、企業ドメイン アカウントの Azure ユーザーのみになります。

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