---
title: Azure Storage をローカル共有 (コンテナー) としてマウントする
description: Azure App Service 内のコンテナー化されたアプリにカスタム ネットワーク共有をアタッチする方法について説明します。 アプリ間でのファイルの共有、静的コンテンツのリモート管理、ローカルでのアクセスなどを行います。
author: msangapu-msft
ms.topic: article
ms.date: 6/21/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 445a834e32b11ca0f2a30120d2942c1057d83d50
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113006034"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-container-app-in-app-service"></a>App Service のコンテナー アプリでローカル共有として Azure Storage をマウントする

::: zone pivot="container-windows"

> [!NOTE]
>App Service Windows コンテナーの Azure Storage は **プレビュー段階** であり、**運用シナリオ** では **サポートされていません**。

このガイドでは、App Service で Azure Storage ファイルをネットワーク共有として Windows コンテナーにマウントする方法について説明します。 [Azure Files Shares](../storage/files/storage-how-to-use-files-cli.md) および [Premium ファイル共有](../storage/files/storage-how-to-create-file-share.md)のみがサポートされています。 カスタムマウント ストレージの利点は次のとおりです。

::: zone-end

::: zone pivot="container-linux"

このガイドでは、App Service の組み込み Linux コンテナーまたはカスタム Linux コンテナーに、ネットワーク共有として Azure Storage をマウントする方法について説明します。 カスタムマウント ストレージの利点は次のとおりです。

::: zone-end

- App Service アプリの永続ストレージを構成し、ストレージを個別に管理します。
- 動画や画像などの静的なコンテンツが App Service アプリですぐに利用できるようになります。 
- Azure ファイル共有に対して、アプリケーション ログ ファイルを書き込むか、古いアプリケーション ログをアーカイブします。  
- 複数のアプリ間または他の Azure サービスとの間でコンテンツを共有します。

::: zone pivot="container-windows"

Windows コンテナーでは、次の機能がサポートされています。

- Azure Files (読み取り/書き込み)。
- アプリあたり最大 5 つのマウント ポイント。

::: zone-end

::: zone pivot="container-linux"

Linux コンテナーでは、次の機能がサポートされています。

- [サービス エンドポイント](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)と[プライベート リンク](../storage/common/storage-private-endpoints.md)を使用したストレージ アカウントへの安全なアクセス ([VNET 統合](web-sites-integrate-with-vnet.md)が使用される場合)。
- Azure Files (読み取り/書き込み)。
- Azure BLOB (読み取り専用)。
- アプリあたり最大 5 つのマウント ポイント。

::: zone-end

## <a name="prerequisites"></a>前提条件

::: zone pivot="container-windows"

