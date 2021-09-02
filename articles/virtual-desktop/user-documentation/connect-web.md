---
title: Web クライアントを使用して Azure Virtual Desktop に接続する - Azure
description: Web クライアントを使用して Azure Virtual Desktop に接続する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0c6e616b63587f46d9dfe73066147de428d0ee0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469191"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>Web クライアントを使用して Azure Virtual Desktop に接続する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](../virtual-desktop-fall-2019/connect-web-2019.md)を参照してください。

Web クライアントを使用すると、時間のかかるインストール プロセスなしで Web ブラウザーから Azure Virtual Desktop リソースにアクセスできます。

>[!NOTE]
>現在、Web クライアントにはモバイル OS のサポートがありません。

## <a name="supported-operating-systems-and-browsers"></a>サポートされているオペレーティング システムとブラウザー
>[!IMPORTANT]
>2021 年 9 月 30 日以降、Azure Virtual Desktop Web クライアントで Internet Explorer 11 はサポートされなくなります。 Internet Explorer 11 の代わりに、Microsoft Edge ブラウザーの使用に移行することをお勧めします。 詳細については、Tech Community の[ブログ記事](https://aka.ms/WVDSupportIE11)を参照してください。

任意の HTML5 対応ブラウザーが動作しますが、正式にサポートしているオペレーティング システムとブラウザーは次のとおりです。

| Browser           | サポート対象 OS                     | Notes               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | バージョン 11 以降 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | バージョン 55 以降 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>リモート リソース フィードにアクセスする

ブラウザーで、Azure Resource Manager 統合バージョンの Azure Virtual Desktop Web クライアント (<https://rdweb.wvd.microsoft.com/arm/webclient>) に移動し、ユーザー アカウントでサインインします。

>[!NOTE]
>Azure Resource Manager と統合されていない Azure Virtual Desktop (クラシック) を使用している場合は、代わりにお使いのリソース (<https://rdweb.wvd.microsoft.com/webclient>) に接続します。
>
> US Gov ポータルを使用している場合、<https://rdweb.wvd.azure.us/arm/webclient/index.html> を使用します。
> 
> Azure China Cloud に接続するには、<https://rdweb.wvd.azure.cn/arm/webclient/index.html> を使用します。

>[!NOTE]
>Azure Virtual Desktop に使用するアカウントとは別の Azure Active Directory アカウントで既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

サインインすると、リソースの一覧が表示されます。 リソースを起動するには、 **[すべてのリソース]** タブで通常のアプリと同様にそのリソースを選択します。

## <a name="next-steps"></a>次のステップ

Web クライアントの使用方法の詳細については、「[Web クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)」を参照してください。
