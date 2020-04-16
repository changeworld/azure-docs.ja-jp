---
title: Azure HPC Cache をマウントする
description: Azure HPC Cache サービスにクライアントを接続する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657347"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache をマウントする

作成されたキャッシュには、NFS クライアントから単純な `mount` コマンドでアクセスできます。 このコマンドは、Azure HPC Cache 上の特定のストレージ ターゲット パスを、クライアント マシンのローカル ディレクトリに接続します。

mount コマンドは、次の要素で構成されています。

* キャッシュのマウント アドレスの 1 つ (キャッシュの概要ページに一覧表示されます)
* ストレージ ターゲットの作成時に設定した仮想名前空間のパス
* クライアントで使用するローカル パス
* この種類の NFS マウントの成功を最適化するコマンド パラメーター

キャッシュの **[Mount instructions]\(マウント手順\)** ページでは、情報と推奨されるオプションが収集され、コピーできるプロトタイプの mount コマンドが作成されます。 詳細については、「[マウント手順ユーティリティを使用する](#use-the-mount-instructions-utility)」をお読みください。

## <a name="prepare-clients"></a>クライアントを準備する

このセクションのガイドラインに従って、クライアントが Azure HPC Cache をマウントできることを確認します。

### <a name="provide-network-access"></a>ネットワーク アクセスを提供する

クライアント マシンには、キャッシュの仮想ネットワークとプライベート サブネットへのネットワーク アクセスが必要です。

たとえば、同じ仮想ネットワーク内にクライアントの VM を作成するか、または仮想ネットワーク内でエンドポイントやゲートウェイなどのソリューションを使用して外部からアクセスできるようにします。 (キャッシュ自体以外は、キャッシュのサブネット内でホストする必要があることに注意してください)。

### <a name="install-utilities"></a>ユーティリティをインストールする

NFS の mount コマンドをサポートするために適切な Linux ユーティリティ ソフトウェアをインストールします。

* Red Hat Enterprise Linux または SuSE の場合: `sudo yum install -y nfs-utils`
* Ubuntu または Debian の場合: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>ローカル パスを作成する

各クライアントに、キャッシュに接続するためのローカル ディレクトリ パスを作成します。 マウントする各ストレージ ターゲットのパスを作成します。

例: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>マウント手順ユーティリティを使用する

Azure portal の [キャッシュ] ビューの **[構成]** セクションから、 **[Mount instructions]\(マウント手順\)** ページを開きます。

![[構成] > [Mount instructions]\(マウント手順\) ページが読み込まれている、ポータルの Azure HPC Cache インスタンスのスクリーンショット](media/mount-instructions.png)

mount コマンドのページには、クライアントのマウント プロセスと前提条件に関する情報、およびコピー可能な mount コマンドの作成に使用できるフィールドが含まれています。

このページを使用するには、次の手順に従います。

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. クライアントの前提条件を確認し、前述の「[クライアントを準備する](#prepare-clients)」の説明に従って、NFS の `mount` コマンドを使用するために必要なユーティリティをインストールします。

1. **[ファイル システムのマウント]**<!-- label will change --> の手順 1 には、クライアントにローカル パスを作成するためのコマンド例が示されます。 これは、クライアントが Azure HPC Cache の内容にアクセスするために使用するパスです。

   必要に応じてコマンド内で変更できるように、パス名をメモしておきます。

1. 手順 2 では、使用可能な IP アドレスのいずれかを選択します。 キャッシュのすべての[クライアント マウント ポイント](#find-mount-command-components)がここに一覧表示されます。 すべての IP アドレス間で負荷を分散するためのシステムがあることを確認します。

1. 手順 3 のフィールドには、プロトタイプの mount コマンドが自動的に入力されます。 フィールドの右側にあるコピー記号をクリックすると、自動的にクリップボードにコピーされます。

   > [!NOTE]
   > 使用する前に、コピー コマンドを確認してください。 場合によっては、クライアントのマウント パスとストレージ ターゲットの仮想名前空間のパスをカスタマイズする必要があります。それらは、このインターフェイスではまだ選択できません。 また、下の[推奨されるオプション](#mount-command-options)を反映するように mount コマンドのオプションを更新する必要もあります。 詳細については、「[mount コマンドの構文について](#understand-mount-command-syntax)」をお読みください。

1. コピーした mount コマンド (必要に応じて編集) をクライアント マシンで使用して、Azure HPC Cache のストレージ ターゲットに接続します。 コマンドをクライアントのコマンド ラインから直接発行することも、クライアントのセットアップ スクリプトまたはテンプレートに mount コマンドを含めることもできます。

## <a name="understand-mount-command-syntax"></a>mount コマンドの構文について

mount コマンドの形式は次のとおりです。

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

例:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

このコマンドが成功すると、ストレージ エクスポートの内容がクライアントの ``hpccache`` ディレクトリに表示されます。

### <a name="mount-command-options"></a>マウント コマンドのオプション

クライアントのマウントが確実に行われるように、mount コマンドで以下の設定と引数を渡します。

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| 推奨される mount コマンドの設定 | |
--- | ---
``hard`` | Azure HPC Cache に対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。
``proto=tcp`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=tcp`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=<value>`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

### <a name="find-mount-command-components"></a>mount コマンドのコンポーネントを見つける

**[Mount instructions]\(マウント手順\)** ページを使用せずに mount コマンドを作成する場合は、キャッシュの **[概要]** ページと **[ストレージ ターゲット]** ページの仮想名前空間パスで、マウント アドレスを確認できます。

![Azure HPC Cache インスタンスの概要ページのスクリーンショット (右下に一連のマウント アドレスが枠囲みで強調表示されている)](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> キャッシュのマウント アドレスは、キャッシュのサブネット内のネットワーク インターフェイスに対応します。 リソース グループでは、これらの NIC が、末尾に `-cluster-nic-` と数字が付いた名前で表示されます。 これらのインターフェイスは変更したり削除したりしないでください。キャッシュが利用できなくなります。

仮想名前空間パスは、 **[ストレージ ターゲット]** ページに表示されます。 個々のストレージ ターゲット名をクリックすると、それに関連付けられている集約された名前空間パスなどの詳細が表示されます。

![キャッシュのストレージ ターゲット パネルのスクリーンショット (テーブルの [パス] 列のエントリが枠囲みで強調表示されている)](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>次のステップ

* キャッシュのストレージ ターゲットを移動する方法については、[新しい Azure Blob Storage へのデータの事前設定](hpc-cache-ingest.md)に関するページを参照してください。
