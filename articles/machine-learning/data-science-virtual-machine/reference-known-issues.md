---
title: 'リファレンス: 既知の問題とトラブルシューティング'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine に関する既知の問題、回避策、トラブルシューティングの一覧を示します
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729805"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine の既知の問題とトラブルシューティング

この記事は、Azure Data Science Virtual Machine の使用時に発生する可能性のあるエラーや障害を見つけて修正するのに役立ちます。


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>NVIDIA A100 GPU チップの GPU を修正する - Azure NDasrv4 シリーズ 

ND A100 v4 シリーズの仮想マシンは、ハイエンドのディープ ラーニング トレーニングと密結合のスケールアップおよびスケールアウトの HPC ワークロード向けに設計された、Azure GPU ファミリに新たに追加された主力製品です。

異なるアーキテクチャにより、TensorFlow または PyTorch フレームワークを使用して GPU アクセラレーションを利用するには、要求の厳しいワークロードに対して異なるセットアップが必要です。

ND A100 マシン GPU の追加設定なしのサポートに向けて取り組んでいます。 その間、NVIDIA の Fabric Manager を追加し、ドライバーを更新することで、GPU を動作させることができます。 

ターミナルで次の簡単な手順に従います。

1. NVIDIA のリポジトリを追加してドライバーをインストールまたは更新します。詳細な手順については、[こちら](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)を参照してください。
2. [省略可能] (上記のリポジトリから) CUDA ドライバーを更新することもできます。
3. NVIDIA の Fabric Manager ドライバーをインストールします。

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. VM を再起動します (ドライバーの準備を行う)。
5. 新しくインストールされた NVIDIA Fabric Manager サービスを有効にして開始します。

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

次のコマンドを実行して、ドライバーと GPU の動作を確認できます。
```
systemctl status nvidia-fabricmanager.service
``` 

その後、![nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png) を実行している Fabric Manager サービスが表示されます。


### <a name="connection-to-desktop-environment-fails"></a>デスクトップ環境への接続が失敗する

SSH ターミナルを使用して DSVM に接続できるが、x2go を使用して接続できない場合は、x2go で間違ったセッションの種類を設定している可能性があります。
DSVM のデスクトップ環境に接続するには *x2go/session preferences/session* でセッションの種類を *XFCE* に設定する必要があります。 その他のデスクトップ環境は現在サポートされていません。

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>x2go を使用して DSVM に接続するとフォントが正しく表示されない

x2go に接続し、一部のフォントが正しく表示されない場合は、x2go のセッション設定に関連している可能性があります。 DSVM に接続する前に、セッション設定ダイアログの [入力/出力] タブの [ディスプレイ DPI の設定] チェック ボックスをオフにします。

### <a name="prompted-for-unknown-password"></a>不明なパスワードの入力を求めるメッセージ

*[認証の種類]* を *[SSH 公開キー]* (パスワード認証を使用する場合に推奨) に設定する DSVM を作成すると、パスワードは指定されません。 ただしシナリオによって、一部のアプリケーションで引き続きパスワードの入力が求められる場合があります。 `sudo passwd <user_name>` を実行して、特定のアカウントの新しいパスワードを作成します。 `sudo passwd` を使用すると、ルート ユーザーの新しいパスワードを作成できます。

これらのコマンドを実行すると、SSH の構成は変更されません。許可されたサインイン メカニズムは同じ状態に維持されます。 

### <a name="prompted-for-password-when-running-sudo-command"></a>sudo コマンドを実行するときにパスワードの入力を求められる

Ubuntu マシンで `sudo` コマンドを実行するときに、実際にログインするユーザーであることを確認するために、パスワードの入力を繰り返し求められる場合があります。 この動作は想定され、Ubuntu では既定です。 ただし、一部のシナリオでは認証を繰り返す必要がなく、煩わしい場合があります。

ほとんどの場合、再認証を無効にするには、ターミナルで次のコマンドを実行します。

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

ターミナルを再起動した後、sudo によって別のログインが要求されることはなく、セッション ログインからの認証で十分であると見なされます。

### <a name="cannot-use-docker-as-non-root-user"></a>非ルート ユーザーとして Docker を使用できない

非ルート ユーザーとして Docker を使用するには、ユーザーが Docker グループのメンバーである必要があります。 `getent group docker` コマンドを実行して、そのグループに属しているユーザーを確認できます。 ユーザーを Docker グループに追加するには、`sudo usermod -aG docker $USER` を実行します。

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>Docker コンテナーがネットワーク経由で外部と対話できない

既定では、Docker は、いわゆる "ブリッジ ネットワーク" (`172.17.0.0/16`) に新しいコンテナーを追加します。 そのブリッジ ネットワークのサブネットが DSVM のサブネットと重複している場合、またはサブスクリプション内にある別のプライベート サブネットと重複している場合は、ホストとコンテナーの間のネットワーク通信は実行できません。 その場合、コンテナーで実行されている Web アプリケーションには到達できません。また、コンテナーは apt からパッケージを更新できません。

この問題を解決するには、サブスクリプションの他のネットワークと重複しないブリッジ ネットワークに IP アドレス空間を使用するように、Docker を再構成する必要があります。 たとえば、

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

をファイル `/etc/docker/daemon.json` に含まれる JSON ドキュメントに追加すると、Docker によってブリッジ ネットワークに別のサブネットが割り当てられます (ファイルは sudo を使用して編集する必要があります。たとえば、`sudo nano /etc/docker/daemon.json` を実行します)。

変更後、`service docker restart` を実行して Docker サービスを再起動する必要があります。

変更が有効になっているかどうかを確認するには、`docker network inspect bridge` を実行します。 *IPAM.Config.Subnet* の下の値は、上で指定したアドレス プールに対応している必要があります。

### <a name="gpus-not-available-in-docker-container"></a>Docker コンテナーで GPU を使用できない

DSVM にインストールされている Docker では、既定で GPU がサポートされます。 ただし、いくつかの前提条件を満たしている必要があります。

* 当然ながら、DSVM の VM サイズには少なくとも 1 つの GPU を含める必要があります。
* `docker run` で Docker コンテナーを起動する場合は、 *--gpus* パラメーター (例: `--gpus all`) を追加する必要があります。
* NVIDIA A100 GPU を含む VM サイズには、追加のソフトウェア パッケージがインストールされている必要があります (特に [NVIDIA Fabric Manager](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf))。 これらのパッケージは、まだイメージにプレインストールされていない可能性があります。


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>SQL Server へのアクセス

プレインストールされている SQL Server インスタンスに接続しようとすると、"ログインに失敗しました" というエラーが発生することがあります。 SQL Server インスタンスに正常に接続するには、接続するプログラム (たとえば、SQL Server Management Studio (SSMS) など) を管理者モードで実行する必要があります。 DSVM の既定では、管理者だけが接続を許可されるため、管理者モードである必要があります。

### <a name="hyper-v-does-not-work"></a>Hyper-V が機能しない

Hyper-V が最初は Windows で正しく動作しないのは、想定されている動作です。 ブート パフォーマンスのために、一部のサービスを無効にしました。
Hyper-V を有効化するには、次の手順に従います。

1. Windows DSVM で検索バーを開きます
1. 「サービス」と入力します
1. すべての Hyper-V サービスを "手動" に設定します
1. "Hyper-V 仮想マシンの管理" を "自動" に設定します

最後の画面は、次のようになります。

   

![Hyper-V の有効化](./media/workaround/hyperv-enable-dsvm.png)
