---
title: "Azure Cloud Shell (プレビュー) でのファイルの永続化 | Microsoft Docs"
description: "Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4603bcbbb752bba341b3e372266b98a1f0863195
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Azure Cloud Shell でのファイルの永続化
Cloud Shell では Azure File ストレージを使用してセッション間でファイルを維持します。

## <a name="setup-clouddrive"></a>clouddrive のセットアップ
Cloud Shell の初回起動時に、セッション間でファイルを維持するために新しいまたは既存のファイル共有を関連付けることを求められます。

### <a name="creating-new-storage"></a>新しいストレージを作成する
![](media/basic-storage.png)

基本設定を使用した場合、1 つのサブスクリプションだけを選択した場合、3 つのリソースがサポートされている一番近いリージョンに自動的に作成されます。
1. `cloud-shell-storage-<region>` という名前のリソース グループ
2. `cs-uniqueGuid` という名前のストレージ アカウント
3. `cs-<user>-<domain>-com-uniqueGuid` という名前のファイル共有

このファイル共有は、`clouddrive` として $Home ディレクトリにマウントされます。 $Home ディレクトリを自動的に更新して永続化する目的で作成される 5 GB のイメージも、このファイル共有を使って格納されます。 これは 1 回限りの作業であり、それ以降のセッションでは自動的にマウントされます。

### <a name="use-existing-resources"></a>既存のリソースの使用
![](media/advanced-storage.png) 詳細オプションで、既にあるリソースを関連付けることもできます。 ストレージのセットアップを促す画面が表示されたら、[詳細設定の表示] を選択して、追加オプションを表示します。 $Home ディレクトリを永続化するために、5 GB のユーザー イメージが既存のファイル共有に作成されます。 ドロップダウンのボックスには、割り当てられている Cloud Shell リージョンとローカル/グローバル冗長ストレージ アカウントが一覧表示されます。

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure リソース ポリシーによるリソース作成の制限
Cloud Shell によって作成されたストレージ アカウントは `ms-resource-usage:azure-cloud-shell` でタグ付けされます。 Cloud Shell からストレージ アカウントを作成できないようにするには、この固有のタグによってトリガーされる[タグの Azure リソース ポリシー](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)を作成します。

## <a name="how-it-works"></a>動作のしくみ
### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell では、以下の 2 つの方法でファイルが永続化されます。
1. $Home 内のすべてのコンテンツを永続化するために、$Home ディレクトリのディスク イメージを作成します。 このディスク イメージは、指定されたファイル共有に `acc_<User>.img` として保存され (`fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`)、変更が自動的に反映されます。

2. ファイル共有を直接操作できるように、指定されたファイル共有を $Home ディレクトリに `clouddrive` としてマウントします。 
`/Home/<User>/clouddrive` は `fileshare.storage.windows.net/fileshare` にマッピングされます。
 
> [!Note]
> SSH キーなど、$Home ディレクトリ内のすべてのファイルが、マウントされたファイル共有に格納されたユーザー ディスク イメージに永続化されます。 $Home ディレクトリおよびマウントされたファイル共有への情報の保存時に、ベスト プラクティスを適用してください。

## <a name="using-the-clouddrive-command"></a>clouddrive コマンドを使用する
Cloud Shell では、ユーザーが `clouddrive` というコマンドを実行することで、Cloud Shell にマウントされたファイル共有を手動で更新することができます。
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>新しい clouddrive のマウント

### <a name="pre-requisites-for-manual-mounting"></a>手動マウントの前提条件
Cloud Shell に関連付けられたファイル共有を更新するには、`clouddrive mount` コマンドを使用します。

既存のファイル共有をマウントする場合、ストレージ アカウントは次の条件を満たす必要があります。
1. ファイル共有をサポートする LRS または GRS であること。
2. 自分の割り当てリージョンに存在すること。 オンボーディング時に、自分に割り当てられているリージョンが、リソース グループ名 `cloud-shell-storage-<region>` として表示されます。

### <a name="supported-storage-regions"></a>サポートされているストレージ リージョン
マウント対象の Cloud Shell マシンと同じリージョンに Azure Files が存在していることが必要です。 Cloud Shell マシンは、次の各リージョンに存在します。
|領域|リージョン|
|---|---|
|アメリカ|米国東部、米国中南部、米国西部|
|ヨーロッパ|北ヨーロッパ、西ヨーロッパ|
|アジア太平洋|インド中部、東南アジア|

### <a name="mount-command"></a>マウント コマンド

> [!NOTE]
> 新しいファイル共有をマウントした場合、$Home ディレクトリ用の新しいユーザー イメージが作成され、以前の $Home イメージは以前のファイル共有に保持されます。

次のパラメーターを指定して `clouddrive mount` を実行します。 <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

詳しい情報を表示するには、`clouddrive mount -h` を実行します。 <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>clouddrive のマウント解除
Cloud Shell にマウントされたファイル共有は、いつでもマウントを解除することができます。 ただし Cloud Shell には、マウントされたファイル共有が必要であるため、削除した場合は、次回のセッション時に新しいファイル共有を作成してマウントするように求められます。

Cloud Shell からファイル共有を削除するには:
1. `clouddrive unmount` を実行します。
2. プロンプトに同意して確定します。

作成したファイル共有は、手動で削除するまで存在します。 以降のセッションから、このファイル共有は、Cloud Shell による検索の対象外になります。

詳しい情報を表示するには、`clouddrive mount -h` を実行します。 <br>
![](media/unmount-h.png)

> [!WARNING]
> このコマンドを実行してもリソースは一切削除されません。 ただし、Cloud Shell にマッピングされたファイル共有、リソース グループ、ストレージ アカウントのいずれかを手動で削除すると、お使いの $Home ディレクトリのディスク イメージとすべてのファイルがファイル共有から消去されます。 この操作を取り消すことはできません。

## <a name="list-clouddrive"></a>clouddrive の一覧表示
`clouddrive` としてマウントされているファイル共有を検出するには:

`df` を実行します。 

clouddrive へのファイル パスの URL に、お使いのストレージ アカウント名とファイル共有が表示されます。

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell にローカル ファイルを転送する
`clouddrive` ディレクトリは、Azure ポータル ストレージ ブレードと同期します。 これを使用して、ファイル共有との間でローカル ファイルを移動できます。 Cloud Shell 内でファイルを更新すると、File Storage の GUI に (ブレードを最新の情報に更新した時点で) 反映されます。

### <a name="download-files"></a>ファイルをダウンロードする
![](media/download.png)
1. マウントしたファイル共有に移動します
2. ポータルで対象ファイルを選択します
3. [ダウンロード] をクリックします

### <a name="upload-files"></a>ファイルのアップロード
![](media/upload.png)
1. マウントしたファイル共有に移動します
2. [アップロード] を選択します。
3. アップロードするファイルを選択します
4. アップロードを確認します

Cloud Shell の clouddrive ディレクトリでファイルにアクセスできるようになったことがわかります。

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Azure File Storage について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
