---
title: Azure Files の新機能
description: Azure Files の新機能と機能強化について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bc44ba9bc2cad45ab447b86a580fb505c8abd6d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273233"
---
# <a name="whats-new-in-azure-files"></a>Azure Files の新機能
Azure Files は、定期的に更新されて、新機能と機能強化が提供されます。 この記事では、Azure Files の新機能に関する詳細情報を提供します。

## <a name="2021-quarter-3-july-august-september"></a>2021 年第 3 四半期 (7 月、8 月、9 月)
### <a name="smb-multichannel-is-generally-available"></a>SMB マルチチャネルの一般提供
SMB マルチチャネルを使用すると、SMB クライアントで Azure ファイル共有への複数の並列接続を確立できます。 これにより、SMB クライアントでは、使用可能なすべてのネットワーク帯域幅をフルに活用し、ネットワーク障害に対する回復性を高め、総保有コストを削減し、1 つのクライアントを使用した読み取りを 2-3 倍、書き込みを 3-4 倍にすることができます。 SMB マルチチャネルは Premium ファイル共有 (FileStorage ストレージ アカウントの種類でデプロイされたファイル共有) で使用でき、既定で無効になります。 

詳細については、次を参照してください。

- [Azure Files での SMB マルチチャネルのパフォーマンス](storage-files-smb-multichannel-performance.md)
- [SMB マルチチャネルを有効にする](files-smb-protocol.md#smb-multichannel)
- [Windows Server ドキュメントの SMB マルチチャネルの概要](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>SMB 3.1.1 と SMB セキュリティの設定
SMB 3.1.1 は、Windows 10 でリリースされた SMB プロトコルの最新バージョンであり、重要なセキュリティとパフォーマンスの更新が含まれています。 Azure Files の SMB 3.1.1 には、既にサポートされている AES-128-CCM に加えて、AES-128-GCM と AES-256-GCM という 2 つの追加の暗号化モードが用意されています。 パフォーマンスを最大化するために、AES-128-GCM は既定の SMB チャネル暗号化オプションとしてネゴシエートされます。AES-128-CCM は、AES-128-GCM をサポートしない古いクライアントでのみネゴシエートされます。 

組織の規制とコンプライアンスの要件に応じて、SMB クライアント、Azure Files、またはその両方で許可される SMB チャネル暗号化オプションを制限することで、AES-128-GCM の代わりに AES-256-GCM をネゴシエートできます。 AES-256-GCM のサポートは、Windows Server 2022 および Windows 10 バージョン 21H1 で追加されました。

SMB 3.1.1 に加えて、Azure Files では SMB プロトコルの動作を変更するセキュリティ設定が公開されています。 このリリースでは、許可される SMB プロトコルのバージョン、SMB チャネル暗号化のオプション、認証方法、Kerberos チケット暗号化のオプションを構成できます。 Azure Files では既定で最も互換性の高いオプションが有効になりますが、これらのオプションはいつでも切り替えることができます。

詳細については、次を参照してください。

- [SMB セキュリティ設定](files-smb-protocol.md#smb-security-settings)
- [Windows](storage-how-to-use-files-windows.md) と [Linux](storage-how-to-use-files-linux.md) の SMB のバージョン情報
- [Windows Server ドキュメントの SMB 機能の概要](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2021 年第 2 四半期 (4 月、5 月、6 月)
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>Premium、ホット、クール ストレージの容量予約 
Azure Files では、ストレージの容量予約 ("*予約インスタンス*" とも呼ばれます) がサポートされます。 ストレージの容量予約では、ストレージ利用の契約を事前に行うことで割引が受けられます。 Azure Files では、Premium、ホット、クール レベルでの容量予約がサポートされています。 容量予約は、単位が 10 TiB または 100 TiB および期間が 1 年または 3 年で販売されています。 

詳細については、次を参照してください。

- [Azure Files の課金の概要](understanding-billing.md)
- [予約容量を使用して Azure Files のコストを最適化する](files-reserve-capacity.md)
- [Azure Files の価格](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>Active Directory へのドメイン参加に関するポータル エクスペリエンスの向上
Azure ストレージ アカウントへのドメイン参加に関するエクスペリエンスが改善され、Azure ファイル共有の管理者がこのプロセスを初めて行う際にガイドが提供されます。 Azure portal の **[ファイル共有]** セクションの **[ファイル共有の設定]** で [Active Directory] を選択すると、ドメイン参加に必要な手順が示されます。

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="Active Directory にストレージ アカウントをドメイン参加させるための新しいポータル エクスペリエンスのスクリーンショット" lightbox="media/files-whats-new/ad-domain-join-1.png":::

詳細については、次を参照してください。

- [SMB アクセスの Azure Files ID ベース認証オプションの概要](storage-files-active-directory-overview.md)
- [概要 - SMB を使用した Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>2021 年第 1 四半期 (1 月、2 月、3 月)
### <a name="azure-files-management-now-available-through-the-control-plane"></a>コントロール プレーンを介した Azure Files の管理が使用可能
Azure Files リソース、ファイル サービス、ファイル共有の管理 API を、コントロール プレーン (`Microsoft.Storage` リソース プロバイダー) を介して使用できるようになりました。 これにより、Azure ファイル共有を Azure Resource Manager または Bicep テンプレートを使用して作成し、データ プレーン (つまり、FileREST API) にアクセスできない場合 (ストレージ アカウントのパブリック エンドポイントが無効になっている場合など) に完全に管理でき、完全なロールベースのアクセス制御 (RBAC) のセマンティクスをサポートすることができます。

ほとんどの場合、コントロール プレーンを使用して Azure Files を管理することをお勧めします。 コントロール プレーンを使用したファイル サービスとファイル共有の管理をサポートするために、Azure portal、Azure Storage の PowerShell モジュール、Azure CLI が更新され、コントロール プレーンを使用したほとんどの管理アクションがサポートされています。 

既存のスクリプトの動作を維持するために、Azure Storage の PowerShell モジュールと Azure CLI では、データ プレーンを使用してファイル サービスとファイル共有を管理する既存のコマンドが維持され、コントロール プレーンを使用する新しいコマンドを追加することもできます。 ポータルの要求は、コントロール プレーンのリソース プロバイダーで処理されます。 PowerShell コマンドと CLI コマンドの名前は次のとおりです。

- Az.Storage の PowerShell:
    - コントロール プレーンのファイル共有コマンドレットには `Rm` というプレフィックスが付けられています (`New-AzRmStorageShare`、`Get-AzRmStorageShare`、`Update-AzRmStorageShare`、`Remove-AzRmStorageShare` など)。 
    - 従来のデータ プレーンのファイル共有コマンドレットには、プレフィックスが付けられていません (`New-AzStorageShare`、`Get-AzStorageShare`、`Set-AzStorageShareQuota`、`Remove-AzStorageShare` など)。
    - ファイル サービスを管理するコマンドレットは、コントロール プレーンを介してのみ使用でき、特別なプレフィックスは付けられていません (`Get-AzStorageFileServiceProperty`、`Update-AzStorageFileServiceProperty` など)。
- Azure Storage の CLI:
    - コントロール プレーンのファイル共有コマンドは、`az storage share-rm` コマンド グループで使用できます (`az storage share-rm create`、`az storage share-rm update` など)。
    - 従来のファイル共有コマンドは、`az storage share` コマンド グループで使用できます (`az storage share create`、`az storage share update` など)。
    - ファイル サービスを管理するコマンドは、コントロール プレーンを介してのみ使用でき、`az storage account file-service-properties` コマンド グループから使用できます (`az storage account file-service-properties show`、`az storage account file-service-properties update` など)。

Azure Files の管理 API の詳細については、こちらを参照してください。

- [Azure Files の REST API の概要](/rest/api/storageservices/file-service-rest-api)
- Azure Files リソースのコントロール プレーン (`Microsoft.Storage` リソース プロバイダー) API: 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) と [Azure CLI](/en-us/cli/azure/storage)

## <a name="2020-quarter-4-october-november-december"></a>2020 年第 4 四半期 (10 月、11 月、12 月)
### <a name="azure-file-share-soft-delete"></a>Azure ファイル共有の論理的な削除
Azure ファイル共有では、論理的な削除がサポートされます。 論理的な削除を有効にすると、ユーザーが定義した保有期間内に削除が取り消された場合に、誤って削除された共有を簡単に回復できます。 保有期間中、論理的に削除された共有には、保有期間中に使用されたデータ容量に対する課金が共有スナップショット レートで発生します。

論理的な削除の詳細については、こちらを参照してください。

- [論理的な削除の概要](storage-files-prevent-file-share-deletion.md)
- [論理的な削除を有効にする](storage-files-enable-soft-delete.md)
- [Azure Files の価格](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="see-also"></a>関連項目
- [Azure Files とは](storage-files-introduction.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure ファイル共有を作成する](storage-how-to-create-file-share.md)
