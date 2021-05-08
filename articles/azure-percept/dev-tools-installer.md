---
title: Azure Percept Dev Tools Pack Installer の概要
description: Dev Tools Pack Installer を使用して Azure Percept による高度な開発を促進する方法について説明します。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608597"
---
# <a name="dev-tools-pack-installer-overview"></a>Dev Tools Pack Installer の概要

Dev Tools Pack Installer は、高度なインテリジェント エッジ ソリューションを開発するのに必要なすべてのツールをインストールして構成するワンストップ ソリューションです。

## <a name="mandatory-tools"></a>必須のツール

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 以降](https://www.python.org/)
* [Docker 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>オプションのツール

* [Nvidia DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Nvidia アクセラレータ用ソリューションを開発するためのツールキット)
* [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/) (Intel アクセラレータ用ソリューションを開発するためのツールキット)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) または 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5.2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>既知の問題

- Docker が正常に動作していない場合、オプションの Caffe のインストールは失敗する可能性があります。 Caffe をインストールする場合、Dev Tools Pack Installer を使用して Caffe のインストールを試行する前に、Docker がインストールされ、動作していることを確認してください。

- 互換性のないシステムでは、オプションの CUDA のインストールは失敗します。 Dev Tools Pack Installer を使用して [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) をインストールする前に、システムの互換性を確認してください。

## <a name="docker-minimum-requirements"></a>Docker の最小要件

### <a name="windows"></a>Windows

- Windows 10 64 ビット: Pro、Enterprise、または Education (ビルド 16299 以降)

- Windows の機能である Hyper-V と Containers が有効になっている必要があります。 Windows 10 で Hyper-V を正常に実行するには、次のハードウェアの前提条件が必要になります。

    - [第 2 レベルのアドレス変換 (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation) を備えた 64 ビット プロセッサ
    - 4 GB のシステム RAM
    - BIOS レベルのハードウェア仮想化のサポートが BIOS の設定で有効になっている必要があります。 詳細については、仮想化に関するページを参照してください。

> [!NOTE]
> Docker は、Microsoft の Windows 10 オペレーティング システムのサポート ライフサイクルに基づいて、Windows 上の Docker Desktop をサポートしています。 詳細については、[Windows ライフサイクルのファクト シート](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)に関するページを参照してください。

[Windows への Docker Desktop のインストール](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows)について詳細を確認してださい。

### <a name="mac"></a>Mac

- Mac は、次の属性を持つ 2010 以降のモデルである必要があります。
    - Intel プロセッサ
    - Extended Page Tables (EPT) や無制限モードなど、メモリ管理ユニット (MMU) の仮想化に関する Intel のハードウェアのサポート。 このサポートが自分のマシンに備わっているかどうかを確認するには、ターミナルで次のコマンドを実行します: ```sysctl kern.hv_support```。 Mac でハイパーバイザー フレームワークがサポートされている場合は、このコマンドによって ```kern.hv_support: 1``` が出力されます。

- macOS バージョン 10.14 以降 (Mojave、Catalina、または Big Sur)。 macOS の最新バージョンにアップグレードすることをお勧めします。 macOS をバージョン 10.15 にアップグレードした後も問題が発生する場合は、このバージョンの macOS との互換性のために Docker Desktop の最新バージョンをインストールする必要があります。

- 少なくとも 4 GB の RAM。

- バージョン 4.3.30 より前の VirtualBox は、Docker Desktop と互換性がないため、インストールしないでください。

- インストーラーは、Apple M1 ではサポートされていません。

[Mac への Docker Desktop のインストール](https://docs.docker.com/docker-for-mac/install/#system-requirements)について詳細を確認してださい。

## <a name="launch-the-installer"></a>インストーラーを起動します

[Windows](https://go.microsoft.com/fwlink/?linkid=2132187)、[Linux](https://go.microsoft.com/fwlink/?linkid=2132186)、または [Mac](https://go.microsoft.com/fwlink/?linkid=2132296) 用の Dev Tools Pack Installer をダウンロードします。 下の説明に従い、お使いのプラットフォームに対応したインストーラーを起動します。

### <a name="windows"></a>Windows

1. **Dev-Tools-Pack-Installer** をクリックして、インストール ウィザードを開きます。

### <a name="mac"></a>Mac

1. ダウンロード後、**Dev-Tools-Pack-Installer.app** ファイルを **Applications** フォルダーに移動します。

1. **Dev-Tools-Pack-Installer.app** をクリックしてインストール ウィザードを開きます。

1. "開発元が未確認" のセキュリティ ダイアログが表示された場合:

    1. **[システム環境設定]**  ->  **[セキュリティとプライバシー]**  ->  **[一般]** に移動し、**Dev-Tools-Pack-Installer.app** の横にある **[Open Anyway]\(このまま開く\)** をクリックします。
    1. Electron アイコンをクリックします。
    1. セキュリティ ダイアログで **[開く]** をクリックします。

### <a name="linux"></a>Linux

1. ブラウザーでメッセージが表示されたら、 **[保存]** をクリックしてインストーラーのダウンロードを完了します。

1. **.appimage** ファイルに実行権限を追加します。

    1. Linux ターミナルを開きます。

    1. ターミナルで次のように入力して、**Downloads** フォルダーに移動します。

        ```bash
        cd ~/Downloads/
        ```

    1. AppImage を実行可能にします。

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. インストーラーを実行します。

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. **.appimage** ファイルに実行権限を追加します。

    1. .appimage ファイルを右クリックし、 **[プロパティ]** を選択します。
    1. **[アクセス許可]** タブを開きます。
    1. **[プログラムとしてのファイルの実行を許可する]** の横にあるチェック ボックスをオンにします。
    1. **[プロパティ]** を閉じて、 **.appimage** ファイルを開きます。

## <a name="run-the-installer"></a>インストーラーを実行する

1. **[Install Dev Tools Pack Installer]\(Dev Tools Pack Installer のインストール\)** ページで、 **[View license]\(ライセンスの表示\)** をクリックして、このインストーラーに含まれる各ソフトウェア パッケージのライセンス契約を表示します。 ライセンス契約の条項に同意する場合は、このチェック ボックスをオンにし、 **[Next]\(次へ\)** をクリックします。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="インストーラーのライセンス契約画面。":::

1. **[Privacy Statement]\(プライバシーに関する声明\)** をクリックして、Microsoft プライバシーに関する声明を確認します。 プライバシーに関する声明に同意し、診断データを Microsoft に送信する場合は、 **[Yes]\(はい\)** を選択して、 **[Next]\(次へ\)** をクリックします。 それ以外の場合は、 **[No]\(いいえ\)** を選択して、 **[Next]\(次へ)** をクリックします。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="インストーラーのプライバシーに関する声明の同意画面。":::

1. **[Configure Components]\(コンポーネントの構成)** ページで、インストールするオプションのツールを選択します (必須のツールは既定でインストールされます)。

    1. Azure Percept DK に含まれている Azure Percept Audio SoM を使用している場合は、必ず Intel OpenVino Toolkit と Miniconda3 をインストールしてください。

    1. **[Install]\(インストール\)** をクリックしてインストールを続行します。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="入手可能なソフトウェア パッケージを示すインストーラー画面。":::

1. 選択したすべてのコンポーネントが正常にインストールされると、ウィザードは **[Completing the Setup Wizard]\(セットアップ ウィザードの完了\)** ページに進みます。 **[Finish]\(完了\)** をクリックして、ウィザードを終了します。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="インストーラーの完了画面。":::

## <a name="docker-status-check"></a>Docker の状態の確認

Docker Desktop が良好な状態で実行されていることを確認するようにインストーラーから通知された場合は、次の手順を参照してください。

### <a name="windows"></a>Windows

1. システム トレイの非表示アイコンを展開します。

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="システム トレイ。":::

1. Docker Desktop アイコンで **[Docker Desktop is Running]\(Docker Desktop が実行中\)** と示されていることを確認します。

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker の状態。":::

1. 上のアイコンがシステム トレイに表示されていない場合は、[スタート] メニューから Docker Desktop を起動します。

1. Docker から再起動するようメッセージが表示された場合は、再起動が完了し、Docker の状態が実行中になった後に、インストーラーを閉じてから再開しても問題はありません。 正常にインストールされたサードパーティ製アプリケーションは検出されるため、自動的に再インストールされることはありません。

## <a name="next-steps"></a>次のステップ

Azure Percept DK 向けの高度な開発を始めるには、「[Azure Percept の高度な開発リポジトリ](https://github.com/microsoft/azure-percept-advanced-development)」を参照してください。
