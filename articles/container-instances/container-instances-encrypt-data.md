---
title: デプロイ データの暗号化
description: コンテナー インスタンス リソースに永続化されたデータの暗号化と、カスタマー マネージド キーでデータを暗号化する方法について説明します
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903692"
---
# <a name="encrypt-deployment-data"></a>デプロイ データの暗号化

クラウドで Azure Container Instances (ACI) リソースを実行すると、ACI サービスにより、コンテナーに関連するデータが収集されて永続化されます。 ACI では、このデータはクラウドに永続化されるときに自動的に暗号化されます。 この暗号化によってデータは保護され、組織のセキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 ACI では、ユーザー独自のキーでこのデータを暗号化するオプションも用意されており、ACI のデプロイに関するデータをさらにきめ細かく制御できます。

## <a name="about-aci-data-encryption"></a>ACI のデータ暗号化について 

ACI のデータは、256 ビット AES 暗号化を使用して暗号化および暗号化解除されます。 ACI のすべてのデプロイで有効になっているため、この暗号化を利用するために、デプロイまたはコンテナーを変更する必要はありません。 これには、デプロイに関するメタデータ、環境変数、コンテナーに渡されるキー、コンテナーが停止した後でも見られるように保持されるログが含まれます。 暗号化によってコンテナー グループのパフォーマンスが影響を受けることはなく、暗号化に追加コストはかかりません。

## <a name="encryption-key-management"></a>暗号化キーの管理

Microsoft のマネージド キーを利用してコンテナー データを暗号化することも、独自のキーで暗号化を管理することもできます。 次の表では、これらのオプションを比較します。 

|    |    Microsoft のマネージド キー     |     カスタマー マネージド キー     |
|----|----|----|
|    暗号化/暗号化解除の操作    |    Azure    |    Azure    |
|    キー記憶域    |    Microsoft キー ストア    |    Azure Key Vault    |
|    キーのローテーションの責任    |    Microsoft    |    Customer    |
|    キーへのアクセス    |    Microsoft のみ    |    Microsoft、顧客    |

このドキュメントの残りの部分では、キー (カスタマー マネージド キー) を使用して ACI のデプロイ データを暗号化するために必要な手順について説明します。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>カスタマー マネージド キーを使用してデータを暗号化する

### <a name="create-service-principal-for-aci"></a>ACI 用のサービス プリンシパルを作成する

最初のステップでは、[Azure テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)に、Azure Container Instances サービスへのアクセス許可を付与するためのサービス プリンシパルが割り当てられていることを確認します。 

次の CLI コマンドを実行すると、Azure 環境に ACI の SP がセットアップされます。

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

このコマンドを実行すると、"displayName": "Azure Container Instance Service" で設定されたサービス プリンシパルが表示されます。

### <a name="create-a-key-vault-resource"></a>Key Vault リソースを作成する

[Azure portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)、[CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)、または [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) を使用して、Azure Key Vault を作成します。 

キー コンテナーのプロパティについては、次のガイドラインを使用します。 
* 名前:一意の名前が必要です。 
* サブスクリプション:サブスクリプションを選択します。
* [リソース グループ] で、既存のリソース グループを選択するか、新しく作成してリソース グループ名を入力します。
* [場所] プルダウン メニューで、場所を選択します。
* 他のオプションは、既定値のままにしておいても、追加の要件に基づいて選択してもかまいません。

> [!IMPORTANT]
> カスタマー マネージド キーを使用して ACI のデプロイ テンプレートを暗号化する場合は、キー コンテナーで [論理的な削除] と [Do Not Purge]\(消去しない\) の 2 つのプロパティを設定することをお勧めします。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

### <a name="generate-a-new-key"></a>新しいキーを生成する 

キー コンテナーが作成されたら、Azure portal でリソースに移動します。 リソース ブレードの左側のナビゲーション メニューで、[設定] の **[キー]** をクリックします。 [キー] のビューで、[生成/インポート] をクリックして新しいキーを生成します。 このキーに対する一意の名前を指定し、他の設定は必要に応じて使用します。 

![新しいキーを生成する](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>アクセス ポリシーを設定する

ACI サービスによるキーへのアクセスを許可するための、新しいアクセス ポリシーを作成します。

* キーが生成されたら、キー コンテナー リソース ブレードに戻り、[設定] の下にある **[アクセス ポリシー]** をクリックします。
* キー コンテナーの [アクセス ポリシー] ページで、 **[アクセス ポリシーの追加]** をクリックします。
* *[キーのアクセス許可]* を、 **[取得]** と **[キーの折り返しを解除]** を含むように設定します ![キーのアクセス許可を設定する](./media/container-instances-encrypt-data/set-key-permissions.png)
* *[プリンシパルの選択]* で、**Azure Container Instance Service** を選択します
* 下部にある **[追加]** をクリックします 

アクセス ポリシーがお使いのキー コンテナーのアクセス ポリシーに表示されるようになります。

![新しいアクセス ポリシー](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON デプロイ テンプレートを変更する

> [!IMPORTANT]
> カスタマー マネージド キーによるデプロイ データの暗号化は、現在ロールアウト中の最新の API バージョン (2019-12-01) で使用できます。デプロイ テンプレートでこの API バージョンを指定してください。 これに関して何か問題がある場合は、Azure サポートにご連絡ください。

キー コンテナーのキーとアクセス ポリシーを設定した後は、次のプロパティを ACI のデプロイ テンプレートに追加します。 テンプレートを使用した ACI リソースのデプロイについて詳しくは、「[チュートリアル: Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)」をご覧ください。 

具体的には、デプロイ テンプレートのコンテナー グループ プロパティ セクションに、次の値を含む "encryptionProperties" を追加します。
* vaultBaseUrl: キー コンテナーの DNS 名。ポータルのキー コンテナー リソースの概要ブレードで確認できます
* keyName: 前に生成したキーの名前
* keyVersion: キーの現在のバージョン。 これは、キー自体をクリックして確認できます (キー コンテナー リソースの [設定] セクションの [キー] の下)


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>リソースをデプロイする

デスクトップ上でテンプレート ファイルを作成および編集した場合は、そのファイルをドラッグすることによって Cloud Shell ディレクトリにアップロードできます。 

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] コマンドで、テンプレートをデプロイします。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

数秒以内に、Azure から最初の応答を受信します。 デプロイが完了すると、ACI サービスによって保持されているそのデプロイに関連したデータはすべて、指定したキーで暗号化されます。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create