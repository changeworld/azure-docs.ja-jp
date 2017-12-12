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
ms.openlocfilehash: 54038785f513e56b07f5f3fafa3dbd6d4b6e7400
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 既知の問題とトラブルシューティング ガイド 
この記事は、Azure Machine Learning Workbench アプリケーションの使用の一環として発生したエラーや障害を見つけて修正するのに役立ちます。 

## <a name="find-the-workbench-build-number"></a>ワークベンチのビルド番号を調べる
サポート チームとやり取りするときは、Workbench アプリのビルド番号を確認しておくことが重要です。 Windows では、**[ヘルプ]** メニューをクリックし、**[About Azure ML Workbench]\(Azure ML Workbench のバージョン情報\)** を選択することで、ビルド番号を調べることができます。 Macos では、**[Azure ML Workbench]** メニューをクリックし、**[About Azure ML Workbench]\(Azure ML Workbench のバージョン情報\)** を選択します。

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

### <a name="app-update"></a>アプリの更新 
#### <a name="no-update-notification-on-windows-desktop"></a>Windows デスクトップで更新が通知されない 
この問題は、今後の更新で対処されます。 その間の回避策として、タスク バーにピン留めされたショートカットからはアプリを起動しないようにします。 代わりに、[スタート] メニュー、スタートの検索バー、またはデスクトップ上のショートカット (ある場合) を使用することでアプリを起動します。 

#### <a name="no-update-notification-on-an-ubuntu-data-sciece-virtual-machine-dsvm"></a>Ubuntu データ サイエンス仮想マシン (DSVM) で更新が通知されない
次の手順を実行して、最新のアプリケーションをダウンロードします。   
   - フォルダー \Users\AppData\Local\amlworkbench を削除する
   - スクリプト `c:\dsvm\tools\setup\InstallAMLFromLocal.ps1` を削除する
   - 上のスクリプトを起動するデスクトップ ショートカットを削除する
   - [https://aka.ms/azureml-wb-msi](https://aka.ms/azureml-wb-msi) を使用して正常にインストールする

### <a name="workbench-desktop-app"></a>Workbench デスクトップ アプリ
ログインで問題が発生した場合、または Workbench デスクトップ アプリがクラッシュした場合、ログ ファイルは次の場所にあります。
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

- HDInsight クラスターをコンピューティング ターゲットとして使用する場合は、プライマリ ストレージとして Azure BLOB を使用する必要があります。 Azure Data Lake Storage の使用はサポートされていません。

- Mac では、テキストのクラスタリング変換はサポートされていません。

- RevoScalePy ライブラリは、Windows または Linux (Docker コンテナー) でのみサポートされています。 macOS ではサポートされていません。

## <a name="cant-update-workbench"></a>Workbench を更新できない
新しい更新が使用できる場合、Workbench アプリのホームページには、その新しい更新に関する情報を通知するメッセージが表示されます。 アプリの左下隅にあるベル アイコンに更新バッジが表示されます。 そのバッジをクリックし、インストーラー ウィザードに従って更新をインストールします。 

![更新イメージ](./media/known-issues-and-troubleshooting-guide/update.png)

通知が表示されない場合は、アプリの再起動を試みてください。 再起動してもまだ更新通知が表示されない場合は、いくつかの原因が考えられます。

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>タスク バーのピン留めされたショートカットから Workbench を起動している
既に更新をインストールしている可能性があります。 しかし、ピン留めされたショートカットが、まだディスク上の古いビットを指しています。 これは、`%localappdata%/AmlWorkbench` フォルダーを参照して、そこに最新バージョンがインストールされているかどうかを確認し、ピン留めされたショートカットのプロパティを調べてそれがどこを指しているかを確認することによって検証できます。 検証された場合は、単純に古いショートカットを削除し、[スタート] メニューから Workbench を起動します。さらに、必要に応じて、タスク バーに新しいピン留めされたショートカットを作成します。

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Windows DSVM の [install Azure ML Workbench] (Azure ML Workbench のインストール) リンクを使用して Workbench をインストールした
残念ながら、この問題の簡単な解決策はありません。 インストールされているビットを削除し、最新のインストーラーをダウンロードして Workbench を新規インストールするために、次の手順を実行する必要があります。 
   - フォルダー `C:\Users\<Username>\AppData\Local\amlworkbench` を削除する
   - スクリプト `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1` を削除する
   - 上のスクリプトを起動するデスクトップ ショートカットを削除する
   - インストーラー https://aka.ms/azureml-wb-msi をダウンロードし、再インストールする。

## <a name="get-stuck-at-checking-experimentation-account-screen-after-logging-in"></a>ログイン後、[Checking experimentation accoun]\(実験アカウントをチェックしています\) 画面から先に進まない
ログイン後、Workbench アプリで回転する歯車が表示され、[Checking experimentation accoun]\(実験アカウントをチェックしています\) というメッセージが表示された空白の画面のままになることがあります。 これを解決するには、次の手順をお試しください。
1. アプリをシャットダウンする
2. 次のファイルを削除する
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. アプリを再起動します。

## <a name="cant-delete-experimentation-account"></a>実験アカウントを削除できない
CLI を使用して実験アカウントを削除できますが、最初に、子ワークスペースとその子ワークスペース内の子プロジェクトを削除する必要があります。 そうしないと、エラーが表示されます。

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Workbench アプリからプロジェクトとワークスペースを削除することもできます。

## <a name="cant-open-file-if-project-is-in-onedrive"></a>プロジェクトが OneDrive に存在する場合はファイルを開けない
Windows 10 Fall Creators Update を使用しており、プロジェクトが OneDrive にマッピングされたローカル フォルダー内に作成されている場合は、Workbench でどのファイルも開けないことに気付く可能性があります。 これは、OneDrive フォルダー内で node.js コードを失敗させる、Fall Creators Update によって導入されたバグのためです。 このバグは Windows Update によってすぐに修正されますが、それまでは OneDrive フォルダー内にプロジェクトを作成しないでください。

## <a name="file-name-too-long-on-windows"></a>Windows では長すぎるファイル名
Windows で Workbench を使用している場合、既定の最大 260 文字のファイル名の長さ制限に達することがあります。これにより、"指定されたパスが見つかりません" というエラーが表示される可能性があります。 さらに長いファイル パス名が許可されるようにレジストリ キーの設定を変更できます。 _MAX_PATH_ レジストリ キーを設定する方法の詳細については、[この記事](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath)を参照してください。

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
