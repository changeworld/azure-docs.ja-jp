---
title: クライアントと Azure NetApp Files を使用してスナップショットからファイルを復元する | Microsoft Docs
description: Azure NetApp Files を使用して、マウントされたボリュームを持つクライアントを使用して、スナップショットからファイルを復元する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699258"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>クライアントと Azure NetApp Files を使用してスナップショットからファイルを復元する

[スナップショット](snapshots-introduction.md)を使用すると、ボリュームの特定の時点への復旧が可能になります。 [スナップショット全体をボリュームに復元する](snapshots-restore-new-volume.md)ことを望まない場合は、ボリュームがマウントされているクライアントを使用してスナップショットからファイルを復元できます。  

マウントされたボリュームには、クライアントからアクセス可能な `.snapshot` (NFS クライアントの場合) または`~snapshot` (SMB クライアントの場合) という名前のスナップショットディレクトリが含まれています。 スナップショット ディレクトリには、ボリュームのスナップショットに対応するサブディレクトリが含まれています。 各サブディレクトリには、スナップショットのファイルが含まれます。 ファイルを誤って削除または上書きした場合、ファイルをスナップショットのサブディレクトリから読み取り/書き込みディレクトリにコピーすることで、ファイルを親の読み取り/書き込みディレクトリに復元できます。 

[[スナップショット パスを非表示にする] オプション](snapshots-edit-hide-path.md)を使用することで、スナップショット ディレクトリへのアクセスを制御できます。 このオプションによって、ディレクトリをクライアントに対して非表示にするかどうかが制御されます。 したがって、スナップショット内のファイルとフォルダーへのアクセスも制御されます。  

NFSv4.1 では `.snapshot` ディレクトリ (`ls -la`) は表示されません。 しかし、[スナップショット パスを非表示にする] オプションが設定されていないときは、クライアントのコマンド ラインから `cd <snapshot-path>` コマンドを使用して NFSv4.1 経由で `.snapshot` ディレクトリにアクセスできます。 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Linux NFS クライアントを使用してファイルを復元する 

1. `ls` Linux コマンドを使用して、`.snapshot` ディレクトリから復元するファイルを一覧表示します。 

    次に例を示します。

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. `cp` コマンドを使用して、ファイルを親ディレクトリにコピーします。  

    次に例を示します。 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>Windows クライアントを使用してファイルを復元する 

1. ボリュームの `~snapshot` ディレクトリが非表示になっている場合は、親ディレクトリの[非表示の項目を表示](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10)して、`~snapshot` を表示します。

    ![ディレクトリの非表示の項目を示すスクリーンショット。](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. `~snapshot` 内のサブディレクトリに移動して、復元するファイルを見つけます。  ファイルを右クリックします。 **[コピー]** を選択します。  

    ![復元するファイルをコピーする方法を示すスクリーンショット。](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 親ディレクトリに戻ります。 親ディレクトリを右クリックし、[`Paste`] を選択して、ファイルをディレクトリに貼り付けます。

    ![復元するファイルを貼り付ける方法を示すスクリーンショット。](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 親ディレクトリを右クリックし、 **[プロパティ]** を選択します。 **[以前のバージョン]** タブをクリックしてスナップショットの一覧を表示し、 **[復元]** を選択してファイルを復元することもできます。  

    ![以前のバージョンのプロパティを示すスクリーンショット。](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>次のステップ

* [スナップショットの詳細を表示](snapshots-introduction.md) 
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files のスナップショット 101 ビデオ](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files のスナップショットの概要](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)