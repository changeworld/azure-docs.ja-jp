---
title: "既知の問題とトラブルシューティング ガイド | Microsoft Docs"
description: "既知の問題の一覧とトラブルシューティングに役立つガイド"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: f79910ba4fea81e88fce90dd67ba7cb4db2e8220
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 既知の問題とトラブルシューティング ガイド 
この記事は、Azure Machine Learning Workbench アプリケーションの使用の一環として発生したエラーや障害を見つけて修正するのに役立ちます。 

> [!IMPORTANT]
> サポート チームとやり取りするときは、ビルド番号を確認しておくことが重要です。 アプリのビルド番号を調べるには、**[ヘルプ]** メニューをクリックします。 ビルド番号をクリックすると、クリップボードにコピーされます。 これを電子メールやサポート フォーラムに貼り付けると、問題のレポートに役立ちます。

![バージョン番号の検査](media/known-issues-and-troubleshooting-guide/buildno.png)

## <a name="machine-learning-msdn-forum"></a>Machine Learning MSDN フォーラム
MSDN フォーラムに質問を投稿できます。 製品チームは、このフォーラムを積極的に監視しています。 フォーラムの URL は [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum) です。 

## <a name="gather-diagnostics-information"></a>診断情報を収集する
サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 ここにログ ファイルが保存されます。

### <a name="installer"></a>インストーラー
インストール中に問題が発生した場合、インストーラーのログ ファイルは以下の場所にあります。

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
これらのディレクトリの内容を zip で圧縮し、Microsoft に診断を送信してください。

### <a name="workbench-desktop-app"></a>Workbench デスクトップ アプリ
Workbench デスクトップ アプリがクラッシュした場合、ログ ファイルは次の場所にあります。
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
これらのディレクトリの内容を zip で圧縮し、Microsoft に診断を送信してください。

### <a name="experiment-execution"></a>実験の実行
デスクトップ アプリからの送信中に特定のスクリプトが失敗する場合は、CLI で `az ml experiment submit` コマンドを使用して再送信してください。 その結果、JSON 形式の完全なエラー メッセージが返されます。このメッセージで最も重要な点は、**操作 ID** 値が含まれていることです。 **操作 ID** を含む JSON ファイルを送信していただけると、診断に役立ちます。 

