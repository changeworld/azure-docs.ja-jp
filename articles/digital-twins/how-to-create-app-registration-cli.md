---
title: アプリの登録を作成する (CLI)
titleSuffix: Azure Digital Twins
description: CLI を使用して、クライアント アプリ向けの認証オプションとして Azure AD アプリの登録を作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6b769bbc1b3edb9a680fc6e2823a4e5eaeeaa201
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617756"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-cli"></a>Azure Digital Twins で使用するアプリの登録を作成する (CLI)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Azure Digital Twins インスタンスを使用する場合、カスタム クライアント アプリや [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md) のようなサンプルなどのクライアント アプリケーションを使用してそのインスタンスを操作することが一般的です。 これらのアプリケーションを操作するには、Azure Digital Twins で認証する必要があります。また、アプリで使用できる [認証メカニズム](how-to-authenticate-client.md)には、[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) の **アプリの登録** が関与します。

アプリの登録は、認証シナリオによっては必要ありません。 ただし、アプリの登録が必要な認証方法またはコード サンプルを使用している場合は、この記事で [Azure CLI](/cli/azure/what-is-azure-cli) を使用した設定方法を確認できます。 また、アプリの登録を使用して認証するために必要な[重要な値を収集](#collect-important-values)する方法についても説明しています。

## <a name="azure-ad-app-registrations"></a>Azure AD アプリの登録

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 Azure AD で **アプリの登録** を設定することは、クライアント アプリに Azure Digital Twins へのアクセスを許可する方法の 1 つです。

このアプリの登録では、[Azure Digital Twins API](concepts-apis-sdks.md) に対するアクセス許可を構成します。 後で、クライアント アプリは登録の **クライアントおよびテナント ID 値** を使用してこのアプリ登録に対して認証できます。結果として API への構成済みのアクセス許可が付与されます。

>[!TIP]
> 新しいアプリ登録を必要となるたびに設定することもできます。*または*、1 回だけ行い、1 つのアプリ登録を確立してそれを必要とするすべてのシナリオで共有することもできます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-manifest"></a>マニフェストを作成する

最初に、アプリの登録で Azure Digital Twins API にアクセスするために必要な特定のサービス情報を含むファイルを作成します。 後で、アプリの登録を作成するときに、このファイルを渡して Azure Digital Twins のアクセス許可を設定します。

コンピューターに **manifest.json** という名前の新しい .json ファイルを作成します。 次のテキストをファイルにコピーします。

```json
[
    {
        "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
        "resourceAccess": [
            {
                "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
                "type": "Scope"
            }
        ]
    }
]
```

静的な値 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` は、Azure Digital Twins サービス エンドポイントのリソース ID です。この ID は、アプリの登録で Azure Digital Twins API にアクセスするために必要となります。
 
完成したファイルを保存します。

### <a name="upload-to-cloud-shell"></a>Upload to Cloud Shell (Azure: Cloud Shell へのアップロード)

次に、作成したマニフェスト ファイルを Cloud Shell にアップロードして、アプリの登録を構成するときに Cloud Shell コマンドでアクセスできるようにします。

ファイルをアップロードするには、ブラウザーで Cloud Shell ウィンドウに移動します。 [ファイルのアップロード/ダウンロード] アイコンを選択し、[アップロード] を選択します。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="[アップロード] アイコンが強調表示されている [Azure Cloud Shell] のスクリーンショット。":::

マシン上の **manifest.json** ファイルに移動し、[開く] を選択します。 こうすることで、Cloud Shell ストレージのルートにファイルがアップロードされます。

## <a name="create-the-registration"></a>登録を作成する

このセクションでは、Cloud Shell コマンドを実行して、次の設定でアプリの登録を作成します。
* 任意の名前
* 既定のディレクトリ内のアカウントでのみ使用できる (シングル テナント)
* `http://localhost` の Web 応答 URL
* Azure Digital Twins API に対する読み取り/書き込みアクセス許可

次のコマンドを実行して登録を作成します。

```azurecli-interactive
az ad app create --display-name <app-registration-name> --available-to-other-tenants false --reply-urls http://localhost --native-app --required-resource-accesses "@manifest.json"
```

コマンドの出力は、作成したアプリの登録に関する情報です。 

## <a name="verify-success"></a>成功を確認する

Azure Digital Twins のアクセス許可が付与されたことを確認するには、`az ad app create` コマンドの出力で次のフィールドを探して、それらの値が次のスクリーンショットに示されている値と一致することを確認します。

:::image type="content" source="media/how-to-create-app-registration/cli-required-resource-access.png" alt-text="アプリの登録作成コマンドの Cloud Shell 出力を示すスクリーンショット。'requiredResourceAccess' の下の項目が強調表示されています。'resourceAppId' 値は 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 であり、'resourceAccess > id' の値は 4589bd03-58cb-4e6c-b17f-b580e39652f8 です。":::

Azure portal を使用して、アプリの登録が正常に作成されたことを確認することもできます。 ポータルでの手順については、[成功の確認 (ポータル)](how-to-create-app-registration-portal.md#verify-success) に関するページを参照してください。

## <a name="collect-important-values"></a>重要な値を収集する

次に、アプリの登録に関する重要な値を収集します。これらは、アプリの登録を使用してクライアント アプリケーションを認証するのに必要となります。 これらの値には次の値が含まれます。
* **リソース名**
* **クライアント ID**
* **テナント ID**
* **クライアント シークレット**

Azure Digital Twins に使用する場合、**リソース名** は `http://digitaltwins.azure.net` になります。

次のセクションでは、他の値を見つける方法について説明します。

### <a name="collect-client-id-and-tenant-id"></a>クライアント ID とテナント ID を収集する

認証にアプリの登録を使用するには、**アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** を指定する必要がある場合があります。 このセクションでは、これらの値を収集します。これにより、これらの値を保存して必要なときにいつでも使用できます。

これらの値は、`az ad app create` コマンドからの出力で確認できます。

アプリケーション (クライアント) ID:

:::image type="content" source="media/how-to-create-app-registration/cli-app-id.png" alt-text="アプリの登録作成コマンドの Cloud Shell 出力を示すスクリーンショット。appId 値が強調表示されています。":::

ディレクトリ (テナント) ID:

:::image type="content" source="media/how-to-create-app-registration/cli-tenant-id.png" alt-text="アプリの登録作成コマンドの Cloud Shell 出力を示すスクリーンショット。odata.metadata の GUID 値が強調表示されています。":::

### <a name="collect-client-secret"></a>クライアント シークレットを収集する

アプリの登録の **クライアント シークレット** を作成するには、前のセクションのアプリの登録のクライアント ID 値が必要です。 新しいシークレットを作成するには、次の CLI コマンドでその値を使用します。

```azurecli-interactive
az ad app credential reset --id <client-ID> --append
```

このコマンドに省略可能なパラメーターを追加して、資格情報の説明、終了日、その他の詳細を指定することもできます。 このコマンドとそのパラメーターの詳細については、[az ad app credential reset のドキュメント](/cli/azure/ad/app/credential?view=azure-cli-latest&preserve-view=true#az_ad_app_credential_reset)を参照してください。

このコマンドによって、作成されたクライアント シークレットに関する情報が出力されます。 認証にクライアント シークレットが必要な場合に使用するために、`password` の値をコピーします。

:::image type="content" source="media/how-to-create-app-registration/cli-client-secret.png" alt-text="アプリの登録作成コマンドの Cloud Shell 出力を示すスクリーンショット。password 値が強調表示されています。":::

>[!IMPORTANT]
>この値を再度取得することはできないため、ここでコピーして安全な場所に保存してください。 後でこの値を見つけることができなかった場合は、新しいシークレットを作成する必要があります。

## <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

アプリの登録を正常に設定するには、サブスクリプションの所有者または管理者からの追加のアクションが組織で必要になる可能性があります。 必要な手順は、組織の具体的な設定によって異なることがあります。

サブスクリプションの所有者または管理者による実行が必要になる可能性がある一般的な潜在的なアクティビティのいくつかを次に示します。
* アプリ登録に対する管理者の同意を付与する。 組織では、サブスクリプション内のすべてのアプリ登録について、Azure AD で **[管理者の同意が必要]** がグローバルに有効になっている可能性があります。 その場合、所有者/管理者は、追加の委任されたアクセス許可またはアプリケーションのアクセス許可を付与する必要がある場合があります。
* 登録の create または update コマンドに `--set publicClient=true` を追加して、パブリック クライアント アクセスをアクティブ化する。
* `--reply-urls` パラメーターを使用して、Web およびデスクトップへのアクセスに特定の応答 URL を設定する。 `az ad` コマンドでこのパラメーターを使用する方法の詳細については、[az ad app のドキュメント](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)を参照してください。
* `--oauth2-allow-implicit-flow` パラメーターを使用して、暗黙的な OAuth2 認証フローを許可する。 `az ad` コマンドでこのパラメーターを使用する方法の詳細については、[az ad app のドキュメント](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true)を参照してください。

アプリ登録とそのさまざまな設定オプションの詳細については、「[Microsoft ID プラットフォームにアプリケーションを登録する](/graph/auth-register-app-v2)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、クライアント アプリケーションを Azure Digital Twins API で認証するために使用できる Azure AD アプリの登録を設定しました。

次に、認証メカニズムについて、アプリの登録を使用するものと、使用しないものについて確認します。
* [アプリ認証コードを作成する](how-to-authenticate-client.md)