- [Azure App Service の既存の Windows コンテナー アプリ](quickstart-custom-container.md)
- [Azure ファイル共有の作成](../storage/files/storage-how-to-use-files-cli.md)
- [Azure File 共有へのファイルのアップロード](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- 既存の [App Service on Linux アプリ](index.yml)。
- [Azure Storage アカウント](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Azure のファイル共有とディレクトリ](../storage/files/storage-how-to-use-files-cli.md)。

::: zone-end

> [!NOTE]
> Azure Storage は、App Service の既定以外のストレージです。別途請求され、App Service には含まれません。
>

## <a name="limitations"></a>制限事項

::: zone pivot="container-windows"

- ストレージのマウントは、ネイティブの Windows アプリ (コンテナー化されていない) ではサポートされていません。
- [ストレージのファイアウォール](../storage/common/storage-network-security.md)、[サービス エンドポイント](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)、および[プライベート エンドポイント](../storage/common/storage-private-endpoints.md)はサポートされていません。
- マウントされたストレージへの FTP/FTPS アクセスはサポートされていません ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してください)。
- Azure CLI、Azure PowerShell、Azure SDK のサポートはプレビュー段階です。
- カスタム マウント ストレージへの `D:\` または `D:\home` のマッピングはサポートされていません。
- ドライブ文字の割り当て (`C:` から `Z:`) はサポートされていません。
- [デプロイ スロット](deploy-staging-slots.md)の作成中に、ストレージのマウントを複製設定オプションと共に使用することはできません。
- ストレージのマウントは、[アプリをバックアップ](manage-backup.md)するときにバックアップされません。 必ずベスト プラクティスに従って Azure Storage アカウントをバックアップしてください。 

::: zone-end

::: zone pivot="container-linux"

- [ストレージのファイアウォール](../storage/common/storage-network-security.md)は、[サービス エンドポイント](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)と[プライベート エンドポイント](../storage/common/storage-private-endpoints.md)を介してのみサポートされます ([VNET 統合](web-sites-integrate-with-vnet.md)が使用されている場合)。 現在、マウントされた Azure Storage アカウントがプライベート エンドポイントを使用している場合には、カスタム DNS サポートは使用できません。
- カスタムマウント ストレージへの FTP/FTPS アクセスはサポートされていません ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してください)。
- Azure CLI、Azure PowerShell、Azure SDK のサポートはプレビュー段階です。
- カスタム マウント ストレージへの `/` または `/home` のマッピングはサポートされていません。
- [デプロイ スロット](deploy-staging-slots.md)の作成中に、ストレージのマウントを複製設定オプションと共に使用することはできません。
- ストレージのマウントは、[アプリをバックアップ](manage-backup.md)するときにバックアップされません。 必ずベスト プラクティスに従って Azure Storage アカウントをバックアップしてください。 

::: zone-end

::: zone pivot="container-windows"

## <a name="mount-storage-to-windows-container"></a>ストレージを Windows コンテナーにマウントする

コマンド [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) を使用します。 次に例を示します。

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

- Windows コンテナーでは `--storage-type` は `AzureFiles` にする必要があります。 
- `mount-path-directory` の形式は、ドライブ文字なしで `/path/to/dir` または `\path\to\dir` にする必要があります。 常に `C:\` ドライブにマウントされます。 `/` または `\` (ルート ディレクトリ) は使用しないでください。

次のコマンドを実行して、ストレージがマウントされていることを確認します。

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

::: zone-end

::: zone pivot="container-linux"

## <a name="mount-storage-to-linux-container"></a>ストレージを Linux コンテナーにマウントする

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) でアプリに移動します。
1. 左側のナビゲーションで、 **[構成]**  >  **[Path Mappings]\(パス マッピング\)**  >  **[新しい Azure Storage マウント]** をクリックします。 
1. 次の表に従って、ストレージのマウントを構成します。 完了したら、 **[OK]** をクリックします。

    | 設定 | 説明 |
    |-|-|
    | **名前** | マウント構成の名前。 スペースは使用できません。 |
    | **[構成オプション]** | [サービス エンドポイント](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)または [プライベート エンドポイント](../storage/common/storage-private-endpoints.md)を使用していない場合は、 **[基本]** を選択します。 それ以外の場合は、 **[詳細]** を選択します。 |
    | **ストレージ アカウント** | Azure ストレージ アカウント。 |
    | **ストレージの種類** | マウントするストレージに基づいて種類を選択します。 Azure BLOB では、読み取り専用アクセスのみがサポートされています。 |
    | **[ストレージ コンテナー]** または **[共有名]** | マウントするファイル共有または BLOB コンテナー。 |
    | **[アクセス キー]** ([詳細] のみ) | ストレージ アカウントの[アクセス キー](../storage/common/storage-account-keys-manage.md)。 |
    | **[マウント パス]** | Azure Storage にマウントする、Linux コンテナー内のディレクトリ。 `/` (ルート ディレクトリ) は使用しないでください。 |

    > [!CAUTION]
    > **[マウント パス]** に指定される Linux コンテナー内のディレクトリは、空であることが必要です。 このディレクトリに格納されている内容は、Azure Storage がマウントされると削除されます (たとえば、`/home` の下のディレクトリを指定した場合)。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
    >
    
# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

コマンド [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) を使用します。 

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

- `--storage-type` には、`AzureBlob` または `AzureFiles` を指定できます。 `AzureBlob` は読み取り専用です。
- `--mount-path`は、Azure Storage にマウントする、Linux コンテナー内のディレクトリです。 `/` (ルート ディレクトリ) は使用しないでください。

> [!CAUTION]
> `--mount-path` に指定される Linux コンテナー内のディレクトリは、空であることが必要です。 このディレクトリに格納されている内容は、Azure Storage がマウントされると削除されます (たとえば、`/home` の下のディレクトリを指定した場合)。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
>

次のコマンドを実行して、構成を確認します。

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

::: zone-end

> [!NOTE]
> ストレージのマウントを追加、編集、または削除すると、アプリが再起動されます。 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>マウントされたストレージをテストする

Azure Storage がアプリに対して正常にマウントされたことを検証するには、次のようにします。

1. コンテナーに対して [SSH セッションを開き](configure-linux-open-ssh-session.md)ます。
1. SSH ターミナルで、次のコマンドを実行します。

    ```bash
    df –h 
    ```
1. ストレージ共有がマウントされているかどうか確認します。 存在しない場合、ストレージ共有のマウントに問題があります。
1. 次のコマンドを使用して、ストレージのマウントの待機時間または一般的な到達可能性を確認します。

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

## <a name="best-practices"></a>ベスト プラクティス

- 待機時間に関連する潜在的な問題を回避するには、アプリと Azure Storage アカウントを同じ Azure リージョンに配置します。 ただし、アプリと Azure Storage アカウントが同じ Azure リージョンにある場合、App Service IP アドレスからのアクセスを [Azure Storage ファイアウォール構成](../storage/common/storage-network-security.md)で許可しても、それらの IP 制限が適用されないことに注意してください。
- コンテナー アプリのマウント パスは空である必要があります。 このパスに格納されている内容は、Azure Storage がマウントされると削除されます (たとえば、`/home` の下のディレクトリを指定した場合)。 既存アプリのファイルを移行する場合は、始める前に、アプリとその内容をバックアップしてください。
- アプリのパフォーマンス ボトルネックが発生する可能性があるため、ストレージの `/home` へのマウントは推奨されません。 
- Azure Storage アカウントでは、アプリでストレージのマウントに使用される[アクセス キーの再生成](../storage/common/storage-account-keys-manage.md)を行わないようにしてください。 ストレージ アカウントには、2 つの異なるキーが含まれています。 キーの再生成中にストレージのマウントをアプリで引き続き使用できるようにするには、段階的なアプローチを使用します。 たとえば、**key1** を使用して、アプリでストレージのマウントを構成したとします。
    1. **key2** を再生成します。 
    1. ストレージのマウント構成で、再生成された **key2** を使用するアクセス キーを更新します。
    1. **key1** を再生成します。
- Azure Storage のアカウント、コンテナー、または共有を削除する場合は、可能性があるエラー シナリオを回避するために、対応するストレージのマウント構成をアプリから削除します。 
- マウントされた Azure Storage アカウントは、Standard または Premium パフォーマンス レベルのいずれかになります。 アプリの容量とスループットの要件に基づいて、ストレージ アカウントに適したパフォーマンス レベルを選択します。 ストレージの種類に対応するスケーラビリティとパフォーマンスの目標を確認します。
    - [ファイルの場合](../storage/files/storage-files-scale-targets.md)
    - [BLOB の場合](../storage/blobs/scalability-targets.md)  
- アプリが[複数のインスタンスにスケーリング](../azure-monitor/autoscale/autoscale-get-started.md)される場合、マウントされている同じ Azure Storage アカウントにすべてのインスタンスが接続します。 パフォーマンス ボトルネックとスループットの問題を回避するには、ストレージ アカウントに対して適切なパフォーマンス レベルを選択します。  
- ローカル データベース (SQLite など) や、ファイル ハンドルやロックに依存するその他のアプリケーションやコンポーネントに対しては、ストレージのマウントの使用をお勧めしません。 
- アプリで Azure Storage [プライベート エンドポイント](../storage/common/storage-private-endpoints.md)を使用しているときは、次の 2 つのアプリ設定を設定する必要があります。
    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`
- [ストレージのフェールオーバーを開始](../storage/common/storage-initiate-account-failover.md)する場合にストレージ アカウントがアプリにマウントされていると、アプリを再起動するか Azure Storage マウントを削除して追加するまでは、マウントが接続に失敗します。 

::: zone-end

## <a name="next-steps"></a>次の手順

::: zone pivot="container-windows"

- [カスタム コンテナーを使用してカスタム ソフトウェアを Azure App Service に移行する](tutorial-custom-container.md?pivots=container-windows)。

::: zone-end

::: zone pivot="container-linux"

- [カスタム コンテナーを構成する](configure-custom-container.md?pivots=platform-linux)。

::: zone-end