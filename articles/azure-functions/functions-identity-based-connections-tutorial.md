---
title: 既定のストレージ シークレットが定義に含まれない関数アプリを作成する
titleSuffix: Azure Functions
ms.service: azure-functions
description: 関数アプリの定義から Storage 接続文字列を削除する方法について説明します。
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 8e6e4bfc80f2154843aab5d3971854054f4319b4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271217"
---
# <a name="tutorial-create-a-function-app-that-connects-to-azure-services-using-identities-instead-of-secrets"></a>チュートリアル: シークレットではなく ID を使用して Azure サービスに接続する関数アプリを作成する

このチュートリアルでは、可能な限り、シークレットや接続文字列ではなく Azure Active Directory の ID を使用する関数アプリを構成する方法について説明します。 ID を使用すると、機密シークレットが誤って漏えいするのを防ぐのに役立ち、データへのアクセス方法をより詳細に可視化できます。 ID ベースの接続について詳しくは、「[ID ベースの接続を構成する](functions-reference.md#configure-an-identity-based-connection)」をご覧ください。

ここで示す手順は一般にすべての言語で動作しますが、このチュートリアルで現在サポートされているのは特に Windows 上の C# クラス ライブラリ関数です。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * ARM テンプレートを使用して Azure で関数アプリを作成する
> * 関数アプリでシステム割り当てとユーザー割り当ての両方のマネージド ID を有効にする
> * 他のリソースへのアクセス許可を付与するロールの割り当てを作成する
> * ID に置き換えることができないシークレットを Azure Key Vault に移動する
> * マネージド ID を使用して既定のホスト ストレージに接続するようにアプリを構成する

このチュートリアルを完了した後は、[トリガーとバインドでシークレットではなく ID ベースの接続を使用する]方法が示されている、後続のチュートリアルを行う必要があります。 

## <a name="prerequisites"></a>前提条件

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) バージョン 3.x。

## <a name="why-use-identity"></a>ID を使用する理由

シークレットと資格情報の管理は、あらゆる規模のチームに共通する課題です。 シークレットは、盗難や偶発的な露見から保護する必要があり、定期的にローテーションすることが必要な場合があります。 多くの Azure サービスでは、代わりに [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) の ID を使用してクライアントの認証を行い、アクセス許可を調べて迅速に変更したり取り消したりすることができます。 これにより、より少ない運用オーバーヘッドで、アプリケーションのセキュリティの制御を強化できます。 ID は、アプリケーションの開発者などの人間のユーザーの場合も、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) が設定された Azure で実行されるアプリケーションの場合もあります。

一部のサービスでは Azure Active Directory による認証がサポートされていないので、アプリケーションでシークレットがまだ必要な場合があります。 ただし、これらは [Azure Key Vault](../key-vault/general/overview.md) に格納できます。これは、シークレットの管理ライフサイクルを簡素化するのに役立ちます。 キー コンテナーへのアクセスも、ID で制御されます。

可能な場合はシークレットではなく ID を使い、それができないときは Key Vault を使う方法を理解すると、リスクを軽減し、運用のオーバーヘッドを減らし、一般にアプリケーションのセキュリティ態勢を向上させることができます。

## <a name="create-a-function-app-that-uses-key-vault-for-necessary-secrets"></a>必要なシークレットに Key Vault を使用する関数アプリを作成する

