---
title: Windows Virtual Desktop に接続する Windows 10 または 7 - Azure
description: Windows デスクトップ クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 745b33efe46c82e3b9358c9c5a2ed13292242db1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997345"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows デスクトップ クライアントを使用して接続する

> 適用対象:Windows 7、Windows 10、および Windows 10 IoT Enterprise

Windows デスクトップ クライアントを使用して、Windows 7 または Windows 10、および Windows 10 IoT Enterprise を使用しているデバイス上の Windows Virtual Desktop リソースにアクセスできます。

> [!IMPORTANT]
> Windows Virtual Desktop では、RemoteApp とデスクトップ接続 (RADC) クライアントおよびリモート デスクトップ接続 (MSTSC) クライアントはサポートされていません。

## <a name="install-the-windows-desktop-client"></a>Windows デスクトップ クライアントをインストールする

ご自身の Windows バージョンに合ったクライアントを選択してください。

- [Windows (64 ビット)](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows (32 ビット)](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

現在のユーザー用にのクライアントをインストールできます。この場合、管理者権限は必要ありません。または、管理者がクライアントをインストールして構成し、デバイス上のすべてのユーザーがアクセスできるようにすることができます。

インストールが完了すると、クライアントはスタート メニューから**リモート デスクトップ**を検索することにより起動できます。

## <a name="subscribe-to-a-feed"></a>フィードのサブスクライブ

管理者によって提供されるフィードにサブスクライブして、使用可能な管理対象リソースの一覧を取得します。サブスクライブによって、ローカル PC でリソースを使用できるようにします。

フィードをサブスクライブするには:

1. Windows デスクトップ クライアントを開きます。
2. サービスに接続して自分のリソースを取得するために、メイン ページで **[サブスクライブ]** を選択します。
3. メッセージが表示されたら、自分のユーザー アカウントでサインインします。

サインインに成功すると、アクセスできるリソースの一覧が表示されます。

リソースは、次の 2 つの方法のいずれかで起動できます。

- クライアントのメイン ページで、リソースをダブルクリックして起動します。
- [スタート] メニューから他のアプリを通常の方法で起動するようにリソースを起動します。
  - 検索バーでアプリを検索することもできます。

フィードにサブスクライブすると、フィードのコンテンツが自動的に定期的に更新されます。 管理者によって行われる変更に基づいて、リソースが追加、変更、または削除されることがあります。

## <a name="next-steps"></a>次の手順

Windows デスクトップ クライアントの使用方法の詳細については、「[Windows デスクトップ クライアントの概要](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop)」を参照してください。
