---
title: チュートリアル - Azure Red Hat OpenShift クラスターを作成する |Microsoft Docs
description: Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成する方法を学習します
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079477"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>チュートリアル:Azure Red Hat OpenShift クラスターを作成する

このチュートリアルは、シリーズの第 1 部です。 Azure CLI を使用して Microsoft Azure Red Hat OpenShift クラスターを作成し、スケーリングしてからこれを削除してリソースをクリーンアップする方法について説明します。

シリーズのパート 1 で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する
> * [Azure Red Hat OpenShift クラスターをスケーリングする](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift クラスターを削除する](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

次を含む[開発環境を設定](howto-setup-environment.md)していることを確認してください。
- 最新の CLI のインストール
- テナントの作成
- Azure アプリケーション オブジェクトの作成
- クラスターで実行されているアプリにサインインするために使用する Active Directory ユーザーの作成

## <a name="step-1-sign-in-to-azure"></a>手順 1:Azure へのサインイン

Azure CLI をローカルで実行している場合は、Bash コマンド シェルを開き、`az login` を実行して Azure にサインインします。

```bash
az login
```

 複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>手順 2:Azure Red Hat OpenShift クラスターを作成する

Bash コマンド ウィンドウで、次の変数を設定します。

> [!IMPORTANT]
> クラスターの名前はすべて小文字にする必要があります。そうしないと、クラスターの作成は失敗します。

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 「[Create a new app registration](howto-aad-app-configuration.md#create-a-new-app-registration)」(新しいアプリ登録の作成) の手順 6 で選択したのと同じクラスター名を使用します。

```bash
LOCATION=<location>
```

クラスターを作成する場所を選択します。 Azure 上の OpenShift がサポートされる Azure リージョンの一覧については、[サポートされるリージョン](supported-resources.md#azure-regions)を参照してください。 (例: `LOCATION=eastus`)。

`FQDN` をクラスターの完全修飾名に設定します。 この名前は、クラスター名、場所、末尾に付加された `.cloudapp.azure.com` で構成されます。 これは、「[Create a new app registration](howto-aad-app-configuration.md#create-a-new-app-registration)」(新しいアプリ登録の作成) の手順 6 で作成したサインオン URL と同じです。 例:   

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

`APPID` を、「[Create a new app registration](howto-aad-app-configuration.md#create-a-new-app-registration)」(新しいアプリ登録の作成) の手順 9 で保存した値に設定します。  

```bash
APPID=<app ID value>
```

`SECRET` を、「[Create a client secret](howto-aad-app-configuration.md#create-a-client-secret)」(クライアント シークレットの作成) の手順 6 で保存した値に設定します。  

```bash
SECRET=<secret value>
```

`TENANT` を、「[Create a new tenant](howto-create-tenant.md#create-a-new-azure-ad-tenant)」(新しいテナントの作成) の手順 7 で保存したテナント ID 値に設定します。  

```bash
TENANT=<tenant ID>
```

クラスターのリソース グループを作成します。 上記の変数を定義するのに使用した Bash シェルの次のコマンドを実行します。

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>省略可能:クラスターの仮想ネットワークを既存の仮想ネットワークに接続する

作成したクラスターの仮想ネットワーク (VNET) を既存の VNET に接続する必要がない場合は、この手順をスキップします。

最初に、既存の VNET の識別子を取得します。 この識別子は、`/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}` の形式になります。

既存の VNET が所属するネットワーク名またはリソース グループがわからない場合は、[[仮想ネットワーク] ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks)に移動し、仮想ネットワークをクリックします。 仮想ネットワーク ページが表示され、これが属するネットワークおよびリソース グループの名前が一覧表示されます。

BASH シェルで次の CLI コマンドを使用して、VNET_ID 変数を定義します。

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

次に例を示します。`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>クラスターを作成する

これで、クラスターを作成する準備ができました。

 クラスターの仮想ネットワークを既存の仮想ネットワークに接続していない場合は、次の例の末尾の `--vnet-peer-id $VNET_ID` パラメーターを省略します。

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

数分後に `az openshift create` は正常に完了し、クラスターの詳細を含む JSON 応答を返します。

> [!NOTE]
> ホスト名が使用できないというエラーが発生する場合は、クラスター名が一意でない可能性があります。 元のアプリ登録を削除し、別のクラスター名を使用して「Create a new app registration」(新しいアプリ登録の作成) (howto-aad-app-configuration.md#create-a-new-app-registration) の手順をもう一度行ってみてください (新しいユーザーは既に作成済みなので、その最後の作成手順を省略します)。

## <a name="step-3-sign-in-to-the-openshift-console"></a>手順 3:OpenShift コンソールにサインインする

これで、新しいクラスターの OpenShift コンソールにサインインする準備ができました。 [OpenShift Web コンソール](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html)により、OpenShift プロジェクトのコンテンツの視覚化、参照、管理ができます。

テスト用に作成した[新しい Azure AD ユーザー](howto-aad-app-configuration.md#create-a-new-active-directory-user)としてサインインします。 そのためには、Azure portal にサインインするために、通常使用する ID がキャッシュされていない、新しいブラウザー インスタンスが必要です。

1. *シークレット* ウィンドウ (Chrome) または *InPrivate* ウィンドウ (Microsoft Edge) を開きます。
2. 「[Create a new app registration](howto-aad-app-configuration.md#create-a-new-app-registration)」(新しいアプリ登録の作成) の手順 6 で作成したサインオン URL に移動します。 たとえば、 https://constoso.eastus.cloudapp.azure.com のように指定します。

> [!NOTE]
> OpenShift コンソールでは、自己署名証明書が使用されます。
> お使いのブラウザーでメッセージが表示されたら、警告を回避し、"信頼されていない" 証明書をそのまま使用します。

「[Create a new Active Directory user](howto-aad-app-configuration.md#create-a-new-active-directory-user)」(新しい Active Directory ユーザーの作成) で作成したユーザーとパスワードを使用してサインインします。**[要求されているアクセス許可]** ダイアログが表示されたら、**[組織の代理として同意する]** を選択し、**[承諾]** を選択します。

これで、クラスター コンソールにログインしました。

[OpenShift クラスター コンソールのスクリーンショット](./media/aro-console.png)

 [OpenShift コンソールを使用](https://docs.openshift.com/dedicated/getting_started/developers_console.html)してイメージを作成およびビルドする方法の詳細については、[Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html) ドキュメントを参照してください。

## <a name="step-4-install-the-openshift-cli"></a>手順 4:OpenShift CLI をインストールする

[OpenShift CLI](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (*OC ツール*) では、OpenShift クラスターのさまざまなコンポーネントと対話するためのアプリケーションおよび低レベルのユーティリティを管理するコマンドを提供します。

OpenShift コンソールで、右上隅のサインイン名の横にある疑問符をクリックし、**[コマンド ライン ツール]** を選択します。  **[最新リリース]** のリンク先に移動し、Linux、MacOS、または Windows 用にサポートされている oc CLI をダウンロードしてインストールします。

> [!NOTE]
> 右上隅の疑問符アイコンが表示されない場合は、左上のドロップダウン リストで *[サービス カタログ]* または *[Application Console]\(アプリケーション コンソール\)* を選択します。
>
> または、直接 [oc CLI をダウンロード](https://www.okd.io/download.html)できます。

**[コマンド ライン ツール]** ページ フォームでは、`oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>` の形式のコマンドが提供されます。  *[クリップボードにコピー]* ボタンをクリックして、このコマンドをコピーします。  ターミナル ウィンドウで、oc ツールのローカル インストールを含めるように[パスを設定します](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli)。 その後、コピーした oc CLI コマンドを使用してクラスターにサインインします。

上記の手順を使用してトークン値を取得できなかった場合は、`https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request` からトークン値を取得します。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを作成する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターをスケーリングする](tutorial-scale-cluster.md)