Azure Files は、SMB ファイル共有用の Azure Active Directory 認証がまだサポートされていないサービスの例です。 Azure Files は、Premium プランと従量課金プランでの Windows のデプロイに対する既定のファイル システムです。 [Azure Files を完全に削除する](./storage-considerations.md#create-an-app-without-azure-files)ことはできますが、それによって望ましくない制限が発生する場合があります。 代わりに、Azure Files の接続文字列を Azure Key Vault に移動します。 そうすることで、ID によって制御されるアクセスを使用して一元的に管理されます。

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する

まず、シークレットを格納するキー コンテナーが必要になります。 コンテナーからシークレットを読み取ることができるユーザーを決定するために、[Azure ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用するように、それを構成します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** (+) を選びます。

1. **[リソースの作成]** ページで、 **[セキュリティ]**  >  **[Key Vault]** を選びます。

1. **[基本]** ページで、下の表を使用してキー コンテナーを構成します。

    | オプション      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい関数アプリが作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 関数アプリを作成する新しいリソース グループの名前。 |
    | **キー コンテナー名** | グローバルに一意の名前 | 新しいキー コンテナーを識別する名前。 コンテナー名に使用できるのは英数字とダッシュのみで、数字で始めることはできません。 |
    | **価格レベル** | Standard | 課金のオプション。 このチュートリアルには Standard で十分です。 |
    |**リージョン**| 優先リージョン | ユーザー、または関数がアクセスする他のサービスに近い[リージョン](https://azure.microsoft.com/regions/)を選択します。 |

    [回復オプション] セクションでは、既定の選択をそのまま使用します。 

1. 後で必要になるので、使用した名前を記録しておきます。

1. **[次へ: アクセス ポリシー]** をクリックして、 **[アクセス ポリシー]** タブに移動します。

1. **[アクセス許可モデル]** で、 **[Azure ロールベースのアクセス制御]** を選びます

1. **[Review + create]\(レビュー + 作成\)** を選択します。 構成を確認し、 **[作成]** をクリックします。

### <a name="set-up-an-identity-and-permissions-for-the-app"></a>アプリの ID とアクセス許可を設定する

Azure Key Vault を使うには、シークレットを読み取るためのアクセス許可を付与できる ID がアプリに必要です。 このアプリでは、アプリが作成される前であってもアクセス許可を設定できるように、ユーザー割り当ての ID を使います。 Azure Functions 用のマネージド ID について詳しくは、[Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)に関するトピックをご覧ください。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** (+) を選びます。

1. **[リソースの作成]** ページで、 **[ID]**  >  **[ユーザー割り当てマネージド ID]** を選びます。

1. **[基本]** ページで、下の表を使用して ID を構成します。

    | オプション      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい関数アプリが作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 関数アプリを作成する新しいリソース グループの名前。 |
    |**リージョン**| 優先リージョン | ユーザー、または関数がアクセスする他のサービスに近い[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
    | **名前** | グローバルに一意の名前 | 新しいユーザー割り当て ID を識別する名前。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 構成を確認し、 **[作成]** をクリックします。

1. ID が作成されたら、ポータルでそれに移動します。 **[プロパティ]** を選び、後で必要になるので **[リソースID]** を記録しておきます。

1. **[Azure ロールの割り当て]** を選び、 **[ロールの割り当てを追加 (プレビュー)]** を選びます。

1. **[ロールの割り当てを追加 (プレビュー)]** ページでは、次の表に示すオプションを使用します。

    | オプション      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **スコープ** |  Key Vault |  スコープは、ロールの割り当てが適用される一連のリソースです。 スコープには、下位のレベルで継承されるレベルがあります。 たとえば、サブスクリプション スコープを選ぶと、そのロールの割り当ては、サブスクリプション内のすべてのリソース グループとリソースに適用されます。 |
    |**サブスクリプション**| 該当するサブスクリプション | この新しい関数アプリが作成されるサブスクリプション。 |
    |**リソース**| 自分のキー コンテナー | 前に作成したキー コンテナー。 |
    | **ロール** | Key Vault Secrets User | ロールは、付与されているアクセス許可のコレクションです。 キー コンテナー シークレット ユーザーでは、コンテナーからシークレット値を読み取るためのアクセス許可が ID に付与されます。 |

1. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。 ID のロール割り当て一覧を更新したとき、ロールが表示されるまでに 1 から 2 分かかることがあります。

その ID で、コンテナーに格納されているシークレットを読み取ることができるようになります。 このチュートリアルで後ほど、さまざまな目的のロール割り当てをさらに追加します。

### <a name="generate-a-template-for-creating-a-function-app"></a>関数アプリを作成するためのテンプレートを生成する

関数アプリを作成するためのポータル エクスペリエンスは Azure Key Vault とやりとりしないため、Azure Resource Manager テンプレートを生成して編集する必要があります。 その後、このテンプレートを使って、キー コンテナーの Azure Files 接続文字列を参照する関数アプリを作成できます。

> [!IMPORTANT]
> ARM テンプレートを編集するまでは、関数アプリを作成しないでください。 Azure Files の構成は、アプリの作成時に設定する必要があります。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** (+) を選びます。

1. **[リソースの作成]** ページで、 **[コンピューティング]**  >  **[関数アプリ]** を選びます。

1. **[基本]** ページで、下の表を使用して関数アプリを構成します。

    | オプション      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい関数アプリが作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 関数アプリを作成する新しいリソース グループの名前。 |
    | **関数アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。  |
    |**発行**| コード | コード ファイルまたは Docker コンテナーの発行を選択します。 |
    | **ランタイム スタック** | .NET | このチュートリアルでは、.NET を使用します。 |
    |**リージョン**| 優先リージョン | ユーザー、または関数がアクセスする他のサービスに近い[リージョン](https://azure.microsoft.com/regions/)を選択します。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 このアプリでは、 **[ホスティング]** ページと **[監視]** ページについては既定値を使います。 既定のオプションを確認することをお勧めします。これらは、生成する ARM テンプレートに組み込まれます。 

1. ここで関数アプリを作成するのではなく、 **[次へ]** ボタンの右側にある **[Automation のテンプレートをダウンロードする]** を選びます。

1. テンプレート ページで **[デプロイ]** を選び、[カスタム デプロイ] ページで **[テンプレートの編集]** を選びます。

    :::image type="content" source="./media/functions-identity-connections-tutorial/function-app-portal-template-deploy-button.png" alt-text="テンプレート画面上部の [デプロイ] ボタンがある場所のスクリーンショット。":::

### <a name="edit-the-template"></a>テンプレートの編集

次に、テンプレートを編集して、Key Vault に Azure Files 接続文字列を格納し、関数アプリでそれを参照できるようにします。 続ける前に、これまでのセクションで出てきた次の値がわかっていることを確認します。

- ユーザー割り当て ID のリソース ID
- キー コンテナーの名前

> [!NOTE]
> 自動化のための完全なテンプレートを作成する場合は、適切な `dependsOn` 句を使って、ID とロール割り当てのリソースの定義を含める必要があります。 これにより、ポータルを使用していたこれまでの手順が置き換えられます。 [Azure Resource Manager のガイダンス](../azure-resource-manager/templates/syntax.md)と各サービスのドキュメントをご覧ください。


1. エディターで、`resources` 配列の開始位置を見つけます。 関数アプリの定義の前に、Azure Files の接続文字列を Key Vault に格納する次のセクションを追加します。 "VAULT_NAME" は、実際のキー コンテナーの名前に置き換えます。

    ```json
    {
        "type": "Microsoft.KeyVault/vaults/secrets",
        "apiVersion": "2016-10-01",
        "name": "VAULT_NAME/azurefilesconnectionstring",
        "properties": {
            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
        },
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
        ]
    },
    ``` 
    
1. 関数アプリ リソースの定義 (`type` が `Microsoft.Web/sites` に設定されているもの) で、`Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring` を `dependsOn` 配列に追加します。 ここでも、"VAULT_NAME" は、実際のキー コンテナーの名前に置き換えます。 これにより、そのシークレットが定義される前にアプリが作成されなくなります。 `dependsOn` 配列は、次の例のようになります。

    ```json
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": null,
            "dependsOn": [
                "microsoft.insights/components/idcxntut",
                "Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring",
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            // ...
        }
    ```

1. 次の例の `identity` ブロックを、関数アプリ リソースの定義に追加します。 "IDENTITY_RESOURCE_ID" は、ユーザー割り当て ID のリソース ID に置き換えます。

    ```json
    {
        "apiVersion": "2018-11-01",
        "name": "[parameters('name')]",
        "type": "Microsoft.Web/sites",
        "kind": "functionapp",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities": {
                "IDENTITY_RESOURCE_ID": {}
            }
        },
        "tags": null,
        // ...
    }
    ```

    また、この `identity` ブロックにより、このチュートリアルで後ほど使用するシステム割り当て ID も設定されます。

1. 次の例のように、関数アプリの `properties` オブジェクトに `keyVaultReferenceIdentity` プロパティを追加します。 "IDENTITY_RESOURCE_ID" は、ユーザー割り当て ID のリソース ID に置き換えます。

    ```json
    {
        // ...
         "properties": {
                "name": "[parameters('name')]",
                "keyVaultReferenceIdentity": "IDENTITY_RESOURCE_ID",
                // ...
         }
    }
    ```

    この構成は、アプリに複数のユーザー割り当て ID が構成されている可能性があるため必要です。 ユーザー割り当て ID を使うときは常に、どの ID のものかを指定する必要があります。 システム割り当て ID の場合は、アプリに 1 つしか存在しないため、これは当てはまりません。 マネージド ID を使う多くの機能では、既定でシステム割り当て ID を使う必要があるものと想定されています。

1. `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` のアプリケーション設定が定義されている JSON オブジェクトを見つけます。次の例のようになっているはずです。

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
    },
    ```

1. 次の例に示すように、`value` フィールドをシークレットへの参照に置き換えます。 "VAULT_NAME" は、実際のキー コンテナーの名前に置き換えます。

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('@Microsoft.KeyVault(SecretUri=', reference(resourceId('Microsoft.KeyVault/vaults/secrets', 'VAULT_NAME', 'azurefilesconnectionstring')).secretUri, ')')]"
    },
    ```

1. **[保存]** を選んで、更新した ARM テンプレートを保存します。

### <a name="deploy-the-modified-template"></a>変更したテンプレートをデプロイする

1. **リソース グループ** など、作成したオプションが正しいことを確認し、 **[確認および作成]** を選びます。

1. テンプレートが検証されたら、 **[ストレージ アカウント名]** を記録しておきます。後でこのアカウントを使います。 最後に、 **[作成]** を選んで Azure リソースを作成し、関数アプリにコードをデプロイします。 

1. デプロイが完了したら、 **[リソース グループに移動]** を選んでから、新しい関数アプリを選びます。 

おめでとうございます。 これで、Azure Key Vault の Azure Files 接続文字列を参照する関数アプリが正しく作成されました。

アプリでシークレットへの参照を追加する必要がある場合は、Key Vault に格納されている値を指し示す新しいアプリケーション設定を定義するだけで済みます。 これについて詳しくは、[Azure Functions での Key Vault の参照](../app-service/app-service-key-vault-references.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)に関する記事で学習できます。

> [!TIP]
> [Application Insights 接続文字列](../azure-monitor/app/sdk-connection-string.md)とそれに含まれるインストルメンテーション キーは、シークレットとは見なされず、[Reader](../role-based-access-control/built-in-roles.md#reader) アクセス許可を使用して App Insights から取得できます。 それらを Key Vault に移動することは、できますが、必要ありません。

## <a name="use-managed-identity-for-azurewebjobsstorage-preview"></a>AzureWebJobsStorage にマネージド ID を使用する (プレビュー)

次に、前の手順で構成したシステム割り当て ID を、`AzureWebJobsStorage` の接続に使用します。 `AzureWebJobsStorage` は、複数の実行中のインスタンスを調整するために、Functions ランタイムおよびいくつかのトリガーとバインディングによって使用されます。 これは関数アプリが動作するために必要であり、Azure Files のように、新しい関数アプリを作成するときに既定で接続文字列を使用して構成されます。

### <a name="grant-the-system-assigned-identity-access-to-the-storage-account"></a>システム割り当て ID にストレージ アカウントへのアクセス権を付与する

ユーザー割り当て ID とキー コンテナーに関して前に行った手順と同様に、ストレージ アカウントへのアクセスをシステム割り当て ID に許可するロールの割り当てを作成します。

1. [Azure portal](https://portal.azure.com) で、前に関数アプリで作成したストレージ アカウントに移動します。

1. **[アクセス制御 (IAM)]** を選択します。 ここで、リソースにアクセスできるユーザーを表示および構成できます。

1. **[追加]** をクリックし、 **[ロールの割り当てを追加]** を選びます。

1. **[ストレージ BLOB データ所有者]** を見つけて選び、 **[次へ]** をクリックします

1. **[メンバー]** タブの **[アクセスの割り当て先]** で、 **[マネージド ID]** を選びます

1. **[メンバーの選択]** をクリックして、 **[マネージド ID の選択]** パネルを開きます。

1. **[サブスクリプション]** が、前にリソースを作成したものであることを確認します。

1. **[マネージド ID]** セレクターで、 **[システム割り当てマネージド ID]** カテゴリから **[関数アプリ]** を選びます。 ラベル "関数アプリ" の横のかっこ内に、サブスクリプション内でシステム割り当て ID を持っているアプリの数を示す値が含まれている場合があります。

1. 入力フィールドの下の一覧に、自分のアプリが表示されているはずです。 表示されていない場合は、 **[選択]** ボックスを使って、アプリの名前で結果をフィルター処理できます。

1. 自分のアプリケーションをクリックします。 下の **[選択したメンバー]** セクションにそれが移動します。 <bpt id="p1">**</bpt>[選択]<ept id="p1">**</ept> をクリックします。

1. **[ロールの割り当てを追加]** 画面に戻り、 **[レビューと割り当て]** をクリックします。 構成を確認し、 **[レビューと割り当て]** をクリックします。

### <a name="edit-the-azurewebjobsstorage-configuration"></a>AzureWebJobsStorage の構成を編集する

次に、ホスト ストレージに BLOB サービスを使用するときにシステム割り当て ID を使用するように、関数アプリを更新します。

> [!IMPORTANT]
> `AzureWebJobsStorage` の構成は一部のトリガーとバインドによっても使用され、それらの拡張機能で ID ベースの接続を使用できる必要があります。 BLOB トリガーまたはイベント ハブ トリガーを使用するアプリでは、それらの拡張機能を更新することが必要になる場合があります。 このアプリには関数が定義されていないので、まだ問題はありません。 この要件について詳しくは、「[ID を使用するホスト ストレージへの接続 (プレビュー)](./functions-reference.md#connecting-to-host-storage-with-an-identity-preview)」をご覧ください。
>
> 同様に、Linux 従量課金プランでサーバー側ビルドを使用する場合は、`AzureWebJobsStorage` がデプロイ成果物に使用されます。 Linux 従量課金プランで `AzureWebJobsStorage` に対して ID ベースの接続を有効にするときは、[外部デプロイ パッケージ](/run-functions-from-deployment-package)を使ってデプロイする必要があります。

1. [Azure portal](https://portal.azure.com) で関数アプリに移動します。

1. **[設定]** の下で **[構成]** を選択します。

1. **AzureWebJobsStorage** アプリケーション設定の横にある **[編集]** ボタンを選び、次の値に基づいて変更します。

    | オプション      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **名前** |  AzureWebJobsStorage__accountName | **AzureWebJobsStorage** からの名前を、正確な名前 `AzureWebJobsStorage__accountName` に更新します。 この設定は、格納されているシークレットを探す代わりに ID を使用するようホストに指示します。 新しい設定で使用されている二重アンダースコア (`__`) は、アプリケーション設定での特殊文字です。  |
    | **Value** | 自分のアカウント名 | 接続文字列から **AccountName** だけに名前を更新します。 |

    この構成により、ID を使ってリソースに接続する必要があることをシステムが認識します。

1. **[OK]** を選んでから、 **[保存]**  >  **[続行]** を選んで、変更を保存します。 

代わりにマネージド ID を使って BLOB に接続するようにアプリを構成することで、AzureWebJobsStorage に対するストレージ接続文字列の要件をなくしました。  

## <a name="next-steps"></a>次のステップ 

このチュートリアルでは、シークレットを構成に格納せずに関数アプリを作成する方法について説明しました。

次のチュートリアルでは、トリガーおよびバインドの接続で ID を使用する方法について説明します。

> [!div class="nextstepaction"]
> [トリガーとバインドでシークレットではなく ID ベースの接続を使用する]

[トリガーとバインドでシークレットではなく ID ベースの接続を使用する]: ./functions-identity-based-connections-tutorial-2.md
