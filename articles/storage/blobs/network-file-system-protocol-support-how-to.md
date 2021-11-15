---
title: NFS 3.0 プロトコルを使用して Azure Blob Storage をマウントする | Microsoft Docs
description: NFS 3.0 プロトコルを使用して、Azure 仮想マシン (VM) から、またはオンプレミスで実行されているクライアントから、BLOB ストレージにコンテナーをマウントする方法について説明します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6b22b99cdd883ed8dedb90f925a918fe9c25d9bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444878"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Blob Storage をマウントする

NFS 3.0 プロトコルを使用して、オンプレミスで実行される Linux ベースの Azure 仮想マシン (VM) または Linux システムから BLOB ストレージにコンテナーをマウントすることができます。 この記事では、ステップ バイ ステップ ガイダンスを提供しています。 Blob Storage での NFS 3.0 プロトコルのサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)」を参照してください。

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>手順 1: Azure 仮想ネットワーク (VNet) を作成する

ストレージ アカウントは VNet 内に含まれている必要があります。 VNet を使用すると、クライアントをストレージ アカウントに安全に接続できます。 VNet とその作成方法の詳細については、[Virtual Network のドキュメント](../../virtual-network/index.yml)を参照してください。

> [!NOTE]
> 同じ VNet 内のクライアントは、アカウントにコンテナーをマウントできます。 オンプレミス ネットワークで実行されているクライアントからコンテナーをマウントすることもできますが、そのためにはまずオンプレミス ネットワークを VNet に接続する必要があります。 詳細については「[サポートされているネットワーク接続](network-file-system-protocol-support.md#supported-network-connections)」を参照してください。

## <a name="step-2-configure-network-security"></a>手順 2: ネットワーク セキュリティの構成

アカウント内のデータをセキュリティで保護する唯一の方法は、VNet とその他のネットワーク セキュリティ設定を使用することです。 アカウント キーの承認、Azure Active Directory (AD) セキュリティ、アクセス制御リスト (ACL) などのデータをセキュリティで保護するために使用されるその他のツールは、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされません。

アカウントのデータをセキュリティで保護するには、次の推奨事項を参照してください。[BLOB ストレージのセキュリティに関する推奨事項](security-recommendations.md#networking)。

## <a name="step-3-create-and-configure-a-storage-account"></a>手順 3: ストレージ アカウントの作成と構成

NFS 3.0 を使用してコンテナーをマウントするには、ストレージ アカウントを作成する必要があります。 既存のアカウントを有効にすることはできません。

NFS 3.0 プロトコルは、標準の汎用 v2 ストレージ アカウントおよび Premium ブロック BLOB ストレージ アカウントでサポートされています。 これらのストレージ アカウントの種類について詳しくは、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

アカウントを構成するときに、次の値を選択します。

|設定 | Premium パフォーマンス | Standard パフォーマンス
|----|---|---|
|場所|利用可能なすべてのリージョン |利用可能なすべてのリージョン
|パフォーマンス|Premium| Standard
|アカウントの種類|BlockBlobStorage| 汎用 v2
|レプリケーション|ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)| ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)
|接続方法|パブリック エンドポイント (選択されたネットワーク) またはプライベート エンドポイント |パブリック エンドポイント (選択されたネットワーク) またはプライベート エンドポイント
|階層型名前空間|Enabled|Enabled
|NFS V3|Enabled |Enabled

他のすべての設定については、既定値をそのまま使用できます。

## <a name="step-4-create-a-container"></a>手順 4: コンテナーを作成する

次のいずれかのツールまたは SDK を使用して、ストレージ アカウントにコンテナーを作成します。

|ツール|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

> [!NOTE]
> 既定では、新しいコンテナーのルート スカッシュ オプションは `no root squash` です。 ただし、`root squash` または `all squash` に変更できます。 これらのスカッシュ オプションの詳細については、オペレーティング システムのドキュメントを参照してください。

次の図は、Azure portal で表示されるスカッシュ オプションを示しています。

> [!div class="mx-imgBorder"]
> ![Azure portal でのスカッシュ オプション](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>手順 5: コンテナーをマウントする

Linux システムにディレクトリを作成してから、コンテナーをストレージ アカウントにマウントします。

1. Linux システムで、ディレクトリを作成します。

   ```
   mkdir -p /mnt/test
   ```

2. 次のコマンドを使用して、コンテナーをマウントします。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - このコマンドに表示される `<storage-account-name>` プレースホルダーをストレージ アカウントの名前に置き換えます。

   - `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

---

## <a name="resolve-common-errors"></a>一般的なエラーを解決する

|エラー | 原因 / 解決方法|
|---|---|
|`Access denied by server while mounting`|サポートされているサブネット内でクライアントが実行されていることを確認します。 [サポートされているネットワークの場所](network-file-system-protocol-support.md#supported-network-connections)のページを参照してください。|
|`No such file or directory`| mount コマンドとそのパラメーターを直接ターミナルに入力するようにしてください。 別のアプリケーションからこのコマンドの一部をコピーしてターミナルに貼り付けると、貼り付けた情報の非表示の文字が原因でこのエラーが発生することがあります。 アカウントが NFS 3.0 に対応していない場合にも、このエラーが表示されることがあります。 |
|`Permision denied`| 新しく作成された NFS v3 コンテナーの既定のモードは 0750 です。 ルート以外のユーザーにはボリュームへのアクセス権がありません。 ルート以外のユーザーからのアクセスが必要な場合、ルート ユーザーがモードを 0755 に変更する必要があります。 サンプル コマンド: `sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |このエラーは、クライアントが次の操作を試みたときに発生する可能性があります。<li>BLOB エンドポイントから作成された BLOB に書き込む。<li>スナップショットを持つ BLOB、またはアクティブな WORM (Write Once, Read Many) ポリシーを持つコンテナー内にある BLOB を削除する。|
|`EROFS ("Read-only file system"`) |このエラーは、クライアントが次の操作を試みたときに発生する可能性があります。<li>アクティブなリースを保持している BLOB に書き込むか、BLOB を削除する。<li>アクティブな WORM (Write Once, Read Many) ポリシーを持つコンテナー内にある BLOB に書き込むか、BLOB を削除する。 |
|`NFS3ERR_IO/EIO ("Input/output error"`) |このエラーは、クライアントがアーカイブ アクセス層に格納されている BLOB への読み取り、書き込み、または属性の設定を試みた場合に発生する可能性があります。 |
|`OperationNotSupportedOnSymLink` エラー| このエラーは、BLOB または Azure Data Lake Storage Gen2 API を介した書き込み操作中に返される可能性があります。 これらの API を使用して、NFS 3.0 を使用して作成されたシンボリック リンクへの書き込みや削除を行うことはできません。 シンボリック リンクを操作する際は、必ず NFS v3 エンドポイントを使用してください。 |
|`mount: /mnt/test: bad option;`| **sudo apt install nfs-common** を使用して、nfs ヘルパー プログラムをインストールします。|

## <a name="see-also"></a>関連項目

- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](network-file-system-protocol-support.md)
- [Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポートにおける既知の問題](network-file-system-protocol-known-issues.md)
