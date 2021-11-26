---
title: ストレージ アカウントの作成
titleSuffix: Azure Storage
description: BLOB、ファイル、キュー、テーブルを格納するためのストレージ アカウントの作成について説明します。 Azure ストレージ アカウントには、データの読み取りと書き込みを行うための Microsoft Azure の一意の名前空間が用意されています。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/18/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3be79a2af3dfb05b289a0111c9e1f9fcf17bab02
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555548"
---
# <a name="create-a-storage-account"></a>ストレージ アカウントを作成する

Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル、キュー、テーブル) が含まれます。 ストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 Azure ストレージ アカウントの詳細については、「[ストレージ アカウントの概要](storage-account-overview.md)」を参照してください。

このハウツー記事では、[Azure portal](https://portal.azure.com/)、[Azure PowerShell](/powershell/azure/)、[Azure CLI](/cli/azure)、または [Azure Resource Manager テンプレート](../../azure-resource-manager/management/overview.md)を使ってストレージ アカウントを作成する方法を説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[なし] :

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で Azure ストレージ アカウントを作成するには、[Az PowerShell モジュール](https://www.powershellgallery.com/packages/Az) バージョン 0.7 以降がインストールされていることを確認します。 詳細については、[Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)に関するページを参照してください。

現在のバージョンを調べるには、次のコマンドを実行します。

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell をインストールまたはアップグレードする場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の 2 つの方法のいずれかで Azure にサインインし、Azure CLI コマンドを実行できます。

- Azure portal 内から、Azure Cloud Shell で CLI コマンドを実行できます。
- CLI をインストールして、CLI コマンドをローカルで実行できます。

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell を使用する

Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI は、アカウントで使用できるように事前にインストールおよび構成されています。 Azure portal の右上のセクションのメニューで **[Cloud Shell]** ボタンをクリックします。

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

このボタンによって対話型のシェルが起動されます。このハウツー記事で説明する手順は、これを使って実行できます。

[![ポータルに Cloud Shell のウィンドウが表示されるスクリーンショット](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI をローカルにインストールする

Azure CLI はローカルにインストールして使用することもできます。 この記事の例には、Azure CLI バージョン 2.0.4 以降が必要です。 `az --version` を実行して、インストールされているバージョンを見つけます。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

# <a name="template"></a>[テンプレート](#tab/template)

[なし] :

---

次に、Azure にサインインします。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure portal](https://portal.azure.com) にサインインします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell を起動するには、[Azure portal](https://portal.azure.com) にサインインします。

CLI のローカル インストールにログインするには、[az login](/cli/azure/reference-index#az_login) コマンドを実行します。

```azurecli-interactive
az login
```

# <a name="template"></a>[テンプレート](#tab/template)

該当なし

---

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Resource Manager のリソースです。 Resource Manager は、Azure のデプロイおよび管理サービスです。 詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」を参照してください。

Azure ストレージ アカウントなど、すべての Resource Manager リソースは、Azure リソース グループに属している必要があります。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。 このハウツーでは、新しいリソース グループを作成する方法を示します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して Azure ストレージ アカウントを作成するには、以下の手順のようにします。

1. 左側のポータル メニューで **[ストレージ アカウント]** を選択して、ストレージ アカウントの一覧を表示します。
1. **[ストレージ アカウント]** ページで、 **[作成]** を選択します。

新しいストレージ アカウントのオプションは、 **[ストレージ アカウントを作成する]** ページのタブにまとめられています。 次のセクションでは、各タブとそのオプションについて説明します。

### <a name="basics-tab"></a>[基本] タブ

**[基本]** タブでは、ストレージ アカウントに関する必須の情報を入力します。 **[基本]** タブの設定が済んだら、他のタブのオプションを設定して新しいストレージ アカウントをさらにカスタマイズするか、 **[確認および作成]** を選択して既定のオプションをそのまま使用し、アカウントの検証と作成を続けることができます。

次の表では **[基本]** タブのフィールドについて説明します。

| Section | フィールド | 必須または省略可能 | 説明 |
|--|--|--|--|
| プロジェクトの詳細 | サブスクリプション | 必須 | 新しいストレージ アカウントのサブスクリプションを選択します。 |
| プロジェクトの詳細 | Resource group | 必須 | このストレージ アカウント用に新しいリソース グループを作成するか、既存のものを選択します。 詳細については、「[リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)」を参照してください。 |
| インスタンスの詳細 | ストレージ アカウント名 | 必須 | ストレージ アカウント用に一意の名前を選択します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 |
| インスタンスの詳細 | リージョン | 必須 | ストレージ アカウントの適切なリージョンを選択します。 詳細については、「[Azure のリージョンと Availability Zones](../../availability-zones/az-overview.md)」をご覧ください。<br /><br />ストレージ アカウントまたは冗長構成の種類によっては、サポートされていないリージョンがあります。 詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。<br /><br />リージョンの選択は、課金に影響を与える可能性があります。 詳細については、「[ストレージ アカウントの課金](storage-account-overview.md#storage-account-billing)」を参照してください。 |
| インスタンスの詳細 | パフォーマンス | 必須 | 汎用 v2 ストレージ アカウント (既定) の場合は、 **[Standard]** パフォーマンスを選択します。 Microsoft は、ほとんどのシナリオにこのアカウントの種類をお勧めします。 詳細については、「[ストレージ アカウントの種類](storage-account-overview.md#types-of-storage-accounts)」を参照してください。<br /><br />待機時間を短くする必要があるシナリオの場合は、 **[Premium]** を選択します。 **[Premium]** を選択した後、作成する Premium ストレージ アカウントの種類を選択します。 次の種類の Premium ストレージ アカウントを使用できます。 <ul><li>[ブロック BLOB](./storage-account-overview.md)</li><li>[ファイル共有](../files/storage-files-planning.md#management-concepts)</li><li>[ページ BLOB](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| インスタンスの詳細 | 冗長性 | 必須 | 目的の冗長構成を選択します。 すべてのリージョンのすべてのストレージ アカウントの種類で、すべての冗長オプションを使用できるわけではありません。 冗長構成の詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。<br /><br />Geo 冗長構成 (GRS または GZRS) を選択した場合、データは別のリージョンのデータ センターにレプリケートされます。 セカンダリ リージョンのデータへの読み取りアクセスを行う場合は、 **[Make read access to data available in the event of regional unavailability]\(リージョンが使用できない場合に使用できるデータに読み取りアクセスを行う\)** をオンにします。 |

次の図は、新しいストレージ アカウントの標準構成を示しています。

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="新しいストレージ アカウントの標準構成を示すスクリーンショット - [基本] タブ":::

### <a name="advanced-tab"></a>[詳細設定] タブ

**[詳細設定]** タブでは、新しいストレージ アカウントの追加のオプションを構成し、既定の設定を変更することができます。 これらのオプションの一部は、ストレージ アカウントの作成後にも構成できますが、それ以外は作成時に構成する必要があります。

次の表では、 **[詳細設定]** タブのフィールドを説明します。

| Section | フィールド | 必須または省略可能 | 説明 |
|--|--|--|--|
| セキュリティ | 安全な転送を有効にする | 省略可能 | セキュリティで保護された転送を有効にして、このストレージ アカウントへの受信要求は HTTPS を介してのみ行われるように要求します (既定)。 最善のセキュリティのためにお勧めします。 詳細については、「[セキュリティで保護された接続を確保するために安全な転送を要求する](storage-require-secure-transfer.md)」を参照してください。 |
| セキュリティ | インフラストラクチャ暗号化を有効にする | 省略可能 | 既定では、インフラストラクチャの暗号化は有効になっていません。 サービス レベルとインフラストラクチャ レベルの両方でデータを暗号化するには、インフラストラクチャの暗号化を有効にします。 詳細については、「[データの二重暗号化のためにインフラストラクチャ暗号化を有効にしてストレージ アカウントを作成する](infrastructure-encryption-enable.md)」を参照してください。 |
| セキュリティ | BLOB パブリック アクセスを有効にする | 省略可能 | この設定を有効にすると、適切なアクセス許可を持つユーザーは、ストレージ アカウント内のコンテナーへの匿名パブリック アクセスを有効にすることができます (既定)。 この設定を無効にすると、ストレージ アカウントへのすべての匿名パブリック アクセスが禁止されます。 詳細については、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](../blobs/anonymous-read-access-prevent.md)」を参照してください。<br> <br> ユーザーが追加の手順によってコンテナーのパブリック アクセスの設定を明示的に構成しない限り、BLOB のパブリック アクセスを有効にしても、パブリック アクセスで BLOB のデータを利用することはできません。 |
| セキュリティ | ストレージ アカウント キーへのアクセスを有効にする (プレビュー) | 省略可能 | この設定を有効にすると、クライアントは、アカウント アクセス キーまたは Azure Active Directory (Azure AD) アカウントのいずれかを使用して、ストレージ アカウントへの要求を承認できます (既定)。 この設定を無効にすると、アカウント アクセス キーによる承認はできなくなります。 詳細については、[Azure ストレージ アカウントの共有キーによる認可の禁止](shared-key-authorization-prevent.md)に関するページを参照してください。 |
| セキュリティ | TLS の最小バージョン | 必須 | ストレージ アカウントへの受信要求に対するトランスポート層セキュリティ (TLS) の最小バージョンを選択します。 既定値は TLS バージョン 1.2 です。 既定値に設定すると、TLS 1.0 または TLS 1.1 を使用して行われた受信要求は拒否されます。 詳細については、「[ストレージ アカウントへの要求に必要な最小バージョンのトランスポート層セキュリティ (TLS) を適用する](transport-layer-security-configure-minimum-version.md)」を参照してください。 |
| Data Lake Storage Gen2 | 階層型名前空間を有効にする | 省略可能 | このストレージ アカウントを Azure Data Lake Storage Gen2 ワークロードに使用するには、階層型名前空間を構成します。 詳細については、「[Azure Data Lake Storage Gen2 の概要](../blobs/data-lake-storage-introduction.md)」を参照してください。 |
| セキュア ファイル転送プロトコル (SFTP) | SFTP を有効にする | オプション | セキュア ファイル転送プロトコル (SFTP) の使用を有効にして、インターネット経由で安全にデータを転送します。 詳細については、「[Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポート](../blobs/secure-file-transfer-protocol-support.md)」を参照してください。 |
| BLOB ストレージ | Enable network file share (NFS) v3 (NFS (ネットワーク ファイル共有) v3 を有効にする) | 省略可能 | NFS v3 により、オブジェクト ストレージのスケールで Linux ファイル システムの互換性が得られます。また、Linux クライアントは、Azure 仮想マシン (VM) またはオンプレミスのコンピューターから Blob Storage にコンテナーをマウントできます。 詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート](../blobs/network-file-system-protocol-support.md)」を参照してください。 |
| BLOB ストレージ | アクセス層 | 必須 | BLOB アクセス層を使用すると、使用方法に基づいて、最もコスト効率の高い方法で BLOB データを格納できます。 頻繁にアクセスされるデータには、ホット層 (既定値) を選択します。 頻繁にアクセスされないデータには、クール層を選択します。 詳細については、[BLOB データのホット、クール、アーカイブ アクセス層](../blobs/access-tiers-overview.md)に関するページを参照してください。 |
| Azure Files | 大型ファイル共有を有効にする | 省略可能 | LRS または ZRS 冗長性を使用する標準のファイル共有でのみ使用できます。 |
| テーブルとキュー | Enable support for customer-managed keys (カスタマー マネージド キーのサポートを有効にする) | 省略可能 | テーブルとキューでカスタマー マネージド キーのサポートを有効にするには、ストレージ アカウントを作成するときにこの設定を選択する必要があります。 詳細については、「[テーブルとキューのカスタマーマネージド キーがサポートされるアカウントを作成する](account-encryption-key-create.md)」を参照してください。 |

### <a name="networking-tab"></a>[ネットワーク] タブ

**[ネットワーク]** タブでは、新しいストレージ アカウントのネットワーク接続とルーティングの基本設定を構成できます。 これらのオプションは、ストレージ アカウントを作成した後で構成することもできます。

次の表では、 **[ネットワーク]** タブのフィールドを説明します。

| Section | フィールド | 必須または省略可能 | 説明 |
|--|--|--|--|
| ネットワーク接続 | 接続方法 | 必須 | 既定では、受信ネットワーク トラフィックはストレージ アカウント用のパブリック エンドポイントにルーティングされます。 トラフィックを Azure 仮想ネットワーク経由でパブリック エンドポイントにルーティングする必要があるように指定できます。 また、ストレージ アカウント用にプライベート エンドポイントを構成することもできます。 詳細については、「[Azure Storage のプライベート エンドポイントを使用する](storage-private-endpoints.md)」を参照してください。 |
| ネットワーク ルーティング | ルーティング設定 | 必須 | ネットワークのルーティングの優先順位では、インターネット経由でクライアントからストレージ アカウントのパブリック エンドポイントにネットワーク トラフィックをルーティングする方法を指定します。 既定では、新しいストレージ アカウントには Microsoft ネットワーク ルーティングが使用されます。 ストレージ アカウントに最も近い POP を使用してネットワーク トラフィックをルーティングすることもできます。これにより、ネットワーク コストが減る可能性があります。 詳細については、「[Azure Storage のネットワーク ルーティング優先設定](network-routing-preference.md)」を参照してください。 |

### <a name="data-protection-tab"></a>[データ保護] タブ

**[データ保護]** タブでは、新しいストレージ アカウントの BLOB データのデータ保護オプションを構成できます。  これらのオプションは、ストレージ アカウントを作成した後で構成することもできます。 Azure Storage でのデータ保護オプションの概要については、「[データ保護の概要](../blobs/data-protection-overview.md)」を参照してください。

次の表では、 **[データ保護]** タブのフィールドを説明します。

| Section | フィールド | 必須または省略可能 | 説明 |
|--|--|--|--|
| Recovery | Enable point-in-time restore for containers (コンテナーのポイントインタイム リストアを有効にする) | 省略可能 | ポイントインタイム リストアでは、ブロック BLOB データを以前の状態に復元できるようにすることで、誤った削除や破損を防ぐことができます。 詳細については、「[ブロック BLOB のポイントインタイム リストア](../blobs/point-in-time-restore-overview.md)」を参照してください。<br /><br />ポイントインタイム リストアを有効にすると、BLOB のバージョン管理、BLOB の論理的な削除、BLOB の変更フィードも有効になります。 これらの前提条件機能は、コストに影響を与える可能性があります。 詳細については、ポイントインタイム リストアに関する「[価格と課金](../blobs/point-in-time-restore-overview.md#pricing-and-billing)」を参照してください。 |
| Recovery | BLOB の論理的な削除の有効化 | 省略可能 | BLOB の論理的な削除を使用すると、削除されたデータがシステムに指定された保持期間だけ維持されることにより、個々の BLOB、スナップショット、またはバージョンが誤った削除または上書きから保護されます。 保持期間中は、論理的に削除されたオブジェクトを削除された時点の状態に復元することができます。 詳細については、「[BLOB の論理的な削除](../blobs/soft-delete-blob-overview.md)」を参照してください。<br /><br />Microsoft は、ストレージ アカウントで BLOB の論理的な削除を有効にし、最小保持期間を 7 日に設定することをお勧めします。 |
| Recovery | Enable soft delete for containers (preview) (コンテナーの論理的な削除を有効にする (プレビュー)) | 省略可能 | コンテナーの論理的な削除を使用すると、削除されたデータがシステムに指定された保持期間だけ維持されることにより、コンテナーとその内容が誤った削除から保護されます。 保持期間中は、論理的に削除されたコンテナーを削除された時点の状態に復元することができます。 詳細については、「[コンテナーの論理的な削除 (プレビュー)](../blobs/soft-delete-container-overview.md)」を参照してください。<br /><br />Microsoft は、ストレージ アカウントでコンテナーの論理的な削除を有効にし、最小保持期間を 7 日に設定することをお勧めします。 |
| Recovery | ファイル共有の論理的な削除を有効にする | 省略可能 | ファイル共有の論理的な削除を使用すると、削除されたデータがシステムに指定された保持期間だけ維持されることにより、ファイル共有とその内容が誤った削除から保護されます。 保持期間中は、論理的に削除されたファイル共有を削除された時点の状態に復元することができます。 詳細については、「[Azure ファイル共有の誤削除を防ぐ](../files/storage-files-prevent-file-share-deletion.md)」を参照してください。<br /><br />Microsoft は、Azure Files ワークロード用のファイル共有で論理的な削除を有効にし、最小保持期間を 7 日に設定することをお勧めします。 |
| 追跡 | BLOB のバージョン管理を有効にする | 省略可能 | BLOB のバージョン管理を有効にすると、BLOB が上書きされるときに、前のバージョンでの BLOB の状態が自動的に保存されます。 詳細については、「[BLOB のバージョン管理](../blobs/versioning-overview.md)」を参照してください。<br /><br />Microsoft は、ストレージ アカウントのデータ保護を最善にするため、BLOB のバージョン管理を有効にすることをお勧めします。 |
| 追跡 | BLOB の変更フィードを有効にする | 省略可能 | BLOB の変更フィードを有効にすると、ストレージ アカウント内のすべての BLOB とそのメタデータに対するすべての変更のトランザクション ログが提供されます。 詳細については、「[Azure Blob Storage の変更フィードのサポート](../blobs/storage-blob-change-feed.md)」を参照してください。 |

### <a name="tags-tab"></a>[タグ] タブ

**[タグ]** タブでは、Azure リソースを整理するための Resource Manager タグを指定できます。 詳細については、「[論理的な組織化のためにリソース、リソース グループ、サブスクリプションにタグを付ける](../../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認および作成]** タブに移動すると、選択したストレージ アカウントの設定の検証が Azure によって実行されます。 検証に成功した場合は、ストレージ アカウントの作成に進むことができます。

検証が失敗した場合は、変更する必要がある設定がポータルに示されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して汎用 v2 ストレージ アカウントを作成するには、まず、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを呼び出して新しいリソース グループを作成します。

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

`-Location` パラメーターに指定するリージョンがわからない場合は、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを使用して、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

```azurepowershell-interactive
Get-AzLocation | select Location
```

次に、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドを使用して、読み取りアクセス geo 冗長ストレージ (RA-GRS) で Standard 汎用 v2 ストレージ アカウントを作成します。 ストレージ アカウントの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

ストレージ アカウントの階層型名前空間で [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用できるようにするには、**New-AzStorageAccount** コマンドの呼び出しで `EnableHierarchicalNamespace' parameter to `$True` を設定します。

次の表は、必要な冗長構成で特定の種類のストレージ アカウントを作成するために、`SkuName` パラメーターと `Kind` パラメーターに使用する値を示したものです。

| ストレージ アカウントの種類 | サポートされている冗長構成 | Kind パラメーターでサポートされる値 | SkuName パラメーターでサポートされる値 | 階層型名前空間のサポート |
|--|--|--|--|--|
| Standard 汎用 v2 | LRS、GRS、RA-GRS、ZRS、GZRS、RA-GZRS | StorageV2 | Standard_LRS、Standard_GRS、Standard_RAGRS、Standard_ZRS、Standard_GZRS、Standard_RAGZRS | はい |
| Premium ブロック BLOB | LRS、ZRS | BlockBlobStorage | Premium_LRS、Premium_ZRS | はい |
| Premium ファイル共有 | LRS、ZRS | FileStorage | Premium_LRS、Premium_ZRS | いいえ |
| Premium ページ BLOB | LRS | StorageV2 | Premium_LRS | いいえ |
| レガシ Standard 汎用 v1 | LRS、GRS、RA-GRS | Storage | Standard_LRS、Standard_GRS、Standard_RAGRS | いいえ |
| レガシ BLOB ストレージ | LRS、GRS、RA-GRS | BlobStorage | Standard_LRS、Standard_GRS、Standard_RAGRS | いいえ |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して汎用 v2 ストレージ アカウントを作成するには、まず、[az group create](/cli/azure/group#az_group_create) コマンドを呼び出して新しいリソース グループを作成します。

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

`--location` パラメーターに指定するリージョンがわからない場合は、[az account list-locations](/cli/azure/account#az_account_list) コマンドで、サブスクリプションに対してサポートされているリージョンの一覧を取得できます。

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

次に、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを使用して、読み取りアクセス geo 冗長ストレージで Standard 汎用 v2 ストレージ アカウントを作成します。 ストレージ アカウントの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダーの値を独自の一意の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

ストレージ アカウントの階層型名前空間で [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用できるようにするには、**az storage account create** コマンドの呼び出しに `enable-hierarchical-namespace` パラメーターを `true` に設定します。 階層型名前空間を作成するには、Azure CLI バージョン 2.0.79 以降が必要です。

次の表は、必要な冗長構成で特定の種類のストレージ アカウントを作成するために、`sku` パラメーターと `kind` パラメーターに使用する値を示したものです。

| ストレージ アカウントの種類 | サポートされている冗長構成 | kind パラメーターでサポートされる値 | sku パラメーターでサポートされる値 | 階層型名前空間のサポート |
|--|--|--|--|--|
| Standard 汎用 v2 | LRS、GRS、RA-GRS、ZRS、GZRS、RA-GZRS | StorageV2 | Standard_LRS、Standard_GRS、Standard_RAGRS、Standard_ZRS、Standard_GZRS、Standard_RAGZRS | はい |
| Premium ブロック BLOB | LRS、ZRS | BlockBlobStorage | Premium_LRS、Premium_ZRS | はい |
| Premium ファイル共有 | LRS、ZRS | FileStorage | Premium_LRS、Premium_ZRS | いいえ |
| Premium ページ BLOB | LRS | StorageV2 | Premium_LRS | いいえ |
| レガシ Standard 汎用 v1 | LRS、GRS、RA-GRS | Storage | Standard_LRS、Standard_GRS、Standard_RAGRS | いいえ |
| レガシ BLOB ストレージ | LRS、GRS、RA-GRS | BlobStorage | Standard_LRS、Standard_GRS、Standard_RAGRS | いいえ |

# <a name="template"></a>[テンプレート](#tab/template)

Azure PowerShell または Azure CLI を使用して Resource Manager テンプレートをデプロイし、ストレージ アカウントを作成できます。 このハウツー記事で使うテンプレートは、[Azure Resource Manager クイックスタートのテンプレート](https://azure.microsoft.com/resources/templates/storage-account-create/)に関する記事からのものです。 スクリプトを実行するには、 **[使ってみる]** を選択して、Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

> [!NOTE]
> このテンプレートは、例としてのみ機能します。 このテンプレートの一部として構成されていないストレージ アカウント設定は多数あります。 たとえば、[Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) を使用する場合、`StorageAccountPropertiesCreateParameters` オブジェクトの `isHnsEnabledad` プロパティを `true` に設定して、このテンプレートを変更します。

このテンプレートの変更方法または新しいテンプレートの作成方法については、以下を参照してください。

- [Azure Resource Manager のドキュメント](../../azure-resource-manager/index.yml)。
- [ストレージ アカウント テンプレート リファレンス](/azure/templates/microsoft.storage/allversions)。
- [その他のストレージ アカウント テンプレート サンプル](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)。

---

## <a name="delete-a-storage-account"></a>ストレージ アカウントを削除する

ストレージ アカウントを削除すると、アカウント内のすべてのデータを含む、アカウント全体が削除されます。 アカウントを削除する前に、保存する必要のあるすべてのデータを必ずバックアップしてください。

特定の状況では、削除したストレージ アカウントを回復できますが、回復は保証されません。 詳しくは、「[削除されたストレージ アカウントを復旧する](storage-account-recover.md)」をご覧ください。

Azure 仮想マシンに関連付けられているストレージ アカウントを削除しようとすると、まだ使用しているストレージ アカウントに関するエラー メッセージが表示されることがあります。 このエラーのトラブルシューティングについては、[ストレージ アカウントを削除する際のエラーのトラブルシューティング](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors)に関するページを参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動します。
1. **[削除]** をクリックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ストレージ アカウントを削除するには、[Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) コマンドを使用します。

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントを削除するには、[az storage account delete](/cli/azure/storage/account#az_storage_account_delete) コマンドを使用します。

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[テンプレート](#tab/template)

ストレージ アカウントを削除するには、Azure PowerShell または Azure CLI を使用します。

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

また、リソース グループを削除して、ストレージ アカウントとそのリソース グループ内の他のリソースを削除することもできます。 リソース グループの削除の詳細については、[リソース グループの削除](../../azure-resource-manager/management/delete-resource-group.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](storage-account-overview.md)
- [汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)
- [ストレージ アカウントを別のリージョンに移動する](storage-account-move.md)
- [削除されたストレージ アカウントを復旧します](storage-account-recover.md)