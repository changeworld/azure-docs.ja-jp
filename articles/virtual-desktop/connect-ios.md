---
title: iOS から Windows Virtual Desktop に接続する - Azure
description: iOS クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
ms.openlocfilehash: 92e11edf3b28017ac73702e3f71dfc491d3986e3
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110631"
---
# <a name="connect-with-the-ios-client"></a>iOS クライアントに接続する

> 適用対象: iOS 13.0 以降。 iPhone、iPad、iPod touch と互換性があります。

ダウンロード可能なクライアントを使用して、ご使用の iOS デバイスから Windows Virtual Desktop リソースにアクセスできます。 このガイドでは、iOS クライアントを設定する方法について説明します。

## <a name="install-the-ios-client"></a>iOS クライアントをインストールする

開始するには、クライアントを[ダウンロード](https://aka.ms/rdios)して、ご利用の iOS デバイスにインストールします。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者から提供されたフィードをサブスクライブして、ご使用の iOS デバイスでアクセスできる管理対象リソースの一覧を取得します。

フィードをサブスクライブするには:

1. 接続センターで、 **+** をタップし、次に **[Add Workspace]\(ワークスペースを追加\)** をタップします。
2. **[Feed URL]\(フィード URL\)** フィールドにフィード URL を入力します。 フィード URL には、URL またはメール アドレスのいずれかを指定できます。
   - URL を使用する場合は、管理者によって付与されたものを使用します。 通常、URL は <https://rdweb.wvd.microsoft.com> です。
   - 電子メールを使用するには、メール アドレスを入力します。 これにより、メール アドレスに関連付けられている URL を検索するようにクライアントに指示されます (管理者がそのようにサーバーを構成した場合)。
3. **[Next]\(次へ\)** をタップします。
4. メッセージが表示されたら、資格情報を入力します。
   - **[User name]\(ユーザー名\)** 。リソースへのアクセス許可を持つユーザー名を指定します。
   - **[パスワード]** には、ユーザー名に関連付けられているパスワードを指定します。
   - 管理者が追加の要素を指定するように認証を構成した場合、そのように求められることもあります。
5. **[保存]** をタップします。

その後、接続センターにリモート リソースが表示されます。

フィードをサブスクライブすると、フィードのコンテンツが自動で定期的に更新されます。 管理者によって行われる変更に基づいて、リソースが追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次のステップ

iOS クライアントの使用方法の詳細については、「[iOS クライアントの概要](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios)」のドキュメントで確認してください。