特定のスクリプトが送信に実行し、実行に失敗する場合は、その特定の実行を識別する**実行 ID** が出力されます。 次のコマンドを使用して、関連するログ ファイルをパッケージ化できます。

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` コマンドにより、プロジェクトのルート フォルダーに `diagnostics.zip` ファイルが生成されます。 ZIP パッケージには、実行時の状態のプロジェクト フォルダー全体とログ情報が含まれています。 診断ファイルを送る前に、ファイルに含めるのは望ましくない機密情報を必ず削除してください。

## <a name="send-us-a-frown-or-a-smile"></a>問題点、改善点 (または気に入った機能) を報告する

Azure ML Workbench での作業中に、アプリケーション シェルの左下隅にあるスマイル アイコンをクリックすることで、問題点、改善点 (または気に入った機能) を報告することができます。 必要に応じて、(サポート チームが返信できるように) メール アドレスや現在の状態のスクリーンショットを含めることもできます。 

## <a name="known-service-limits"></a>サービスの既知の制限
- プロジェクト フォルダー サイズの上限: 25 MB。
    >[!NOTE]
    >この制限は、`.git`、`docs`、`outputs` の各フォルダーには適用されません。 これらのフォルダー名では大文字と小文字が区別されます。 大きなファイルを処理する場合は、「[Persisting Changes and Deal with Large Files (大きなファイルの変更の永続化と処理)](how-to-read-write-files.md)」をご覧ください。

- 実験実行時間の上限: 7 日間
- 実行時に `outputs` フォルダーで追跡されるファイルの最大サイズ: 512 MB
  - スクリプトによって、outputs フォルダーに 512 MB を超えるファイルが生成された場合、フォルダーでそのファイルは収集されません。 大きなファイルを処理する場合は、「[Persisting Changes and Deal with Large Files (大きなファイルの変更の永続化と処理)](how-to-read-write-files.md)」をご覧ください。

- SSH キーは、リモート マシンまたは SSH 経由の Spark クラスターに接続しているときにはサポートされません。 現在サポートされているのは、ユーザー名/パスワード モードだけです。

- Mac では、テキストのクラスタリング変換はサポートされていません。

- RevoScalePy ライブラリは、Windows または Linux (Docker コンテナー) でのみサポートされています。 macOS ではサポートされていません。

## <a name="docker-error-read-connection-refused"></a>Docker エラー "read: connection refused"
ローカル Docker コンテナーに対して実行しているときに、次のエラーが表示される場合があります。 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

このエラーを修正するには、Docker DNS サーバーを `automatic` から固定値の `8.8.8.8` に変更します。

## <a name="remove-vm-execution-error-no-tty-present"></a>VM の実行エラー "no tty present" を排除する
Linux リモート マシンの Docker コンテナーに対して実行しているときに、次のエラー メッセージが表示される場合があります。
```
sudo: no tty present and no askpass program specified.
``` 
これは、Azure Portal を使用して Ubuntu Linux VM のルート パスワードを変更した場合に発生する可能性があります。 

Azure Machine Learning Workbench をリモート ホストで実行するには、パスワードのない sudoers アクセスが必要になります。 これを行う最も簡単な方法は、_visudo_ を使用して次のファイルを編集することです (このファイルが存在しない場合は作成できます)。

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>別のコマンドではなく、_visudo_ を使用してファイルを編集することが重要です。 _visudo_ では、すべての sudo 構成ファイルの構文チェックが自動的に実行されます。構文的に正しい sudoers ファイルを生成できない場合、sudo からロックアウトされる可能性があります。

ファイルの末尾に次の行を挿入します。

```
username ALL=(ALL) NOPASSWD:ALL
```

_username_ は、リモート ホストにログインする際に使用する Azure Machine Learning Workbench の名前です。

この行は、#includedir "/etc/sudoers.d" の後に配置する必要があります。そうしないと、別の規則によって上書きされる場合があります。

より複雑な sudo 構成がある場合、使用可能な Ubuntu については、sudo のドキュメント (https://help.ubuntu.com/community/Sudoers) を参照してください。

上記のエラーは、Azure で実行対象として Ubuntu ベースの Linux VM を使用していない場合にも発生する可能性があります。 リモート実行でサポートされているのは、Ubuntu ベースの Linux VM だけです。 

## <a name="vm-disk-is-full"></a>VM ディスクがいっぱいになっている
既定では、Azure に新しい Linux VM を作成すると、オペレーティング システム用に 30 GB のディスクが提供されます。 Docker エンジンでは、イメージのプルとコンテナーの実行に既定でこの OS ディスクを使用します。 これにより、OS ディスクがいっぱいになる可能性があります。その場合、"VM Disk is Full"\(VM ディスクがいっぱいです\) というエラーが表示されます。

これをすばやく修正するには、使用されなくなった Docker イメージをすべて削除します。 そのためには、次の Docker コマンドを実行します  (Bash シェルから Docker コマンドを実行するために、VM に SSH 接続する必要があります)。

```
$ docker system prune -a
```

データ ディスクを追加し、そのデータ ディスクをイメージの格納に使用するように Docker エンジンを構成することもできます。 データ ディスクを追加する方法については、[こちら](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)をご覧ください。 その後、[Docker がイメージを格納する場所を変更](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)できます。

または、OS ディスクを拡張することもできます。Docker エンジン構成に手を加える必要はありません。 OS ディスクを拡張する方法については、[こちら](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)をご覧ください。

## <a name="sharing-c-drive-on-windows"></a>Windows の C ドライブを共有する
Windows のローカル Docker コンテナーで実行している場合、`aml_config` の `docker.compute` ファイルで `sharedVolumes` を `true` に設定すると、実行のパフォーマンスを向上させることができます。 ただし、そのためには、"_Docker for Windows ツール_" で C ドライブを共有する必要があります。 C ドライブを共有できない場合は、次のヒントを試してください。

* ファイル エクスプローラーを使用して、C ドライブ上の共有を検査します
* ネットワーク アダプターの設定を開き、vEthernet の "Microsoft ネットワーク用ファイルとプリンター共有" をアンインストール/再インストールします
* Docker の設定を開き、Docker の設定から C ドライブを共有します
* Windows パスワードの変更は共有に影響します。 ファイル エクスプローラーを開き、C ドライブをもう一度共有し、新しいパスワードを入力します。
* C ドライブを Docker と共有しようとするときに、ファイアウォールの問題が発生する可能性もあります。 この [Stack Overflow の投稿](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)が役立つ可能性があります。
* ドメインの資格情報を使用して C ドライブを共有する場合、ドメイン コントローラーにアクセスできないネットワーク (たとえば、ホーム ネットワークやパブリック wifi など) で共有が動作を停止することがあります。 詳細については、[こちらの投稿](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)をご覧ください。

`docker.compute` ファイルで `sharedVolumne` を `false` に設定することによって、わずかなパフォーマンス コストで共有の問題を回避することもできます。

## <a name="some-useful-docker-commands"></a>便利な Docker コマンド

いくつかの便利な Docker コマンドを次に示します。

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```