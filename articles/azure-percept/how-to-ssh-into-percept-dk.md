---
title: Azure Percept DK に SSH で接続する
description: PuTTY を使用して Azure Percept DK に SSH 接続する方法について説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721479"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Azure Percept DK に SSH で接続する

OpenSSH または [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) を使用して Azure Percept DK への SSH 接続を設定するには、下の手順に従います。

## <a name="prerequisites"></a>前提条件

- Wi-Fi 機能を備えた Windows、Linux、OS X ベースのホスト コンピューター
- SSH クライアント (インストールのガイダンスについては、次のセクションを参照)
- Azure Percept DK (開発キット)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に作成された SSH ログイン

## <a name="install-your-preferred-ssh-client"></a>希望の SSH クライアントをインストールする

ホスト コンピューターで Linux または OS X が実行されている場合、SSH サービスはオペレーティング システムに含まれているため、個別のクライアント アプリケーションを使用しなくても実行できます。 SSH サービスの実行方法について詳しくは、オペレーティング システムの製品ドキュメントを確認してください。

ホスト コンピューターで Windows を実行している場合は、OpenSSH と PuTTY という 2 つの SSH クライアントの選択肢があります。

### <a name="openssh"></a>OpenSSH

Windows 10 には、OpenSSH と呼ばれる組み込みの SSH クライアントが含まれています。これは、コマンド プロンプト内で簡単なコマンドを使用して実行できます。 使用可能な場合は、Azure Percept で OpenSSH を使用することをお勧めします。 Windows コンピューターに OpenSSH がインストールされているかどうかを確認するには、こちらの手順に従います。

1. **[スタート]**  ->  **[設定]** に移動します。

1. **[アプリ]** を選択します。

1. **[アプリと機能]** で、 **[オプション機能]** を選択します。

1. **[インストールされている機能]** 検索バーに「**OpenSSH クライアント**」と入力します。 OpenSSH が表示されたら、クライアントが既にインストールされているため、次のセクションに進むことができます。 OpenSSH が表示されない場合は、 **[機能の追加]** をクリックします。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="OpenSSH のインストール状態を示す設定のスクリーンショット。":::

1. **[OpenSSH クライアント]** を選択して **[インストール]** をクリックします。 これで、次のセクションに進むことができます。 OpenSSH をコンピューターにインストールできない場合は、下の手順に従って、サードパーティの SSH クライアントである PuTTY をインストールします。

### <a name="putty"></a>PuTTY

Windows コンピューターに OpenSSH が含まれていない場合は、[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) を使用することをお勧めします。 PuTTY をダウンロードしてインストールするには、次の手順を実行します。

1. [PuTTY のダウンロード ページ](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)にアクセスします。

1. 「**Package files**」で 32 ビットまたは 64 ビットの .msi ファイルをクリックして、インストーラーをダウンロードします。 選択するバージョンがわからない場合は、[FAQ](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit) を確認してください。

1. インストーラーをクリックして、インストール プロセスを開始します。 必要に応じて、プロンプトに従います。

1. おめでとうございます。 PuTTY SSH クライアントが正常にインストールされました。

## <a name="initiate-the-ssh-connection"></a>SSH 接続を初期化する

1. Azure Percept DK の電源をオンにします。

1. 開発キットが既にイーサネットまたは Wi-Fi でネットワークに接続されている場合は、次の手順に進んでください。 そうでない場合は、開発キットの Wi-Fi アクセス ポイントにホスト コンピューターを直接接続します。 他の Wi-Fi ネットワークへの接続と同様に、お使いのコンピューターでネットワークとインターネットの設定を開き、次のネットワークをクリックして、プロンプトが表示されたらネットワーク パスワードを入力します。

    - **ネットワーク名**: 開発キットのオペレーティング システムのバージョンによりますが、Wi-Fi アクセス ポイントの名前は **scz-xxxx** または **apd-xxxx** のいずれかです ("xxxx" は開発キットの MAC アドレスの最後の 4 桁です)
    - **パスワード**: 開発キットに付属のウェルカム カードに記載されています

    > [!WARNING]
    > Azure Percept DK Wi-Fi のアクセスポイントに接続している間は、ホスト コンピューターのインターネット接続が一時的に失われます。 アクティブなビデオ会議通話や Web ストリーミングといったネットワークベースのエクスペリエンスは中断されます。

