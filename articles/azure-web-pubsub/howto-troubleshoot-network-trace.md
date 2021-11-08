---
title: ネットワーク トレースの収集方法
description: トラブルシューティングに役立つネットワーク トレースを取得する方法について学習する
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 48209fc5f4876c097d30ce6bcf464ba40ee94a7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997611"
---
# <a name="how-to-collect-a-network-trace"></a>ネットワーク トレースの収集方法

問題が発生した場合、ネットワーク トレースにより、多くの役立つ情報が得られることがあります。 このハウツー ガイドでは、ネットワーク トレースを収集するためのオプションを示します。

> [!WARNING]
> ネットワーク トレースには、アプリによって送信されるすべてのメッセージの完全な内容が含まれています。 運用アプリから GitHub などのパブリック フォーラムに未加工のネットワーク トレースを投稿 **しないでください**。

## <a name="collect-a-network-trace-with-fiddler"></a>Fiddler でネットワーク トレースを収集する

Fiddler は、HTTP トレースを収集するための強力なツールです。 [telerik.com/fiddler](https://www.telerik.com/fiddler) からインストールして起動し、アプリを実行して問題を再現します。 Fiddler は、Windows、macOS、および Linux で使用できます。 

HTTPS を使用して接続する場合は、Fiddler で確実に HTTPS トラフィックの暗号を解除できるようにするための追加の手順がいくつかあります。 詳細については、[Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。

トレースを収集したら、メニュー バーから **[ファイル]**  >  **[保存]**  >  **[すべてのセッション]** の順に選択することで、そのトレースをエクスポートできます。

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>tcpdump でネットワーク トレースを収集する (macOS および Linux のみ)

この方法はすべてのアプリで使用できます。

tcpdump を使用して未加工の TCP トレースを収集できます。その場合、コマンド シェルから次のコマンドを実行します。 アクセス許可エラーが発生した場合は、`root` になるか、コマンドの前に `sudo` を入力する必要があることがあります。

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]` は、取り込むネットワーク インターフェイスに置き換えます。 通常、これは `/dev/eth0` (標準イーサネット インターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。 詳細については、ホスト システムの `tcpdump` man ページを参照してください。

```console
man tcpdump
```

## <a name="collect-a-network-trace-in-the-browser-browser-based-apps-only"></a>ブラウザーでネットワーク トレースを収集する (ブラウザーベースのアプリのみ)

ほとんどのブラウザー開発者ツールには [ネットワーク] タブがあります。これを使用して、ブラウザーとサーバーの間のネットワーク アクティビティを取り込むことができます。 

### <a name="microsoft-edge-chromium"></a>Microsoft Edge (Chromium)

1. [DevTools](/microsoft-edge/devtools-guide-chromium/) を開きます
    * [`F12`] を選択します 
    * `Ctrl`+`Shift`+`I` \(Windows または Linux\) または `Command`+`Option`+`I` \(macOS\) を選択します
    * [`Settings and more`]、[`More Tools > Developer Tools`] の順に選択します  
1. [`Network`] タブを選択します
1. (必要に応じて) ページを更新し、問題を再現します
1. ツール バーの [`Export HAR...`] を選択し、トレースを "HAR" ファイルとしてエクスポートします

    :::image type="content" source="./media/howto-troubleshoot-network-trace/edge-export-network-trace.png" alt-text="Microsoft Edge でネットワーク トレースを収集する":::

### <a name="google-chrome"></a>Google Chrome

1. [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) に関するページを開きます
    * [`F12`] を選択します 
    * `Ctrl`+`Shift`+`I` \(Windows または Linux\) または `Command`+`Option`+`I` \(macOS\) を選択します  
    * [`Customize and control Google Chrome`]、[`More Tools > Developer Tools`] の順に選択します
1. [`Network`] タブを選択します
1. (必要に応じて) ページを更新し、問題を再現します
1. ツール バーの [`Export HAR...`] を選択し、トレースを "HAR" ファイルとしてエクスポートします

    :::image type="content" source="./media/howto-troubleshoot-network-trace/chrome-export-network-trace.png" alt-text="Google Chrome でネットワーク トレースを収集する":::

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. [Firefox の開発者ツール](https://developer.mozilla.org/en-US/docs/Tools)に関するページを開きます
    * [`F12`] を選択します
    * `Ctrl`+`Shift`+`I` \(Windows または Linux\) または `Command`+`Option`+`I` \(macOS\) を選択します 
    * [`Open menu`]、[`Web Developer > Toggle Tools`] の順に選択します
1. [`Network`] タブを選択します
1. (必要に応じて) ページを更新し、問題を再現します
1. 要求の一覧内で任意の場所を右クリックし、[Save All As HAR]\(すべてを HAR として保存\) を選びます

    :::image type="content" source="./media/howto-troubleshoot-network-trace/firefox-export-network-trace.png" alt-text="Mozilla Firefox でネットワーク トレースを収集する":::

### <a name="safari"></a>Safari

1. [Web の開発ツール](https://developer.apple.com/safari/tools/)に関するページを開きます
    * `Command`+`Option`+`I` キーを選択します
    * [`Developer`] メニュー、[`Show Web Inspector`] の順に選択します 
1. [`Network`] タブを選択します
1. (必要に応じて) ページを更新し、問題を再現します
1. 要求の一覧内で任意の場所を右クリックし、[Save All As HAR]\(すべてを HAR として保存\) を選びます