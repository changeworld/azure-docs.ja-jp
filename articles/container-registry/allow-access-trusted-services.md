---
title: 信頼された Azure サービスを使用してネットワーク制限付きレジストリにアクセスする
description: 信頼された Azure サービス インスタンスが、ネットワーク制限付きコンテナー レジストリに安全にアクセスしてイメージをプルまたはプッシュできるようにする
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 8d4379c8ea9935c366c94828e58bc236d9e4310f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327369"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>信頼されたサービスがネットワーク制限付きコンテナー レジストリに安全にアクセスできるようにする (プレビュー)

Azure Container Registry を使用すると、特定の信頼された Azure サービスが、ネットワーク アクセス ルールを使用して構成されたレジストリにアクセスすることを許可できます。 信頼されたサービスが許可されると、信頼されたサービス インスタンスは、レジストリのネットワーク ルールを安全にバイパスし、イメージのプルやプッシュなどの操作を実行できます。 この記事では、ネットワーク制限付き Azure コンテナー レジストリで信頼されたサービスを有効にして使用する方法について説明します。

この記事のサンプル コマンドは、Azure Cloud Shell または Azure CLI のローカル インストールを使用して実行します。 これをローカルで使用したい場合は、バージョン 2.18 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

信頼された Azure サービスによるレジストリ アクセスを許可することは、**プレビュー** 機能です。

## <a name="limitations"></a>制限事項

* マネージド ID を必要とするレジストリ アクセス シナリオでは、システムによって割り当てられた ID のみを使用できます。 ユーザー割り当てマネージド ID は現在サポートされていません。
* 信頼されたサービスを許可することは、[サービス エンドポイント](container-registry-vnet.md)で構成されたコンテナー レジストリには適用されません。 この機能は、[プライベート エンドポイント](container-registry-private-link.md)で制限されているか、[パブリック IP アクセス ルール](container-registry-access-selected-networks.md)が適用されているレジストリにのみ影響します。 

## <a name="about-trusted-services"></a>信頼されたサービスについて

Azure Container Registry には多層型セキュリティ モデルがあり、レジストリへのアクセスを制限する、次のものを含む複数のネットワーク構成がサポートされています。

* [Azure Private Link によるプライベート エンドポイント](container-registry-private-link.md)。 構成した場合、レジストリのプライベート エンドポイントは、プライベート IP アドレスを使用して、仮想ネットワーク内のリソースにのみアクセスできます。  
* [レジストリのファイアウォール規則](container-registry-access-selected-networks.md)。これは、特定のパブリック IP アドレスまたはアドレス範囲からのみレジストリのパブリック エンドポイントへのアクセスを許可します。 プライベート エンドポイントの使用時には、パブリック エンドポイントへのすべてのアクセスをブロックするようにファイアウォールを構成することもできます。

仮想ネットワークにデプロイされている場合、またはファイアウォール規則で構成されている場合、レジストリでは、それらのソースの外部からのユーザーまたはサービスへのアクセスが拒否されます。 

いくつかのマルチテナント Azure サービスは、これらのレジストリ ネットワーク設定に含めることができないネットワークから動作するため、レジストリに対してイメージをプルしたりプッシュしたりするなどの操作の実行が妨げられます。 特定のサービス インスタンスを "信頼済み" として指定することで、レジストリ所有者は、選択された Azure リソースが、レジストリ操作を実行するために、レジストリのネットワーク設定を安全にバイパスできるようにすることができます。 

### <a name="trusted-services"></a>信頼できるサービス

レジストリの **信頼されたサービスを許可する** 設定が有効になっている場合 (既定)、次のサービスのインスタンスはネットワーク制限付きコンテナー レジストリにアクセスできます。 今後、さらに多くのサービスが追加される予定です。

