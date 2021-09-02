---
title: Azure Percept DK にイーサネットで接続する
description: このガイドでは、イーサネット接続を介して接続したときに Azure Percept DK セットアップ エクスペリエンスに接続する方法について説明します。
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ffc2a2d55bdad670c8323dfc57efad17dec1930
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227100"
---
# <a name="connect-to-azure-percept-dk-over-ethernet"></a>Azure Percept DK にイーサネットで接続する

このハウツーガイドでは、イーサネット接続経由で Azure Percept DK セットアップ エクスペリエンスを起動する方法について説明します。 これは「[クイック スタート: Azure Percept DK を設定し、最初の AI モデルをデプロイする](./quickstart-percept-dk-set-up.md)」ガイドに関連しています。 以下で説明する各オプションを参照し、環境に最適なものを選択してください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK ([ここで取得](https://go.microsoft.com/fwlink/?linkid=2155270))
- Wi-Fi またはイーサネット機能と Web ブラウザーを備えた Windows、Linux、OS X ベースのホスト コンピューター
- ネットワーク ケーブル

## <a name="identify-your-dev-kits-ip-address"></a>開発キットの IP アドレスの特定

イーサネット接続経由で Azure Percept DK セットアップ エクスペリエンスを実行する鍵は、開発キットの IP アドレスを見つけることです。 この記事では、以下の 3 つのオプションについて説明します。
1. ネットワーク ルーターから
1. SSH 経由
1. Nmap ツール経由

### <a name="from-your-network-router"></a>ネットワーク ルーターから
開発キットの IP アドレスを特定する最も簡単な方法は、ネットワーク ルーターを調べることです。
1. イーサネット ケーブルを開発キットに接続し、もう一端をルーターに接続します。
1. Azure Percept DK の電源をオンにします。
1. ネットワーク ルーターでアクセス指示を指定するステッカーを探します。

    **ルーターのステッカーの例を次に示します。**

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="ネットワーク ルーターのステッカーの例":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="別のネットワーク ルーターのステッカーの例":::

1. イーサネットまたは Wi-fi に接続されているコンピューターで、Web ブラウザーを開きます。
1. ステッカーに記載されているルーターのブラウザー アドレスを入力します。
1. メッセージが表示されたら、ステッカーに記載されているルーターの名前とパスワードを入力します。
1. ルーター インターフェイスで、[デバイス] を選択します (またはルーターに応じて、同様のものを選択します)。
1. デバイスの一覧で Azure Percept 開発キットを見つけます
1. Azure Percept 開発キットの IP アドレスをコピーします

### <a name="via-ssh"></a>SSH 経由
SSH 経由で開発キットに接続することで、開発キットの IP アドレスを見つけることができます。

> [!NOTE]
> 開発キットの IP アドレスを識別する SSH メソッドを使用するには、開発キットの Wi-Fi アクセス ポイントに接続できる必要があります。 これが不可能な場合は、その他の方法の 1 つを使用する必要があります。

1. イーサネット ケーブルを開発キットに接続し、もう一端をルーターに接続します
1. Azure Percept 開発キットの電源をオンにします
1. SSH 経由で開発キットに接続します。 SSH 経由で開発キットに接続する方法の詳細な手順については、「[SSH 経由で Azure Percept DK に接続する](./how-to-ssh-into-percept-dk.md)」を参照してください。
1. イーサネット ローカル ネットワークの IP アドレスを一覧表示するには、SSH ターミナル ウィンドウで以下のコマンドを入力します。

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="SSH ターミナルでローカル ネットワーク IP を識別する例":::


1. 開発キットの IP アドレスが "inet" の後に表示されています。 IP アドレスをコピーします。

### <a name="using-the-nmap-tool"></a>Nmap ツールの使用
Web 上の無料ツールを使用して、開発キットの IP アドレスを特定することもできます。 この手順では、Nmap という名前のツールについて説明します。
1. イーサネット ケーブルを開発キットに接続し、もう一端をルーターに接続します。
1. Azure Percept 開発キットの電源をオンにします。
1. ホスト コンピューターで、プラットフォーム (Windows/Mac/Linux) に必要な[無料の Nmap セキュリティ スキャナー](https://nmap.org/download.html)をダウンロードしてインストールします。
1. コンピューターの "既定のゲートウェイ" を取得します - [既定のゲートウェイを検索する方法](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/)
1. Nmap アプリケーションを開きます 
1. *[ターゲット]* ボックスにデフォルト ゲートウェイを入力し、 **/24** を末尾に追加します。 *[プロファイル]* を **[クイック スキャン]** に変更し、 **[スキャン]** ボタンを選択します。
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Nmap ツール入力の例":::
 
1. 結果の中のデバイスの一覧で、Azure Percept 開発キットを見つけます (**apd-xxxxxxxx** と同様)。
1. Azure Percept 開発キットの IP アドレスをコピーします 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを起動する
1. イーサネット ケーブルを開発キットに接続し、もう一端をルーターに接続します。
1. Azure Percept 開発キットの電源をオンにします。
1. Web ブラウザーを開いて、開発キットの IP アドレスをペーストします。 セットアップ エクスペリエンスがブラウザーで起動します。

## <a name="next-steps"></a>次のステップ
- [セットアップ エクスペリエンスの完了](./quickstart-percept-dk-set-up.md)