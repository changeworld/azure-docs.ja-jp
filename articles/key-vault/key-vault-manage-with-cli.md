---
title: "CLI を使用した Azure Key Vault の管理 | Microsoft Docs"
description: "このチュートリアルを活用し、CLI を使用した Key Vault での一般的なタスクを自動化します"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>CLI を使用した Key Vault の管理

このチュートリアルを使用すると、Azure Key Vault で強化されたコンテナー (キー コンテナー) を Azure に作成できます。 Azure Key Vault は、暗号化キーとシークレットを格納して管理するために使用されます。 この記事では、Azure クロスプラットフォーム コマンド ライン インターフェイスを使用してコンテナーを作成する手順を説明します。 作成後に、コンテナーと対話して、さまざまな一般的な操作を実行します。 

Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

**推定所要時間:** 20 分

> [!NOTE]
> このチュートリアルには、いずれかの手順に含まれている Azure アプリケーションを記述する方法に関する手順は含まれていません。 サンプル アプリケーションは、キー コンテナーに格納されているキーまたはシークレットを使用するためにアプリケーションがどのように承認されるかを示すために使用されています。
> この記事は、クロスプラットフォーム コマンド ライン インターフェイスを使用した Azure Key Vault の構成について説明しています。 Azure PowerShell の手順については、[こちらのチュートリアル](key-vault-get-started.md)をご覧ください。

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、 [無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* コマンドライン インターフェイス バージョン 0.9.1 以降。 最新バージョンをインストールして、Azure サブスクリプションに接続するには、「 [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](../cli-install-nodejs.md)」を参照してください。
* このチュートリアルで作成したキーやパスワードを使用して構成されるアプリケーション。 サンプル アプリケーションは、[Microsoft ダウンロード センター](http://www.microsoft.com/download/details.aspx?id=45343)から入手できます。 手順については、付属の Readme ファイルをご覧ください。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Azure クロスプラットフォーム コマンドライン インターフェイスでのヘルプの取得

このチュートリアルでは、コマンドライン インターフェイス (Bash、Terminal、Command プロンプト) に慣れていることを前提としています。

--help または -h パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。 または、azure help [コマンド][オプション] の形式で指定しても、同じ情報が返されます。 たとえば、次のコマンドでは、すべて同じ情報が返されます。

    azure account set --help

    azure account set -h

    azure help account set

コマンドに必要なパラメーターがわからない場合は、--help、-h、または azure help [コマンド] を使用してヘルプを参照してください。

また、次のチュートリアルを読めば、Azure クロスプラットフォーム コマンド ライン インターフェイスでの Azure Resource Manager のデプロイについて詳しく理解できます。

* [Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成方法](../cli-install-nodejs.md)
* [Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>サブスクリプションへの接続

次のコマンドを使用して Azure にログインします。

```azurecli-interactive
azure login -u username -p password
```

また、対話形式で入力してログインする場合には、次のコマンドを使用します。

```azurecli-interactive
azure login
```

複数のサブスクリプションがあり、特定の 1 つのサブスクリプションを指定して Azure Key Vault を使用する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

```azurecli-interactive
azure account list
```

使用するサブスクリプションを指定するには、次のように入力します。

```azurecli-interactive
azure account set <subscription name>
```

Azure クロスプラットフォーム コマンドライン インターフェイスの構成方法については、「 [Azure クロスプラットォーム コマンドライン インターフェイスのインストールと構成方法](../cli-install-nodejs.md)」をご覧ください。

## <a name="switch-to-using-azure-resource-manager"></a>Azure リソース マネージャーの使用に切り替える
Key Vault では Azure リソース マネージャーが必要なため、次のように入力して Azure リソース マネージャー モードに切り替えます。

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>新しいリソース グループを作成する
Azure リソース マネージャーを使用すると、すべての関連するリソースが 1 つのリソース グループ内に作成されます。 このチュートリアルでは、'ContosoResourceGroup' という新しいリソース グループを作成します。

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

最初のパラメーターはリソース グループ名で、2 番目のパラメーターは場所です。 場所の場合には、 `azure location list` コマンドを使用して別の場所をこの例の場所に指定する方法を識別します。 詳細情報が必要な場合は、 `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Key Vault リソース プロバイダーの登録
Key Vault リソース プロバイダーがサブスクリプションに登録されていることを確認します。

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

これは、サブスクリプションごとに 1 回だけ実行する必要があります。

## <a name="create-a-key-vault"></a>Key Vault を作成します

`azure keyvault create` コマンドを使用して、Key Vault を作成します。 このスクリプトには、3 つの必須パラメーター (リソース グループ名、Key Vault 名、地理的な場所) が含まれています。

次に例を示します。
- 使用するコンテナー名: **ContosoKeyVault**
- リソース グループ名: **ContosoResourceGroup**
- 場所: **東アジア**

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

このコマンドの出力は、作成されたばかりの Key Vault のプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **Name**: この例では、これは ContosoKeyVault です。 この名前を他の Key Vault コマンドレットに使用できます。
* **vaultUri**: この例では、これは https://contosokeyvault.vault.azure.net です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 まだ、どのユーザーも許可されていません。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>キーやシークレットを Key Vault に追加します

Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、`azure key create` コマンドを使用して次のように入力します。

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

ただし、Azure Key Vault にアップロードする softkey.pem という名前の、ローカル ファイルとして保存された .pem ファイル内に既存のキーがある場合には、キーを .PEM ファイルからインポートするために次のコマンドを入力します。これにより Key Vault サービスでソフトウェアによりキーが保護されます。

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** を使用すると、その特定のバージョンを取得できます。

資格情報コンテナーにシークレットを追加するには (SQLPassword という名前のパスワードで、Azure Key Vault に Pa$$w0rd 値を設定)、次のように入力します。

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** を使用すると、その特定のバージョンを取得できます。

作成したキーやシークレットを表示してみましょう。

* キーを表示するには、次のように入力します。`azure keyvault key list --vault-name 'ContosoKeyVault'`
* シークレットを表示するには、次のように入力します。`azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録します

この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有のものではありませんが、完全を期すために説明します。

> [!IMPORTANT]
> チュートリアルを完了するには、この手順で登録するアカウント、コンテナー、アプリケーションがすべて同じ Azure Active Directory に格納されている必要があります。

Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。 これを行うには、アプリケーションの所有者は、まず Azure Active Directory でアプリケーションを登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

- **アプリケーション ID** 
- **認証キー** (共有シークレットとも呼ばれます) 

アプリケーションは、トークンを取得するために、この 2 つの値を Azure Active Directory に示す必要があります。 これを行うようにアプリケーションを構成する方法は、アプリケーションによって異なります。 [Key Vault のサンプル アプリケーション](https://www.microsoft.com/download/details.aspx?id=45343)の場合は、アプリケーション所有者がこれらの値を app.config ファイルに設定します。

Azure Active Directory にアプリケーションを登録する手順の詳細については、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/active-directory-integrating-applications.md)」または「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)」の記事を参照してください。アプリケーションを Azure Active Directory に登録するには:

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側にある **[アプリの登録]** をクリックします。 [アプリの登録] が表示されない場合は、**[その他のサービス]** をクリックすると表示されます。  
    >[!NOTE]
    キー コンテナーを作成した Azure サブスクリプションが含まれている、同じディレクトリを選択する必要があります。 
3. **[新しいアプリケーションの登録]** をクリックします。
4. **[作成]** ブレードでアプリケーションの名前を入力し、**[Web アプリケーションや Web API]** (既定値) を選択して、Web アプリケーションの**サインオン URL** を指定します。 この時点でこの情報がない場合は、この手順用に構成できます (たとえば、http://test1.contoso.com と指定できます)。 これらのサイトが存在するかどうかは関係ありません。 

   ![[新しいアプリケーションの登録]](./media/key-vault-manage-with-cli/new-application-registration.png)

5. **[作成]** ボタンをクリックします。
6. アプリの登録が完了すると、登録済みのアプリの一覧が表示されます。 先ほど登録したアプリを探してクリックします。
7. **[登録済みのアプリケーション]** ブレードをクリックし、**アプリケーション ID** をコピーします。
8. **[すべての設定]** をクリックします。
9. **[設定]** ブレードで **[キー]** をクリックします。
10. **[キーの説明]** ボックスに説明を入力し、期間を選択して、**[保存]** をクリックします。 ページが更新され、キーの値が表示されます。 
11. **[アプリケーション ID]** と **[キー]** の情報は、次の手順でコンテナーのアクセス許可を設定する際に使用します。

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>キーまたはシークレットを使用してアプリケーションを承認します
コンテナー内のキーまたはシークレットにアクセスするアプリケーションを承認するには、次のコマンドを使用します。

```azurecli-interactive
azure keyvault set-policy
```

たとえば、資格情報コンテナー名が ContosoKeyVault で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のように実行します。

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Windows コマンド プロンプトで実行する場合は、単一引用符を二重引用符に置き換え、内側の二重引用符をエスケープします。 たとえば、"[\"decrypt\",\"sign\"]" のようになります。
> 

その同じアプリケーションを認証し、資格情報コンテナーのシークレットの読み取りを許可する場合、次を実行します。

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>ハードウェア セキュリティ モジュール (HSM) を使用する場合
さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、 [Key Vault と関連するキーとシークレットを削除する](#delete-the-key-vault-and-associated-keys-and-secrets)に進んでください。

これらの HSM で保護されたキーを作成するには、HSM で保護されたキーをサポートする資格情報コンテナーのサブスクリプションが必要です。

資格情報コンテナーを作成するときに、'sku' パラメーターを追加します。

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

この資格情報コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、Destination パラメーターを 'HSM' に設定します。

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

次のコマンドを使用して、自分のコンピューターの .pem ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

次のコマンドは、“bring your own key" (BYOK) パッケージをインポートします。 これを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)」をご覧ください。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Key Vault と関連するキーとシークレットを削除する
Key Vault と、これに含まれるキーやシークレットが不要になった場合は、Azure Key Vault の削除コマンドを使用して Key Vault を削除できます。

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>その他の Azure クロスプラットフォーム コマンドライン インターフェイスのコマンド
Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

このコマンドは、すべてのキーと選択したプロパティを表形式で一覧表示します。

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧表示します。

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

特定のキーを削除する方法の例です。

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

特定のシークレットを削除する方法の例です。

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>次のステップ
- プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。
- Azure Key Vault の概要については、「[Azure Key Vault とは](key-vault-whatis.md)」を参照してください。
- PowerShell を使用した Azure Key Vault の操作方法については、「[Azure Key Vault の概要](key-vault-get-started.md)」を参照してください。


