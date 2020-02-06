---
title: '対話型デバッグ: VS Code および ML のコンピューティング インスタンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で自分のコードを対話形式でデバッグできるように VS Code Remote を設定します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 27243f47df7da22ab4adda088bdf631c1030dd6c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845187"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>VS Code Remote を使用して Azure Machine Learning コンピューティング インスタンス上で対話形式でデバッグする

この記事では、VS Code から**自分のコードを対話形式でデバッグ**できるように、Azure Machine Learning コンピューティング インスタンスに対して Visual Studio Code Remote を設定する方法について説明します。 

> [!NOTE]
> コンピューティング インスタンスは、**米国中北部**、** 米国東部 2**、**北ヨーロッパ**、または**英国南部**のワークスペースにのみ使用できます。

+ [Azure Machine Learning コンピューティング インスタンス](concept-compute-instance.md)は、データ サイエンティスト向けのクラウドベースのフルマネージド ワークステーションであり、IT 管理者用の管理およびエンタープライズ対応機能を備えています。 


+ [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) Development を使用すると、コンテナー、リモート コンピューター、または Linux 用 Windows サブシステム (WSL) を完全な機能を備えた開発環境として使用できるようになります。 

## <a name="prerequisite"></a>前提条件  

Windows プラットフォーム上で、まだ存在していない場合に [OpenSSH と互換性のある SSH クライアントをインストール](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client)する必要があります。 

> [!Note]
> PuTTY は Windows ではサポートされていません。パスに ssh コマンドを含める必要があるからです。 

## <a name="get-ip-and-ssh-port"></a>IP および SSH ポートを取得する 

1. https://ml.azure.com/ で Azure Machine Learning Studio に移動します。

2. ご利用の[ワークスペース](concept-workspace.md)を選択します。
1. **[コンピューティング インスタンス]** タブをクリックします。
1. **[アプリケーション URI]** 列で、リモート コンピューティングとして使用するコンピューティング インスタンスの **SSH** リンクをクリックします。 
1. ダイアログで、IP アドレスと SSH ポートをメモします。 
1. ご利用の秘密キーをご自分のローカル コンピューター上の ~/.ssh/ ディレクトリに保存します。たとえば、新しいファイルのエディターを開き、次のようにキーを貼り付けます。 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   秘密キーは次のようになります。
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 自分だけがファイルを読み取ることができるように、そのファイルに対するアクセス許可を変更します。  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>インスタンスをホストとして追加する 

ファイル `~/.ssh/config` (Linux) または `C:\Users<username>.ssh\config` (Windows) をエディターで開き、次のような新しいエントリを追加します。

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

ここでは、フィールドに関する詳細の一部を説明します。 

|フィールド|[説明]|
|----|---------|
|Host|コンピューティング インスタンスの短縮形として何でも好きなものを使用できます |
|HostName|これはコンピューティング インスタンスの IP アドレスです。 |
|Port|これは、上の SSH ダイアログに表示されるポートです。 |
|User|これは、 `azureuser` とする必要があります。 |
|IdentityFile|秘密キーを保存したファイルを指す必要があります。 |

これで、前に使用した短縮形 `ssh azmlci1` を使用して、ご利用のコンピューティング インスタンスに ssh 接続できるようになったはずです。 

## <a name="connect-vs-code-to-the-instance"></a>インスタンスに VS Code を接続する 

1. [Visual Studio Code をインストールする](https://code.visualstudio.com/)。

1. [リモート SSH 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)をインストールします。 

1. 左側の [リモート SSH] アイコンをクリックして、自分の SSH 構成を表示します。

1. 作成したばかりの SSH ホスト構成を右クリックします。

1. **[Connect to Host in Current Window]\(現在のウィンドウのホストに接続\)** を選択します。 

ここからは、完全にコンピューティング インスタンス上での作業となり、ご利用のローカル Visual Studio Code の場合と同様に、git を編集、デバッグ、および使用したり、拡張機能を使用したりできるようになります。 

## <a name="next-steps"></a>次のステップ

Visual Studio Code Remote の設定が完了したので、Visual Studio Code からコンピューティング インスタンスをリモート コンピューティングとして使用して、自分のコードを対話形式でデバッグすることができます。 

[チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。