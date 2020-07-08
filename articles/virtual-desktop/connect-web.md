---
title: Windows Virtual Desktop Web クライアントの接続 - Azure
description: Web クライアントを使用して Windows Virtual Desktop に接続する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb3a73566cd2477e493ca8621336fb3a51f3b3b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212464"
---
# <a name="connect-with-the-web-client"></a>Web クライアントに接続する

>[!IMPORTANT]
>このコンテンツは、Spring 2020 更新プログラムと Azure Resource Manager Windows Virtual Desktop オブジェクトの組み合わせを対象としています。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop Fall 2019 リリースを使用している場合は、[この記事](./virtual-desktop-fall-2019/connect-web-2019.md)を参照してください。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

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

ブラウザーで、Azure Resource Manager と統合されたバージョンの Windows Virtual Desktop Web クライアント (<https://rdweb.wvd.microsoft.com/arm/webclient>) に移動して、ユーザー アカウントを使ってサインインします。

>[!NOTE]
>Azure Resource Manager と統合されていない Windows Virtual Desktop Fall 2019 リリースを使用している場合は、代わりにお使いのリソース (<https://rdweb.wvd.microsoft.com/webclient>) に接続します。

>[!NOTE]
>Windows Virtual Desktop に使用するアカウントとは別の Azure Active Directory アカウントで既にサインインしている場合は、サインアウトするか、プライベート ブラウザー ウィンドウを使用する必要があります。

サインインすると、リソースの一覧が表示されます。 リソースを起動するには、 **[すべてのリソース]** タブで通常のアプリと同様にそのリソースを選択します。

## <a name="next-steps"></a>次のステップ

Web クライアントの使用方法の詳細については、「[Web クライアントの概要](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)」を参照してください。