ここで示されている場合、信頼されたサービスによるアクセスでは、サービス インスタンスでのマネージド ID の追加の構成、[RBAC ロール](container-registry-roles.md)の割り当て、およびレジストリによる認証が必要になります。 手順の例については、この記事の後半の「[信頼されたサービスのワークフロー](#trusted-services-workflow)」を参照してください。

|信頼できるサービス  |サポートされる使用シナリオ  | RBAC ロールによるマネージド ID の構成
|---------|---------|------|
| Microsoft Defender for Cloud | [Microsoft Defender for container registries](scan-images-defender.md) による脆弱性スキャン | いいえ |
|ACR タスク     | [ACR タスクから親レジストリまたは別のレジストリにアクセスする](container-registry-tasks-cross-registry-authentication.md)       | はい |
|Machine Learning | カスタムの Docker コンテナー イメージを使用して、Machine Learning ワークスペースでモデルを[デプロイ](../machine-learning/how-to-deploy-custom-container.md)または[トレーニング](../machine-learning/how-to-train-with-custom-image.md)する | はい |
|Azure Container Registry | ネットワークで制限された Azure Container Registry との間で[イメージをインポート](container-registry-import-images.md)する | いいえ |

> [!NOTE]
> 現時点では、信頼されたサービスを許可する設定を有効にしても、App Service や Azure Container Instances を含む他の特定のマネージド Azure サービスには適用されません。

## <a name="allow-trusted-services---cli"></a>信頼されたサービスを許可する - CLI

既定では、信頼されたサービスを許可する設定は、新しい Azure コンテナー レジストリで有効になっています。 [az acr update](/cli/azure/acr#az_acr_update) コマンドを実行して、設定を無効または有効にします。

無効にするには:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

既存のレジストリ、または設定が既に無効になっているレジストリで設定を有効にするには、次のようにします。

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>信頼されたサービスを許可する - ポータル

既定では、信頼されたサービスを許可する設定は、新しい Azure コンテナー レジストリで有効になっています。 

ポータルで設定を無効にするか、再度有効にするには、次のようにします。

1. ポータルで、自分のコンテナー レジストリに移動します。
1. **[設定]** で **[ネットワーク]** を選択します。 
1. **[Allow public network access]\(パブリック ネットワーク アクセスを許可する\)** で、 **[選択されたネットワーク]** または **[無効]** を選択します。
1. 次のいずれかの操作を行います。
    * 信頼されたサービスによるアクセスを無効にするには、 **[ファイアウォールの例外]** で、 **[信頼された Microsoft サービスによるこのコンテナー レジストリへのアクセスを許可]** のチェックをオフにします。 
    * 信頼されたサービスを許可するには、 **[ファイアウォールの例外]** で、 **[信頼された Microsoft サービスによるこのコンテナー レジストリへのアクセスを許可]** のチェックをオンにします。
1. **[保存]** を選択します。

## <a name="trusted-services-workflow"></a>信頼されたサービスのワークフロー

信頼されたサービスのインスタンスが、ネットワーク制限付きコンテナー レジストリにアクセスできるようにするための一般的なワークフローをここに示します。 このワークフローは、サービス インスタンスのマネージド ID を使用してレジストリのネットワーク ルールをバイパスする場合に必要です。

1. Azure Container Registry の[信頼されたサービス](#trusted-services)の 1 つのインスタンスで、[Azure リソースのシステム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にします。
1. ID に、レジストリに対する [Azure ロール](container-registry-roles.md) を割り当てます。 たとえば、コンテナー イメージをプルするには、ACRPull ロールを割り当てます。
1. ネットワーク制限付きレジストリで、信頼されたサービスによるアクセスを許可するように設定を構成します。
1. ID の資格情報を使用して、ネットワーク制限付きレジストリで認証します。 
1. レジストリからイメージをプルするか、ロールで許可されている他の操作を実行します。

### <a name="example-acr-tasks"></a>例:ACR タスク

次の例は、信頼されたサービスとして ACR タスクを使用する方法を示しています。 タスクの詳細については、「[ACR タスクでの Azure マネージド ID を使用したクロスレジストリ認証](container-registry-tasks-cross-registry-authentication.md)」を参照してください。

1. Azure コンテナー レジストリを作成または更新します。
ACR タスクを[作成](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity)します。 
    * タスクの作成時に、システム割り当てマネージド ID を有効にします。
    * タスクの既定の認証モード (`--auth-mode None`) を無効にします。
1. タスク ID に、[レジストリにアクセスするための Azure ロール](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)を割り当てます。 たとえば、イメージをプルおよびプッシュするアクセス許可を持つ AcrPush ロールを割り当てます。
2. タスクに[レジストリのマネージド ID 資格情報を追加](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task)します。
3. タスクがネットワーク制限をバイパスすることを確認するために、レジストリで[パブリック アクセスを無効にします](container-registry-access-selected-networks.md#disable-public-network-access)。
4. タスクを実行します。 レジストリとタスクが適切に構成されている場合は、レジストリによってアクセスが許可されるため、タスクは正常に実行されます。

信頼されたサービスによるアクセスを無効にするテストを行うには、次のようにします。

1. 信頼されたサービスによるアクセスを許可する設定を無効にします。
1. タスクを再び実行します。 この場合、レジストリでタスクによるアクセスが許可されなくなるため、タスクの実行は失敗します。

## <a name="next-steps"></a>次のステップ

* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* レジストリのファイアウォール規則を設定するには、「[パブリック IP ネットワーク ルールを構成する](container-registry-access-selected-networks.md)」を参照してください。
