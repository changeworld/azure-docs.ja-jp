---
title: Azure NFS ファイル共有をマウントする - Azure Files
description: ネットワーク ファイル システム共有をマウントする方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 530ae82720e6b4eb6a3e4d1021c0b37b9f4dbf5c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707443"
---
# <a name="how-to-mount-an-nfs-file-share"></a>NFS ファイル共有をマウントする方法

[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、サーバー メッセージ ブロック プロトコル (SMB) またはネットワーク ファイル システム (NFS) プロトコルのいずれかを使用して Linux ディストリビューションにマウントできます。 この記事では、NFS を使用したマウントについて説明します。SMB を使用したマウントの詳細については、「[Linux で Azure Files を使用する](storage-how-to-use-files-linux.md)」を参照してください。 使用できる各プロトコルの詳細については、「[Azure ファイル共有プロトコル](storage-files-compare-protocols.md)」を参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>前提条件

- [NFS 共有を作成する](storage-files-how-to-create-nfs-shares.md)。

    > [!IMPORTANT]
    > NFS 共有には、信頼されたネットワークからしかアクセスできません。 NFS 共有への接続は、次のいずれかのソースから行う必要があります。

- 次のいずれかのネットワークソリューションを使用する。
    - [プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint) (推奨) か、または[パブリック エンドポイントへのアクセスを制限](storage-files-networking-endpoints.md#restrict-public-endpoint-access)します。
    - [Azure Files で使用する Linux 上のポイント対サイト (P2S) VPN を構成](storage-files-configure-p2s-vpn-linux.md)します。
    - [Azure Files で使用するサイト間 VPN を構成](storage-files-configure-s2s-vpn.md)します。
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) を構成します。

## <a name="disable-secure-transfer"></a>セキュリティで保護された転送を無効にする

1. Azure portal にサインインし、作成した NFS 共有を含むストレージ アカウントにアクセスします。
1. **[構成]** を選択します。
1. **[安全な転送が必須]** には **[無効]** を選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="安全な転送が無効になっているストレージ アカウントの構成画面のスクリーンショット。":::

## <a name="mount-an-nfs-share"></a>NFS 共有をマウントする

1. ファイル共有が作成されたら、共有を選択し、 **[Linux からの接続]** を選択します。
1. 使用するマウント パスを入力し、スクリプトをコピーします。
1. クライアントに接続し、提供されたマウント スクリプトを使用します。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="安全な転送が無効になっているストレージ アカウントの構成画面のスクリーンショット。":::

これで NFS 共有がマウントされました。

## <a name="next-steps"></a>次の手順

- Azure Files の詳細については、「[Azure Files のデプロイの計画](storage-files-planning.md)」を参照してください。
- 何か問題が発生した場合は、「[Azure NFS ファイル共有に関するトラブルシューティング](storage-troubleshooting-files-nfs.md)」を参照してください。