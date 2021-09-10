---
title: カスタム ゲスト構成パッケージの成果物の公開方法
description: ゲスト構成パッケージ ファイルを Azure Blob Storage に公開し、安全なアクセスのために SAS トークンを取得する方法について説明します。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: aa20bc3d9c47258c6ebedd2419cf830ba47c1c1c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868568"
---
# <a name="how-to-publish-custom-guest-configuration-package-artifacts"></a>カスタム ゲスト構成パッケージの成果物の公開方法

開始する前に、[ゲスト構成](../concepts/guest-configuration.md)の概要ページを参照することをお勧めします。

ゲスト構成のカスタム .zip パッケージは、管理されたマシンから HTTPS 経由でアクセスできる場所に格納する必要があります。 たとえば、GitHub リポジトリ、Azure リポジトリ、Azure Storage、またはプライベート データセンター内の Web サーバーなどです。

`Audit` と `AuditandSet` をサポートする構成パッケージは、同じ方法で公開されます。 パッケージ モードに基づいた公開時には、特別なことを行う必要はありません。

## <a name="publish-a-configuration-package"></a>構成パッケージの公開

構成パッケージの保存先として推奨される場所は Azure Blob Storage です。
ストレージ アカウントに特別な要件はありませんが、自分のコンピューターの近くのリージョンでファイルをホストすることをお勧めします。 パッケージを公開したくない場合は、URL に [SAS トークン](../../../storage/common/storage-sas-overview.md)を含めるか、マシンの[サービス エンドポイント](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)をプライベート ネットワークに実装することができます。

ストレージ アカウントがない場合は、次の例を使用して作成してください。

```powershell
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

`Publish-GuestConfigurationPackage` コマンドは、構成パッケージを Azure Blob Storage にアップロードし、SAS トークンを取得して安全にアクセスできるようにします。

`Publish-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**:発行するパッケージの場所
- **ResourceGroupName**:ストレージ アカウントが配置されているリソース グループの名前
- **StorageAccountName**:パッケージを発行する必要があるストレージ アカウントの名前
- **StorageContainerName**: (既定: _guestconfiguration_) ストレージ アカウントのストレージ コンテナーの名前
- **Force**:同じ名前のストレージ アカウント内の既存のパッケージを上書きする

次の例では、パッケージをストレージ コンテナー名 'guestconfiguration' に発行します。

```powershell
Publish-GuestConfigurationPackage -Path ./MyConfig.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName | % ContentUri
```

## <a name="next-steps"></a>次のステップ

- 開発環境から[パッケージ成果物をテストする](./guest-configuration-create-test.md)。
- `GuestConfiguration` モジュールを使用して、環境を大規模に管理するための [Azure Policy の定義を作成する](./guest-configuration-create-definition.md)。
- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成のポリシー割り当てのコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を確認する方法を学ぶ。
