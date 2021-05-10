---
title: Web クライアントを使用して Windows Virtual Desktop に接続する - Azure
description: Web クライアントを使用して Windows Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725010"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Web クライアントを使用して Windows Virtual Desktop に接続する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/connect-web-2019.md)を参照してください。

Web クライアントを利用すると、時間のかかるインストール プロセスなしで Web ブラウザーから Windows Virtual Desktop リソースにアクセスできます。

>[!NOTE]
>現在、Web クライアントにはモバイル OS のサポートがありません。

## <a name="supported-operating-systems-and-browsers"></a>サポートされているオペレーティング システムとブラウザー

任意の HTML5 対応ブラウザーが動作しますが、正式にサポートしているオペレーティング システムとブラウザーは次のとおりです。

| Browser           | サポート対象 OS                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | バージョン 11 以降 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | バージョン 55 以降 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>リモート リソース フィードにアクセスする

ブラウザーで、Azure Resource Manager と統合されたバージョンの Windows Virtual Desktop Web クライアント (<https://rdweb.wvd.microsoft.com/arm/webclient>) に移動して、ユーザー アカウントを使ってサインインします。

>[!NOTE]
>Azure Resource Manager 統合なしで Windows Virtual Desktop (クラシック) を使用している場合は、代わりにお使いのリソース (<https://rdweb.wvd.microsoft.com/webclient>) に接続します。
>
> US Gov ポータルを使用している場合、<https://rdweb.wvd.azure.us/arm/webclient/index.html> を使用します。

>[!NOTE]
>Windows Virtual Desktop に使用するアカウントとは別の Azure Active Directory アカウントで既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

サインインすると、リソースの一覧が表示されます。 リソースを起動するには、 **[すべてのリソース]** タブで通常のアプリと同様にそのリソースを選択します。

## <a name="next-steps"></a>次のステップ

Web クライアントの使用方法の詳細については、「[Web クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)」を参照してください。
