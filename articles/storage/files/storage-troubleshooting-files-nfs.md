---
title: Azure NFS ファイル共有に関する問題のトラブルシューティング - Azure Files
description: Azure NFS ファイル共有に関する問題のトラブルシューティングを行います。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708462"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Azure NFS ファイル共有に関するトラブルシューティング

この記事では、Azure NFS ファイル共有に関連する一般的な問題をいくつか示します。 これらの問題が発生した場合に考えられる原因と回避策を提示します。

## <a name="unable-to-create-an-nfs-share"></a>NFS 共有を作成できない

### <a name="cause-1-subscription-is-not-enabled"></a>原因 1:サブスクリプションが有効になっていない

サブスクリプションが Azure Files の NFS のプレビューに登録されていない可能性があります。 この機能を有効にするには、Cloud Shell またはローカル ターミナルから、追加のコマンドレットをいくつか実行する必要があります。

> [!NOTE]
> 登録が完了するまで最長で 30 分ほどお待ちいただく必要がある場合があります。


#### <a name="solution"></a>解決策

次のスクリプトを使用して機能とリソース プロバイダーを登録します。スクリプトを実行する前に `<yourSubscriptionIDHere>` を置き換えてください。

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>原因 2:ストレージ アカウントの設定がサポートされていない

NFS は、次の構成のストレージ アカウントでのみ使用できます。

- レベル - Premium
- アカウントの種類 - FileStorage
- 冗長性 - LRS
- リージョン - 米国東部、米国東部 2、英国南部、東南アジア

#### <a name="solution"></a>解決策

次の記事の手順に従ってください。[NFS 共有を作成する方法](storage-files-how-to-create-nfs-shares.md)

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>原因 3:登録が完了する前にストレージ アカウントが作成された

ストレージ アカウントでこの機能を使用するには、サブスクリプションで NFS の登録を完了した後にそれを作成する必要があります。 登録が完了するまで最長で 30 分かかる場合があります。

#### <a name="solution"></a>解決策

登録が完了したら、次の記事の手順に従ってください。[NFS 共有を作成する方法](storage-files-how-to-create-nfs-shares.md)

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS ファイル共有を接続またはマウントできない

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因 1:要求の発信元が信頼できないネットワークまたは信頼できない IP にあるクライアントである

SMB とは異なり、NFS にはユーザーベースの認証がありません。 共有の認証は、ネットワーク セキュリティ規則の構成に基づいています。 このため、NFS 共有に対してセキュリティで保護された接続のみが確立されるようにするには、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用する必要があります。 プライベート エンドポイントに加えて、オンプレミスからも共有にアクセスするには、VPN または ExpressRoute を設定する必要があります。 ファイアウォール用のストレージ アカウントの許可リストに追加された IP は無視されます。 NFS 共有へのアクセスを設定するには、次のいずれかの方法を使用する必要があります。


- [サービス エンドポイント](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - パブリック エンドポイントによってアクセスされます
    - 同じリージョンでのみ使用できます。
    - VNet ピアリングでは共有へのアクセスが許可されません。
    - 各仮想ネットワークまたはサブネットを許可リストに個別に追加する必要があります。
    - オンプレミスでのアクセスの場合、サービス エンド ポイントを ExpressRoute、ポイント対サイト VPN、サイト間 VPN と共に使用できますが、より安全なプライベート エンドポイントの使用をお勧めします。

次の図は、パブリック エンドポイントを使用した接続を示しています。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="パブリック エンドポイント接続の図。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [プライベート エンドポイント](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - アクセスは、サービス エンドポイントよりも安全です。
    - NFS 共有へのプライベート リンク経由のアクセスは、ストレージ アカウントの Azure リージョンの内部および外部 (リージョン間、オンプレミス) から使用できます
    - プライベート エンドポイントでホストされている仮想ネットワークを使用した仮想ネットワーク ピアリングでは、ピアリングされた仮想ネットワーク内のクライアントに NFS 共有へのアクセスが許可されます。
    - プライベート エンドポイントは、ExpressRoute、ポイント対サイト VPN、サイト間 VPN と共に使用できます。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="パブリック エンドポイント接続の図。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因 2:[安全な転送が必須] が有効になっている

NFS 共有では、二重暗号化がまだサポートされていません。 Azure では、Azure データセンター間で転送中のすべてのデータに対して、MACSec を使用した暗号化レイヤーが提供されます。 NFS 共有には、信頼された仮想ネットワークから、および VPN トンネル経由でのみアクセスできます。 NFS 共有で使用できる追加のトランスポート層暗号化はありません。

#### <a name="solution"></a>解決策

ストレージ アカウントの構成ブレードで [安全な転送が必須] を無効にします。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="パブリック エンドポイント接続の図。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因 3: nfs-common パッケージがインストールされていない
マウント コマンドを実行する前に、以下に示すディストリビューション固有のコマンドを実行して、このパッケージをインストールします。

NFS パッケージがインストールされているかどうかを確認するには、`rpm qa | grep nfs-utils` を実行します

#### <a name="solution"></a>解決策

パッケージがインストールされていない場合は、ご使用のディストリビューションにパッケージをインストールします。

##### <a name="ubuntu-or-debian"></a>Ubuntu または Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8+、CentOS 8+

dnf パッケージ マネージャーを使用します (`sudo dnf install nfs-common`)。

以前のバージョンの Red Hat Enterprise Linux と CentOS では、yum パッケージ マネージャーを使用します (`sudo yum install nfs-common`)。

##### <a name="opensuse"></a>openSUSE

zypper パッケージ マネージャーを使用します (`sudo zypper install-nfscommon`)。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因 4:ファイアウォールによってポート 2049 がブロックされている

NFS プロトコルは、ポート 2049 経由でそのサーバーと通信します。このポートがストレージ アカウント (NFS サーバー) に対して開いていることを確認します。

#### <a name="solution"></a>解決策

次のコマンドを実行して、ポート 2049 がご使用のクライアントで開いていることを確認します: `telnet <storageaccountnamehere>.file.core.windows.net 2049`。 ポートが開いていない場合は開きます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。