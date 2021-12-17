---
title: Azure NFS ファイル共有に関する問題のトラブルシューティング - Azure Files
description: Azure NFS ファイル共有に関する問題のトラブルシューティングを行います。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: fe9450310e3f8774b31557fd7c045f05ed9b56b1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518358"
---
# <a name="troubleshoot-azure-nfs-file-share-problems"></a>Azure NFS ファイル共有に関する問題のトラブルシューティングを行います

この記事では、Azure NFS ファイル共有に関連する一般的な問題と既知の問題をいくつか説明します。 これらの問題が発生した場合に考えられる原因と回避策を提示します。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![いいえ](../media/icons/no-icon.png) | ![はい](../media/icons/yes-icon.png) |

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" が失敗しました: 無効な引数 (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>原因 1: idmapping が無効になっていない
Azure Files では、英数字の UID/GID は許可されていません。 そのため、idmapping を無効にする必要があります。 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>原因 2: idmapping は無効になっていたが、無効なファイル名またはディレクトリ名が検出された後に再度有効化された
idmapping が正しく無効になっている場合でも、idmapping を無効にする設定が上書きされる場合があります。 たとえば、Azure Files が無効なファイル名を検出すると、エラーが返されます。 この特定のエラー コードが発生すると、NFS v 4.1 Linux クライアントは idmapping を再度有効にすることを決定し、今後の要求は再び英数字の UID/GID を使用して送信されます。 Azure Files でサポートされていない文字の一覧については、こちらの[記事](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)をご覧ください。 コロンは、サポートされていない文字の 1 つです。 

### <a name="workaround"></a>回避策
idmapping が無効になっており、再度有効にするものがないことを確認してから、次の手順を実行します。

- 共有のマウントを解除します
- # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping を使用して idmapping を無効にします。
- 共有を再度マウントします。
- rsyncを実行している場合は、ディレクトリ/ファイル名が正しくないディレクトリから「—numeric-ids」引数を指定してrsyncを実行します。

## <a name="unable-to-create-an-nfs-share"></a>NFS 共有を作成できない

### <a name="cause-1-unsupported-storage-account-settings"></a>原因 1: ストレージ アカウントの設定がサポートされていない

NFS は、次の構成のストレージ アカウントでのみ使用できます。

- レベル - Premium
- アカウントの種類 - FileStorage
- リージョン - [サポートされているリージョンの一覧](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>解決策

次の記事の手順に従ってください。[NFS 共有を作成する方法](storage-files-how-to-create-nfs-shares.md)

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS ファイル共有を接続またはマウントできない

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因 1:要求の発信元が信頼できないネットワークまたは信頼できない IP にあるクライアントである

SMB とは異なり、NFS にはユーザーベースの認証がありません。 共有の認証は、ネットワーク セキュリティ規則の構成に基づいています。 このため、NFS 共有に対してセキュリティで保護された接続のみが確立されるようにするには、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用する必要があります。 プライベート エンドポイントに加えて、オンプレミスからも共有にアクセスするには、VPN または ExpressRoute を設定する必要があります。 ファイアウォール用のストレージ アカウントの許可リストに追加された IP は無視されます。 NFS 共有へのアクセスを設定するには、次のいずれかの方法を使用する必要があります。


- [サービス エンドポイント](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - パブリック エンドポイントによってアクセスされます
    - 同じリージョンでのみ使用できます。
    - VNet ピアリングでは共有へのアクセスが許可されません。
    - 各仮想ネットワークまたはサブネットを許可リストに個別に追加する必要があります。
    - オンプレミスでのアクセスの場合、サービス エンドポイントを ExpressRoute、ポイント対サイト、サイト間 VPN と共に使用できますが、より安全なプライベート エンドポイントの使用をお勧めします。

次の図は、パブリック エンドポイントを使用した接続を示しています。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="パブリック エンドポイント接続の図。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [プライベート エンドポイント](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - アクセスは、サービス エンドポイントよりも安全です。
    - NFS 共有へのプライベート リンク経由のアクセスは、ストレージ アカウントの Azure リージョンの内部および外部 (リージョン間、オンプレミス) から使用できます
    - プライベート エンドポイントでホストされている仮想ネットワークを使用した仮想ネットワーク ピアリングでは、ピアリングされた仮想ネットワーク内のクライアントに NFS 共有へのアクセスが許可されます。
    - プライベート エンドポイントは、ExpressRoute、ポイント対サイト、サイト間 VPN と共に使用できます。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="プライベート エンドポイント接続の図。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因 2:[安全な転送が必須] が有効になっている

NFS 共有では、二重暗号化がまだサポートされていません。 Azure では、Azure データセンター間で転送中のすべてのデータに対して、MACSec を使用した暗号化レイヤーが提供されます。 NFS 共有には、信頼された仮想ネットワークから、および VPN トンネル経由でのみアクセスできます。 NFS 共有で使用できる追加のトランスポート層暗号化はありません。

#### <a name="solution"></a>解決策

ストレージ アカウントの構成ブレードで [安全な転送が必須] を無効にします。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="[安全な転送が必須] を無効にしている、ストレージ アカウントの構成ブレードのスクリーンショット。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因 3: nfs-common パッケージがインストールされていない
マウント コマンドを実行する前に、以下に示すディストリビューション固有のコマンドを実行して、このパッケージをインストールします。

NFS パッケージがインストールされているかどうかを確認するには、`rpm qa | grep nfs-utils` を実行します

#### <a name="solution"></a>解決策

パッケージがインストールされていない場合は、ご使用のディストリビューションにパッケージをインストールします。

##### <a name="ubuntu-or-debian"></a>Ubuntu または Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8+、CentOS 8+

dnf パッケージ マネージャーを使用します (`sudo dnf install nfs-utils`)。

以前のバージョンの Red Hat Enterprise Linux と CentOS では、yum パッケージ マネージャーを使用します (`sudo yum install nfs-common`)。

##### <a name="opensuse"></a>openSUSE

zypper パッケージ マネージャーを使用します (`sudo zypper install-nfscommon`)。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因 4:ファイアウォールによってポート 2049 がブロックされている

NFS プロトコルは、ポート 2049 経由でそのサーバーと通信します。このポートがストレージ アカウント (NFS サーバー) に対して開いていることを確認します。

#### <a name="solution"></a>解決策

次のコマンドを実行して、ポート 2049 がご使用のクライアントで開いていることを確認します: `telnet <storageaccountnamehere>.file.core.windows.net 2049`。 ポートが開いていない場合は開きます。

## <a name="ls-hangs-for-large-directory-enumeration-on-some-kernels"></a>一部のカーネルで、大きなディレクトリ列挙に対して ls がハングする

### <a name="cause-a-bug-was-introduced-in-linux-kernel-v511-and-was-fixed-in-v5125"></a>原因: Linux kernel v5.11 でバグが確認され、v5.12.5 で修正されました。  
一部のカーネルバージョンには、ディレクトリの一覧が無限の READDIR シーケンスを引き起こすバグがあります。 すべてのエントリを 1 回の呼び出しで呼び出すことができる非常に小さなディレクトリには、この問題はありません。
このバグは、Linux kernel v5.11 で確認され、v5.12.5 で修正されました。 したがって、その間のバージョンにはこのバグが存在します。 RHEL 8.4 には、このカーネル バージョンが含まれることが知られています。

#### <a name="workaround-downgrading-or-upgrading-the-kernel"></a>回避策: カーネルのダウングレードまたはアップグレード
影響を受けるカーネル以外のカーネルにダウングレードまたはアップグレードすると、この問題は解決します。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