1. SSH クライアントに従って SSH 接続プロセスを完了します。

### <a name="using-openssh"></a>OpenSSH の使用

1. コマンド プロンプトを開きます ( **[スタート]**  ->  **[コマンド プロンプト]** )。

1. コマンド プロンプトに以下を入力します。

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    コンピューターが開発キットの Wi-Fi アクセス ポイントに接続されている場合、IP アドレスは 10.1.1.1 になります。 イーサネット経由で接続している場合は、デバイスのローカル IP アドレスを使用します。デバイスのローカル IP アドレスは、イーサネット ルーターまたはハブから取得できます。 開発キットが Wi-Fi で接続されている場合、[Azure Percept DK のセットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に開発キットに割り当てられた IP アドレスを使用する必要があります。

    > [!TIP]
    > 開発キットが Wi-Fi ネットワークに接続されているものの、IP アドレスがわからない場合は、Azure Percept Studio にアクセスして、[デバイスのビデオ ストリームを開きます](./how-to-view-video-stream.md)。 ビデオ ストリーム ブラウザー タブのアドレス バーに、デバイスの IP アドレスが表示されます。

1. SSH パスワードを求められたら、入力します。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="OpenSSH コマンド プロンプトのログインを示すスクリーンショット。":::

1. OpenSSH を介して開発キットに接続するのが初めてである場合は、ホストのキーを受け入れるように求められることもあります。 「**yes**」と入力して、キーを受け入れます。

1. おめでとうございます。 SSH 経由で開発キットに正常に接続しました。

### <a name="using-putty"></a>PuTTY の使用

1. PuTTY を開きます。 **[PuTTY Configuration]\(PuTTY の構成\)**  ウィンドウに以下を入力し、 **[Open]\(開く\)** をクリックして開発キットに SSH 接続します。

    1. ホスト名: [IP アドレス]
    1. ポート: 22
    1. 接続の種類: SSH

    **[Host Name]\(ホスト名\)** は、開発キットの IP アドレスです。 コンピューターが開発キットの Wi-Fi アクセス ポイントに接続されている場合、IP アドレスは 10.1.1.1 になります。 イーサネット経由で接続している場合は、デバイスのローカル IP アドレスを使用します。デバイスのローカル IP アドレスは、イーサネット ルーターまたはハブから取得できます。 開発キットが Wi-Fi で接続されている場合、[Azure Percept DK のセットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に開発キットに割り当てられた IP アドレスを使用する必要があります。

    > [!TIP]
    > 開発キットが Wi-Fi ネットワークに接続されているものの、IP アドレスがわからない場合は、Azure Percept Studio にアクセスして、[デバイスのビデオ ストリームを開きます](./how-to-view-video-stream.md)。 ビデオ ストリーム ブラウザー タブのアドレス バーに、デバイスの IP アドレスが表示されます。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="[PuTTY Configuration]\(PuTTY の構成\) ウィンドウのスクリーンショット。":::

1. PuTTY ターミナルが開きます。 プロンプトが表示されたら、SSH のユーザー名とパスワードをターミナルに入力します。

1. おめでとうございます。 SSH 経由で開発キットに正常に接続しました。

## <a name="next-steps"></a>次のステップ

SSH を介して Azure Percept DK に接続したら、[デバイスのトラブルシューティング](./troubleshoot-dev-kit.md)、[USB 更新](./how-to-update-via-usb.md)などのさまざまなタスクを実行できます。