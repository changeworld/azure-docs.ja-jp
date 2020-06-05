---
title: Windows Virtual Desktop Web クライアントの接続 - Azure
description: Web クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b73a6bf2c8b853d9e3e939b6c8ff6fc05ac7021
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657260"
---
# <a name="connect-with-the-web-client"></a>Web クライアントに接続する

>[!IMPORTANT]
>この記事の内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Fall 2019 リリースに適用されます。 Spring 2020 更新プログラムで導入された Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../connect-web.md)を参照してください。

Web クライアントを利用すると、時間のかかるインストール プロセスなしで Web ブラウザーから Windows Virtual Desktop リソースにアクセスできます。

>[!NOTE]
>現在、Web クライアントにはモバイル OS のサポートがありません。

## <a name="supported-operating-systems-and-browsers"></a>サポートされているオペレーティング システムとブラウザー

任意の HTML5 対応ブラウザーが動作しますが、正式にサポートしているオペレーティング システムとブラウザーは次のとおりです。

| Browser           | サポート対象 OS                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | バージョン 55 以降 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>リモート リソース フィードにアクセスする

ブラウザーで、Windows Virtual Desktop Web クライアント (<https://rdweb.wvd.microsoft.com/webclient>) に移動し、ユーザー アカウントを使用してサインインします。

>[!NOTE]
>Azure Resource Manager 統合で Windows Virtual Desktop Spring 2020 リリースを使用している場合は、代わりにお使いのリソース (<https://rdweb.wvd.microsoft.com/arm/webclient>) に接続します。

>[!NOTE]
>Windows Virtual Desktop に使用するアカウントとは別の Azure Active Directory アカウントで既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

サインインすると、リソースの一覧が表示されます。 リソースを起動するには、 **[すべてのリソース]** タブで通常のアプリと同様にそのリソースを選択します。

## <a name="next-steps"></a>次のステップ

Web クライアントの使用方法の詳細については、「[Web クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)」を参照してください。
