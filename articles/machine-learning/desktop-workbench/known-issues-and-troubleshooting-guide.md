---
title: 既知の問題とトラブルシューティング ガイド | Microsoft Docs
description: 既知の問題の一覧とトラブルシューティングに役立つガイド
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 8177808fd4d666ea04b1bc097f261c7643931704
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885049"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 既知の問題とトラブルシューティング ガイド 
この記事は、Azure Machine Learning Workbench アプリケーションの使用の一環として発生したエラーや障害を見つけて修正するのに役立ちます。 

## <a name="find-the-workbench-build-number"></a>ワークベンチのビルド番号を調べる
サポート チームとやり取りするときは、Workbench アプリのビルド番号を確認しておくことが重要です。 Windows では、**[ヘルプ]** メニューをクリックし、**[About Azure ML Workbench]\(Azure ML Workbench のバージョン情報\)** を選択することで、ビルド番号を調べることができます。 Macos では、**[Azure ML Workbench]** メニューをクリックし、**[About Azure ML Workbench]\(Azure ML Workbench のバージョン情報\)** を選択します。

## <a name="machine-learning-msdn-forum"></a>Machine Learning MSDN フォーラム
MSDN フォーラムに質問を投稿できます。 製品チームは、このフォーラムを積極的に監視しています。 フォーラムの URL は [https://aka.ms/aml-forum-service](https://aka.ms/aml-forum-service) です。 

## <a name="gather-diagnostics-information"></a>診断情報を収集する
サポートを依頼するときに診断情報を提供できると、役に立つ場合があります。 ここにログ ファイルが保存されます。

### <a name="installer-log"></a>インストーラー ログ
インストール中に問題が発生した場合、インストーラーのログ ファイルは以下の場所にあります。

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
これらのディレクトリの内容を zip で圧縮し、Microsoft に診断を送信してください。

### <a name="workbench-desktop-app-log"></a>Workbench デスクトップ アプリ ログ
ログインで問題が発生した場合、または Workbench デスクトップ アプリがクラッシュした場合、ログ ファイルは次の場所にあります。
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
これらのディレクトリの内容を zip で圧縮し、Microsoft に診断を送信してください。

### <a name="experiment-execution-log"></a>実験実行ログ
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

- Workbench アプリから Jupyter Notebook を開くときは、5 MB の最大サイズ制限があります。 大きいノートブックは CLI の "az ml notebook start" コマンドを使って開くことができ、セル出力をクリーンアップしてファイル サイズを小さくできます。

## <a name="cant-update-workbench"></a>Workbench を更新できない
新しい更新が使用できる場合、Workbench アプリのホームページには、その新しい更新に関する情報を通知するメッセージが表示されます。 アプリの左下隅にあるベル アイコンに更新バッジが表示されます。 そのバッジをクリックし、インストーラー ウィザードに従って更新をインストールします。 

![更新イメージ](../service/media/known-issues-and-troubleshooting-guide/update.png)

通知が表示されない場合は、アプリの再起動を試みてください。 再起動してもまだ更新通知が表示されない場合は、いくつかの原因が考えられます。

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>タスク バーのピン留めされたショートカットから Workbench を起動している
既に更新をインストールしている可能性があります。 しかし、ピン留めされたショートカットが、まだディスク上の古いビットを指しています。 これは、`%localappdata%/AmlWorkbench` フォルダーを参照して、そこに最新バージョンがインストールされているかどうかを確認し、ピン留めされたショートカットのプロパティを調べてそれがどこを指しているかを確認することによって検証できます。 検証された場合は、単純に古いショートカットを削除し、[スタート] メニューから Workbench を起動します。さらに、必要に応じて、タスク バーに新しいピン留めされたショートカットを作成します。

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Windows DSVM の [install Azure ML Workbench] (Azure ML Workbench のインストール) リンクを使用して Workbench をインストールした
残念ながら、この問題の簡単な解決策はありません。 インストールされているビットを削除し、最新のインストーラーをダウンロードして Workbench を新規インストールするために、次の手順を実行する必要があります。 
   - フォルダー `C:\Users\<Username>\AppData\Local\amlworkbench` を削除する
   - スクリプト `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1` を削除する
   - 上のスクリプトを起動するデスクトップ ショートカットを削除する
   - インストーラー https://aka.ms/azureml-wb-msi をダウンロードし、再インストールする。

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>ログイン後に [Checking experimentation accoun]\(実験アカウントをチェックしています\) 画面でスタックする
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
CLI を使用して実験アカウントを削除できますが、最初に、子ワークスペースとその子ワークスペース内の子プロジェクトを削除する必要があります。 そうしないと、"入れ子になっているリソースを削除する前にリソースを削除することはできません" というエラーが表示されます。

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <workspace name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <workspace name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Workbench アプリからプロジェクトとワークスペースを削除することもできます。

## <a name="cant-open-file-if-project-is-in-onedrive"></a>プロジェクトが OneDrive に存在する場合はファイルを開けない
Windows 10 Fall Creators Update を使用しており、プロジェクトが OneDrive にマッピングされたローカル フォルダー内に作成されている場合は、Workbench でどのファイルも開けないことに気付く可能性があります。 これは、OneDrive フォルダー内で node.js コードを失敗させる、Fall Creators Update によって導入されたバグのためです。 このバグは Windows Update によってすぐに修正されますが、それまでは OneDrive フォルダー内にプロジェクトを作成しないでください。

## <a name="file-name-too-long-on-windows"></a>Windows では長すぎるファイル名
Windows で Workbench を使用している場合、既定の最大 260 文字のファイル名の長さ制限に達することがあります。これにより、"指定されたパスが見つかりません" というエラーが表示される可能性があります。 さらに長いファイル パス名が許可されるようにレジストリ キーの設定を変更できます。 _MAX_PATH_ レジストリ キーを設定する方法の詳細については、[この記事](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath)を参照してください。

## <a name="interrupt-cli-execution-output"></a>CLI 実行の出力を中断する
`az ml experiment submit` または `az ml notebook start` を使って実験実行を開始して、出力を中断したい場合: 
- Windows では、キーボードの Ctrl + Break キーの組み合わせを使います
- macOS では、Ctrl + C を使います。

これは CLI ウィンドウ内の出力ストリームのみを中断することに注意してください。 実行されているジョブを実際に停止することはありません。 実行中のジョブをキャンセルする場合は、`az ml experiment cancel -r <run_id> -t <target name>` コマンドを使います。

キーボードに Break キーがない Windows コンピューターでは、Fn + B、Ctrl + Fn + B、または Fn + Esc を代わりに使うことができます。特定のキーの組み合わせについては、ハードウェア ベンダーのドキュメントをご覧ください。

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

この行は、#includedir "/etc/sudoers.d" の後に配置する必要があります。そうしないと、別の規則によってオーバーライドされる場合があります。

より複雑な sudo 構成がある場合、使用可能な Ubuntu については、sudo のドキュメント (https://help.ubuntu.com/community/Sudoers) を参照してください。

上記のエラーは、Azure で実行対象として Ubuntu ベースの Linux VM を使用していない場合にも発生する可能性があります。 リモート実行でサポートされているのは、Ubuntu ベースの Linux VM だけです。 

## <a name="vm-disk-is-full"></a>VM ディスクがいっぱいになっている
既定では、Azure に新しい Linux VM を作成すると、オペレーティング システム用に 30 GB のディスクが提供されます。 Docker エンジンでは、イメージのプルとコンテナーの実行に既定でこの OS ディスクを使用します。 これにより、OS ディスクがいっぱいになる可能性があります。その場合、"VM Disk is Full"\(VM ディスクがいっぱいです\) というエラーが表示されます。

これをすばやく修正するには、使用されなくなった Docker イメージをすべて削除します。 そのためには、次の Docker コマンドを実行します  (Bash シェルから Docker コマンドを実行するために、VM に SSH 接続する必要があります)。

```
$ docker system prune -a
```

データ ディスクを追加し、そのデータ ディスクをイメージの格納に使用するように Docker エンジンを構成することもできます。 データ ディスクを追加する方法については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)をご覧ください。 その後、[Docker がイメージを格納する場所を変更](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)できます。

または、OS ディスクを拡張することもできます。Docker エンジン構成に手を加える必要はありません。 OS ディスクを拡張する方法については、[こちら](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks)をご覧ください。

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Windows の C ドライブを共有する
Windows のローカル Docker コンテナーで実行している場合、`aml_config` の `docker.compute` ファイルで `sharedVolumes` を `true` に設定すると、実行のパフォーマンスを向上させることができます。 ただし、そのためには、"_Docker for Windows ツール_" で C ドライブを共有する必要があります。 C ドライブを共有できない場合は、次のヒントを試してください。

* ファイル エクスプローラーを使用して、C ドライブ上の共有を検査します
* ネットワーク アダプターの設定を開き、vEthernet の "Microsoft ネットワーク用ファイルとプリンター共有" をアンインストール/再インストールします
* Docker の設定を開き、Docker の設定から C ドライブを共有します
* Windows パスワードの変更は共有に影響します。 ファイル エクスプローラーを開き、C ドライブをもう一度共有し、新しいパスワードを入力します。
* C ドライブを Docker と共有しようとするときに、ファイアウォールの問題が発生する可能性もあります。 この [Stack Overflow の投稿](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051)が役立つ可能性があります。
* ドメインの資格情報を使用して C ドライブを共有する場合、ドメイン コントローラーにアクセスできないネットワーク (たとえば、ホーム ネットワークやパブリック wifi など) で共有が動作を停止することがあります。 詳細については、[こちらの投稿](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/)をご覧ください。

`docker.compute` ファイルで `sharedVolumne` を `false` に設定することによって、わずかなパフォーマンス コストで共有の問題を回避することもできます。

## <a name="wipe-clean-workbench-installation"></a>Workbench のインストールを消去する
一般に、これを行う必要はありません。 ただし、インストールを消去する必要がある場合は、以下の手順で行います。

- Windows の場合:
  - 最初に、_コントロール パネル_ の _[プログラムの追加と削除]_ アプレットを使って、_Azure Machine Learning Workbench_ アプリケーションのエントリを削除します。  
  - その後、次のいずれかのスクリプトをダウンロードして実行します。
    - [Windows コマンド ライン スクリプト](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd)。
    - [Windows PowerShell スクリプト](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1)  (スクリプトを実行する前に、管理者特権の PowerShell ウィンドウで `Set-ExecutionPolicy Unrestricted` を実行することが必要な場合があります)。
- macOS の場合:
  - [macOS bash シェル スクリプト](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh)をダウンロードして実行します。

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Azure ML がインストールした Python 環境とは異なる Python の場所を使用する Azure ML
Azure Machine Learning Workbench の最近の変更により、Azure ML Workbench によってインストールされた Python 環境をローカル実行が指し示していないことに気付く場合があります。 このようなことは、ユーザーが自身のコンピューターに別の Python 環境をインストールしてあり、"Python" パスがその環境を指すように設定されている場合に発生する可能性があります。 Azure ML Workbench がインストールした Python 環境を使うには、以下の手順のようにします。
- プロジェクト ルートの下の aml_config フォルダーにある local.compute ファイルを開きます。
- "pythonLocation" 変数を、Azure ML Workbench によってインストールされた Python 環境の物理パスを指すように変更します。 このパスは、2 つの方法で取得できます。
    - Azure ML Python の場所は、%localappdata%\AmlWorkbench\python\python.exe でわかります
    - Azure ML Workbench から cmd を開き、コマンド プロンプトで「python」と入力して、sys.exe をインポートし、sys.executable を実行して、そこからパスを取得します。 



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
