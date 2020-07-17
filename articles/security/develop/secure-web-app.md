---
title: セキュリティで保護された Web アプリケーションを開発する | Microsoft Docs
description: この簡単なサンプル アプリでは、セキュリティのベスト プラクティスを実装して、Azure で開発を行う際のアプリケーションと組織のセキュリティ体制を向上させます。
keywords: NA
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.custom: has-adal-ref
ms.openlocfilehash: 690cb37df4a5d195bfce6ee792f7565a6f7f1768
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612777"
---
# <a name="develop-a-secure-web-app"></a>セキュリティで保護された Web アプリを開発する

このサンプルは、Azure でアプリを開発するためのセキュリティ リソースへのリンクが含まれた Web ページを表示する単純な Python アプリです。 このアプリでは、Azure でアプリを開発するときに、アプリケーションと組織のセキュリティ体制を向上させるうえで役立つセキュリティのベスト プラクティスを実装します。

この記事で説明されている手順に順次従って、アプリケーション コンポーネントが適切に構成されていることを確認してください。 データベース、Azure App Service、Azure Key Vault インスタンス、および Azure Application Gateway インスタンスは互いに依存しています。

デプロイ スクリプトによって、インフラストラクチャがセットアップされます。 デプロイ スクリプトを実行した後は、Azure portal でいくつかの手動構成を実行し、コンポーネントとサービスを互いにリンクする必要があります。

このサンプル アプリは、Azure でアプリケーションにセキュリティ対策を実装する必要がある、アプリケーション開発のビギナーを対象としています。

このアプリを開発してデプロイする中で、次の方法を学習します。

- Azure Key Vault インスタンスを作成して保存し、そこからシークレットを取得する。
- Azure Database for PostgreSQL をデプロイし、セキュリティで保護されたパスワードを設定して、そこへのアクセスを承認する。
- Azure Web Apps for Linux で Alpine Linux コンテナーを実行し、Azure リソースのマネージド ID を有効にする。
- [OWASP 上位 10 のルール セット](https://coreruleset.org/)を使用するファイアウォールを使用して、Azure Application Gateway インスタンスを作成して構成する。
- Azure サービスを使用して、転送中および保存時のデータの暗号化を有効にする。

このアプリを開発してデプロイすると、次のサンプル Web アプリと、説明されている構成およびセキュリティ対策が設定されます。

![サンプル Web アプリ](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architecture

このアプリは、3 つの階層を持つ典型的な n 層アプリケーションです。 次に示すように、フロントエンド、バックエンド、データベースの各レイヤーに、監視コンポーネントとシークレット管理コンポーネントが統合されています。

![アプリのアーキテクチャ](./media/secure-web-app/architecture.png)

このアーキテクチャは、次のコンポーネントで構成されています。

- [Azure Application Gateway](../../application-gateway/index.yml)。 アプリケーション アーキテクチャにゲートウェイとファイアウォールを提供します。
- [Azure Web Apps on Linux](../../app-service/containers/app-service-linux-intro.md)。 Linux 環境で Python アプリを実行するコンテナー ランタイムを提供します。
- [Azure Key Vault](../../key-vault/index.yml)。 アプリのシークレットを格納して暗号化し、それらに関連するアクセス ポリシーの作成を管理します。
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/)。 アプリのデータを安全に格納します。
- [Azure Security Center](../../security-center/index.yml) と [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)。 アプリの操作に関する監視とアラートを提供します。

## <a name="threat-model"></a>脅威モデル

脅威モデリングは、ビジネスとアプリケーションに対する潜在的なセキュリティ上の脅威を特定して、適切な軽減計画を確保するプロセスです。

このサンプルでは、[Microsoft Threat Modeling Tool](threat-modeling-tool.md) を使用して、セキュリティで保護されたサンプル アプリに対する脅威モデリングを実装しています。 コンポーネントとデータ フローを図式化することで、開発プロセスの早い段階で問題と脅威を特定できます。 これは後々、時間やコストに節約にもつながります。

次に示すのは、サンプル アプリの脅威モデルです。

![脅威モデル](./media/secure-web-app/threat-model.png)

Threat Modeling Tool によって生成される脅威と潜在的脆弱性の例を、次のスクリーンショットに示します。 脅威モデルでは、公開される攻撃対象領域の概要が示され、問題を軽減する方法を検討するよう開発者に促します。

![脅威モデルの出力](./media/secure-web-app/threat-model-output.png)

たとえば、上の脅威モデルの出力に示されている SQL インジェクションは、ユーザー入力をサニタイズしたり、Azure Database for PostgreSQL でストアド関数を使用したりすることによって軽減できます。 この軽減策により、データの読み書き中に任意にクエリを実行することはできなくなります。

開発者は、脅威モデルの出力に示されている各脅威を軽減することによって、システムの全体的なセキュリティを向上させることができます。

## <a name="deployment"></a>デプロイ

次のオプションを使用すると、Azure App Service で Linux を実行できます。

- サポート技術 (Python、Ruby、PHP、Java、node.js、.NET Core) を使用して作成された、Azure 上の事前構築済み Microsoft コンテナーの一覧からコンテナーを選択します。
- カスタムビルド コンテナーを使用します。 イメージのソースとして独自のコンテナー レジストリを選択し、HTTP をサポートする数多くの利用可能なテクノロジを基盤にします。

この例ではデプロイ スクリプトを実行して、Web アプリを App Service にデプロイし、リソースを作成します。

このアプリでは、次に示すさまざまなデプロイ モデルを使用できます。

![デプロイのデータ フロー図](./media/secure-web-app/deployment.png)

Azure にアプリをデプロイする方法は、次のように多数あります。

- Azure Resource Manager のテンプレート
- PowerShell
- Azure CLI
- Azure portal
- Azure DevOps

このアプリケーションでは、以下を使用します。

- コンテナー イメージの作成とビルドには、[Docker](https://docs.docker.com/) を使用します。
- デプロイには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用します。
- コンテナー レジストリとして、[Docker Hub](https://hub.docker.com/) を使用します。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

### <a name="network"></a>ネットワーク

このサンプル アプリでは、ネットワークとの間でやり取りされる転送中のデータに、エンド ツー エンドの TLS/SSL 暗号化を使用します。 ゲートウェイは自己署名証明書を使用して構成されます。
> [!IMPORTANT]
> このデモでは、自己署名証明書を使用します。 運用環境では、検証済みの証明機関 (CA) から証明書を取得する必要があります。

アプリケーション ファイアウォールでも受信トラフィックを検査し、ネットワーク トラフィックで悪意のあるトラフィックが検出された場合、管理者にアラートが送信されます。
アプリケーション ゲートウェイでは、脅威モデルで検出された DDoS と SQL インジェクションの脅威が発生する可能性が軽減されます。

### <a name="identity"></a>ID

このサンプル アプリでは、リソースへのアクセス権が割り当てられている Azure Active Directory (Azure AD) 管理者がポータルにサインインする際に Multi-factor Authentication が使用されます。
サンプル アプリでは、マネージド ID を使用して、Azure Key Vault からシークレットの読み取りと取得を行うためのアクセス許可を取得します。これにより、アプリでは、シークレットを読み取るための資格情報とトークンをハード コーディングする必要がなくなります。 Azure AD では、マネージド ID の使用時に、アプリでシークレットを読み取ったり変更したりするために必要なサービス プリンシパルが自動的に作成されます。

Azure リソースと MFA にマネージド ID を使用すると、敵対者が特権を取得し、システム内で特権を昇格させることが難しくなります。 これは、脅威モデルで指摘されていた脅威です。
アプリでは OAuth を使用します。これにより、OAuth アプリケーションに登録されているユーザーがアプリにサインインできるようになります。

### <a name="storage"></a>ストレージ

PostgreSQL データベースのデータは、保存時に Azure Database for PostgreSQL によって自動的に暗号化されます。 データベースでは App Service IP アドレスを承認することによって、デプロイされている App Service Web アプリだけが適切な認証資格情報を使用してデータベース リソースにアクセスできるようにします。

### <a name="logging-and-auditing"></a>ログ記録と監査

アプリには Application Insights を使用したログ記録が実装されており、発生したメトリック、ログ、例外を追跡できます。 このログ記録によって、開発者や運用チームのメンバーにアプリの状態を知らせるための十分なアプリ メタデータが提供されます。 また、セキュリティ インシデントが発生した場合にバックトラックできるだけの十分なデータも提供されます。

## <a name="cost-considerations"></a>コストに関する考慮事項

まだ Azure アカウントを持っていない場合は、無料で作成できます。 開始するには、[無料アカウントのページ](https://azure.microsoft.com/free/)に移動し、無料の Azure アカウントでできることと、12 か月間無料で使用できる製品をご確認ください。

セキュリティ機能を備えたサンプル アプリのリソースをデプロイするには、いくつかの Premium 機能に対して料金を支払う必要があります。 アプリケーションの規模が拡大し、Azure によって提供される無料レベルと試用版をアップグレードしてアプリケーションの要件を満たす必要が生じるにつれ、コストが増加する可能性があります。 コストの見積もりには、Azure [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)をご利用ください。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

### <a name="prerequisites"></a>前提条件

アプリケーションを起動して稼働させるには、次のツールをインストールする必要があります。

- アプリケーション コードを表示および変更するためのコード エディター。[Visual Studio Code](https://code.visualstudio.com/) はオープンソース オプションです。
- 開発コンピューター上の [Azure CLI](/cli/azure/install-azure-cli)。
- システム上の [Git](https://git-scm.com/)。 Git を使用してソース コードをローカルに複製します。
- [jq](https://stedolan.github.io/jq/)。わかりやすい方法で JSON にクエリを実行するための UNIX ツールです。

サンプル アプリのリソースをデプロイするには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウントを作成して](https://azure.microsoft.com/free/)、サンプル アプリをテストできます。

これらのツールをインストールすると、アプリを Azure にデプロイする準備が整います。

### <a name="environment-setup"></a>環境のセットアップ

デプロイ スクリプトを実行して、環境とサブスクリプションを設定します。

1. ソース コード リポジトリを複製するには、次の Git コマンドを使用します。

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. ディレクトリに移動するには、次のコマンドを使用します。

   ```shell
   cd tutorial-project/scripts
   ```

3. スクリプト フォルダーには、使用しているプラットフォーム (Windows または Linux) に固有のファイルがあります。 Azure CLI が既にインストールされているため、コマンド プロンプトで次の Azure CLI コマンドを実行して、Azure アカウントにサインインします。

   ```azurecli-interactive
   az login
   ```

ブラウザーが開いたら、ご自身の資格情報を使用してサインインします。 サインインしたら、コマンド プロンプトからリソースのデプロイを開始できます。

デプロイ スクリプト `deploy-powershell.ps1` と `deploy-bash.sh` には、アプリケーション全体をデプロイするコードが含まれています。
ソリューションをデプロイするには、次の手順を実行します。

1. PowerShell を使用している場合は、「`./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME`」と入力して `deploy-powershell.ps1` ファイルを実行します。このとき、リージョンとリソース グループ名を適切な Azure リージョンとリソース グループの名前で置き換えます
2. Linux を使用している場合は、「`/deploy-bash.sh REGION RESOURCE_GROUP_NAME`」と入力して `deploy-bash.sh` ファイルを実行します。このとき、「`chmod +x deploy-bash.sh`」と入力してファイルを実行可能にしなければならない場合があります

次の例では、主要なコンポーネントのスニペットを紹介します。 サンプルを個別にデプロイすることも、デプロイ ファイルを実行することで他のコンポーネントと共にデプロイすることもできます。

### <a name="implementation-guidance"></a>実装ガイダンス

デプロイ スクリプトは、4 つのフェーズに分割できる 1 つのスクリプトです。 各フェーズでは、[アーキテクチャ ダイアグラム](#architecture)に含まれる Azure リソースが 1 つずつデプロイおよび構成されます。

4 つのフェーズは次のとおりです。

- Azure Key Vault をデプロイする。
- Azure Database for PostgreSQL をデプロイする。
- Azure Web Apps on Linux をデプロイする。
- Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイをデプロイする。

各フェーズは、それまでにデプロイしたリソースの構成を使用して、前のフェーズに基づいて構築されます。

実装手順を完了するには、「[前提条件](#prerequisites)」に記載されているツールがインストールされていることを確認します。

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault をデプロイする

このセクションでは、シークレットと証明書の格納に使用する Azure Key Vault インスタンスを作成してデプロイします。

デプロイが完了すると、Azure Key Vault インスタンスが Azure にデプロイされます。

Azure CLI を使用して Azure Key Vault をデプロイするには、次の手順を実行します。

1. Azure Key Vault の変数を宣言します。
2. Azure Key Vault プロバイダーを登録します。
3. インスタンスのリソース グループを作成します。
4. 手順 3. で作成したリソース グループに Azure Key Vault インスタンスを作成します。

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

リソースへのアクセスにキー コンテナーを使用するアプリでは、Azure リソースにマネージド ID を使用することをお勧めします。 キー コンテナーへのアクセス キーをコードや構成に格納しない場合、その分、セキュリティ体制が高まります。

#### <a name="deploy-azure-database-for-postgresql"></a>Azure Database for PostgreSQL をデプロイする

まずデータベース サーバーを作成し、次にスキーマとデータを格納するデータベースを作成することで、Azure Database for PostgreSQL が機能するようになります。

デプロイを完了すると、PostgreSQL サーバーとデータベースが Azure で実行されます。

Azure CLI を使用して Azure Database for PostgreSQL をデプロイするには、次の手順を実行します。

1. Azure CLI を使用してターミナルを開き、Azure サブスクリプションのセットアップを開きます。
2. データベースへのアクセスに使用する、セキュリティで保護されたユーザー名とパスワードの組み合わせを生成します  (これらは、それらを使用するアプリの Azure Key Vault に格納する必要があります)。
3. PostgreSQL サーバー インスタンスを作成します。
4. 手順 3. で作成したサーバー インスタンスにデータベースを作成します。
5. PostgreSQL インスタンスで PostgreSQL スクリプトを実行します。

以下のコードは、上記のキー コンテナーのデプロイ手順で作成した Azure Key Vault に格納されている PGUSERNAME および PGPASSWORD シークレットに依存しています。

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

データベースをデプロイしたら、Azure Key Vault にその資格情報と接続文字列を保存する必要があります。
scripts フォルダーには、実行時にストアド関数を作成する PL/pgSQL コードが含まれた `functions.sql` ファイルがあります。 このファイルを実行すると、SQL インジェクションを制限するために入力がパラメーター化されます。

PostgreSQL は、データベースへの接続に使用される、`psql` と呼ばれるツールにバンドルされています。 `functions.sql` は、ローカル コンピューターから Azure Database for PostgreSQL インスタンスに接続し、そこから実行する必要があります。 psql ツールのインストールは、各オペレーティング システム上の PostgreSQL の既定のインストールに含まれています。
詳細については、[psql のドキュメント](https://www.postgresql.org/docs/9.3/app-psql.html)を参照してください。

Azure Cloud Shell にも `psql` ツールが含まれています。 Cloud Shell アイコンを選択すると、Azure portal から直接 Cloud Shell を使用できます。

PostgreSQL インスタンスへのリモートアクセスを有効にするには、PostgreSQL で IP アドレスを承認する必要があります。
このアクセスを有効にするには、 **[接続のセキュリティ]** タブに移動し、 **[クライアント IP の追加]** を選択して、新しい設定を保存します。

![クライアント IP を承認する](./media/secure-web-app/add-client-ip-postgres.png)

ローカルの psql ツールの代わりに Cloud Shell を使用している場合は、 **[Azure サービスへのアクセスを許可]** を選択し、その値を **[オン]** に変更して Cloud Shell アクセスを許可します。

次に、Azure portal の PostgreSQL インスタンスの **[接続文字列]** タブにある接続文字列を指定して、次の psql コマンドを実行し、インスタンスに接続します。
空の中かっこをデータベースの [接続文字列] ブレードのパラメーターで置き換え、パスワードを Azure Key Vault のパスワードで置き換えます。

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

データベースに接続していることを確認した後、次の PL/pgSQL スクリプトを実行します。 このスクリプトでは、データベースにデータを挿入するために使用されるストアド関数が作成されます。

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

PostgreSQL の TLS および証明機関 (CA) 検証を設定する方法の詳細については、[Azure Database for PostgreSQL での TLS 接続の構成](/azure/postgresql/concepts-ssl-connection-security)に関するページをご覧ください。

ルート証明書はコンテナーに含まれています。 証明書を取得するには、次の手順を実行します。

1. [証明機関](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)から証明書ファイルをダウンロードします。
2. [マシンに OpenSSL をダウンロードしてインストールします](/azure/postgresql/concepts-ssl-connection-security)。
3. 次のコマンドを実行して、証明書ファイルをデコードします。

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

PostgreSQL に TLS のセキュリティを構成する方法の詳細については、[TLS 接続のセキュリティの構成](/azure/postgresql/concepts-ssl-connection-security)に関するページをご覧ください。

#### <a name="deploy-azure-web-apps-on-linux"></a>Azure Web Apps on Linux をデプロイする

Azure では、Python、Ruby、C#、Java など、広く使用されている言語用に一連の事前構築済みコンテナーとイメージが用意されているため、Azure App Service 上に Linux サービスを簡単に構築できます。 Azure ではカスタム コンテナーもサポートしているため、事実上すべてのプログラミング言語を Azure App Service プラットフォームで実行できます。

デプロイされるアプリは、最新の Ubuntu Linux ディストリビューションで実行される単純な Python アプリです。 このアプリは、前のセクションで作成した Azure Key Vault インスタンス (資格情報の管理用) と PostgreSQL インスタンス (データ ストレージ用) に接続します。

アプリのルート フォルダーには、次の Docker ファイルが用意されています。

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

上の Docker ファイルは、`mcr.microsoft.com/samples/basic-linux-app` にある Azure Container Registry でホストされるコンテナーをビルドするために使用されます。

下のコードでは、以下が実行されます。

1. App Service インスタンスの変数と名前を宣言します。
2. App Service プランのリソース グループを作成します。
3. Linux コンテナー インスタンス上で Azure Web Apps をプロビジョニングします。
4. Web アプリ コンテナーのログ記録を有効にします。
5. コンテナーのアプリ設定で一部のアプリ構成を設定します。

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

このスクリプトにより、シークレットをコードまたは構成にハード コーディングすることなく、Azure Key Vault と対話するために MSI で使用できる、App Service インスタンス用の割り当て ID が作成されます。

ポータルの Azure Key Vault インスタンスに移動し、[アクセス ポリシー] タブで割り当て ID を承認します。 **[新しいアクセス ポリシーの追加]** を選択します。 **[プリンシパルの選択]** で、作成した App Service インスタンスの名前に類似のアプリケーション名を検索します。
アプリケーションに関連付けられているサービス プリンシパルが表示されます。 次のスクリーンショットに示すように、それを選択し、アクセス ポリシー ページを保存します。

アプリケーションではキーを取得すればよいだけなので、シークレットのオプションで **[取得]** アクセス許可を選択してアクセスを許可し、付与されている特権を減らします。

![Key Vault アクセス ポリシー](./media/secure-web-app/kv-access-policy.png)

"*キー コンテナーのアクセス ポリシーを作成する*"

アクセス ポリシーを保存し、 **[アクセスポリシー]** タブで新しい変更を保存して、ポリシーを更新します。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイをデプロイする

Web アプリでは、外部のインターネット上にサービスを直接公開することはお勧めしません。
負荷分散とファイアウォール規則を使用すると、セキュリティが強化され、受信トラフィックが制御され、管理が容易になります。

Application Gateway インスタンスをデプロイするには、次の手順を実行します。

1. アプリケーション ゲートウェイを格納するリソース グループを作成します。
2. ゲートウェイに接続する仮想ネットワークをプロビジョニングします。
3. 仮想ネットワーク内にゲートウェイ用のサブネットを作成します。
4. パブリック IP アドレスをプロビジョニングします。
5. アプリケーション ゲートウェイをプロビジョニングします。
6. ゲートウェイで Web アプリケーション ファイアウォールを有効にします。

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

前のスクリプトでは、以下が実行されます。

1. Azure で新しい自己署名証明書を作成します。
2. 自己署名証明書を Base64 でエンコードされたファイルとしてダウンロードします。
3. 自己署名証明書のパスワードを生成します。
4. パスワードを使用して署名された PFX ファイルとして証明書をエクスポートします。
5. 証明書のパスワードを Azure Key Vault に格納します。

このセクションでは、次を実行してアプリケーション ゲートウェイをデプロイします。

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

デプロイが完了すると、Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイが作成されます。

ゲートウェイ インスタンスでは、HTTPS 用にポート 443 が公開されます。 この構成により、アプリには HTTPS 経由でポート 443 からのみアクセスできるようになります。

未使用のポートをブロックし、攻撃対象として公開される領域を制限することは、セキュリティ上のベスト プラクティスです。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service インスタンスにネットワーク セキュリティ グループを追加する

App Service インスタンスは、仮想ネットワークと統合できます。 この統合により、アプリの送受信トラフィックを管理するネットワーク セキュリティ グループ ポリシーを使用して、App Service インスタンスを構成することができます。

1. この機能を有効にするには、Azure App Service インスタンスのブレードの **[設定]** で **[ネットワーク]** を選択します。 右側のウィンドウの **[VNet 統合]** で、 **[構成するにはここをクリック]** を選択します。

   ![新しい仮想ネットワーク統合](./media/secure-web-app/app-vnet-menu.png)

    "*App Service 用の新しい仮想ネットワーク統合*"

1. 次のページで、 **[VNet の追加 (プレビュー)]** を選択します。

1. 次のメニューで、デプロイで作成した、名前が `hello-vnet` で始まる仮想ネットワークを選択します。 新しいワークスペースを作成することも、既存のワークスペースを選択することができます。
   ここでは、新しいサブネットを作成します。 **[アドレス範囲]** を「**10.0.3.0/24**」に設定し、サブネットに「**app-subnet**」という名前を付けます。

   ![App Service 仮想ネットワークの構成](./media/secure-web-app/app-vnet-config.png)

    "*App Service 用の仮想ネットワークの構成*"

仮想ネットワークの統合を有効にしたので、ネットワーク セキュリティ グループをアプリに追加できます。

1. 検索ボックスを使用して、「**ネットワーク セキュリティ グループ**」を検索します。 結果で **[ネットワーク セキュリティ グループ]** を選択します。

    ![ネットワーク セキュリティ グループを検索する](./media/secure-web-app/nsg-search-menu.png)

    "*ネットワーク セキュリティ グループを検索する*"

2. 次のメニューで、 **[追加]** を選択します。 NSG の**名前**と、NSG を配置する**リソース グループ**を入力します。 この NSG は、アプリケーション ゲートウェイのサブネットに適用されます。

    ![NSG を作成する](./media/secure-web-app/nsg-create-new.png)

    "*NSG を作成する*"

3. NSG を作成したら、それを選択します。 そのブレードの **[設定]** で **[受信セキュリティ規則]** を選びます。 次の設定を構成して、ポート 443 経由でアプリケーション ゲートウェイに送信されてくる接続を許可します。

   ![NSG を構成する](./media/secure-web-app/nsg-gateway-config.png)

   "*NSG を構成する*"

4. ゲートウェイ NSG の送信規則では、サービス タグ `AppService` をターゲットとする規則を作成して、App Service インスタンスへの送信接続を許可する規則を追加します。

   ![NSG の送信規則を追加する](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   "*NSG の送信規則を追加する*"

    ゲートウェイから仮想ネットワークに送信規則を送信できるように、別の送信規則を追加します。

   ![別の送信規則を追加する](./media/secure-web-app/nsg-outbound-vnet.png)

    "*別の送信規則を追加する*"

5. NSG のサブネット ブレードで **[関連付け]** を選択し、デプロイで作成した仮想ネットワークを選択して、**gw-subnet** という名前のゲートウェイ サブネットを選択します。 この NSG はサブネットに適用されます。

6. 前の手順と同じように、今回は App Service インスタンス用に別の NSG を作成します。 NSG に名前を付けます。 アプリケーション ゲートウェイ NSG の場合と同じように、ポート 443 の受信規則を追加します。

   このアプリ用ではない App Service Environment インスタンスに App Service インスタンスがデプロイされている場合は、App Service NSG の受信セキュリティ グループでポート 454 と 455 を開いて Azure Service Health プローブを許可する受信規則を追加できます。 その構成は次のとおりです。

   ![Azure Service Health プローブ用の規則を追加する](./media/secure-web-app/nsg-create-healthprobes.png)

    "*Azure Service Health プローブ用の規則を追加する (App Service Environment のみ)* "

7. 送信セキュリティ規則で、App Service インスタンスが PostgreSQL データベースと通信できるようにする新しい送信セキュリティ規則を作成します。 これを次のように構成します。

   ![PostgreSQL の送信接続を許可する規則](./media/secure-web-app/nsg-outbound-postgresql.png)

   "*PostgreSQL の送信接続を許可する規則を追加する*"

攻撃対象領域を制限するには、App Service のネットワーク設定を変更して、アプリケーション ゲートウェイのみがアプリケーションにアクセスできるようにします。
これを行うには、App Service のネットワーク タブに移動して、 **[IP 制限]** タブを選択し、アプリケーション ゲートウェイの IP のみがサービスに直接アクセスできるようにする許可規則を作成します。

ゲートウェイの IP アドレスは、その概要ページから取得できます。 **[IP アドレスまたは CIDR]** タブで、`<GATEWAY_IP_ADDRESS>/32` の形式で IP アドレスを入力します。

![ゲートウェイのみを許可する](./media/secure-web-app/app-allow-gw-only.png)

"*ゲートウェイ IP のみに App Service へのアクセスを許可する*"

#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth を実装する

サンプル Web アプリ ページに配布される Azure ドキュメントは、保護が必要となる可能性のある、アプリのリソースです。 Azure Active Directory (Azure AD) では、さまざまな認証フローを使用して、Web アプリ、デスクトップ アプリ、およびモバイル アプリの認証を実装できます。
このアプリでは **Microsoft アカウントでのログイン**を使用します。これにより、アプリは、シングルテナント Azure AD ユーザーの一覧に追加されているユーザーのプロファイルを読み取ることができます。

Azure portal で、次のように、必要な資格情報が使用されるようにアプリを構成します。

1. **[Azure Active Directory]** を選択するか、検索ボックスを使用してこれを検索します。

2. **[新規登録]** を選択します。

   ![登録を作成する](./media/secure-web-app/ad-auth-create.png)

   "*Azure AD アプリの登録を作成する*"

3. 次のページで、アプリの名前を入力します。 **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
    **[リダイレクト URI]** に、アプリが実行される基本ドメインと、トークン エンド ポイントを含むドメインを入力します。 次に例を示します。*GATEWAY_HASH*.cloudapp.net/token。

   ![Azure AD アプリの登録を構成する](./media/secure-web-app/ad-auth-type.png)

   "*Azure AD アプリの登録を構成する*"

4. 登録済みのアプリとその情報を示す画面が表示されます。 この情報は Azure Key Vault インスタンスに追加する必要があります。
   1. アプリケーション (クライアント) ID をコピーし、`CLIENTID` としてキー コンテナーに保存します。
   2. 前の手順で入力したリダイレクト URI をコピーし、`REDIRECTURI` として保存します。
   3. Azure AD の既定のディレクトリ名 (形式は *name*.microsoftonline.com) をコピーし、`TENANT` としてキー コンテナーに保存します。
   4. 前に作成した Azure AD アプリの **[証明書とシークレット]** タブに移動し、次のスクリーンショットに示すように **[新しいクライアント シークレット]** を選択します。 有効期限を設定してから、生成された値をコピーし、`CLIENTSECRET` としてキー コンテナーに保存します。

      ![Azure AD 承認シークレット](./media/secure-web-app/ad-auth-secrets.png)

      "*Azure AD 承認シークレット*"

   5. 任意のコマンドライン/オンライン ツールを使用して、セキュリティで保護されたランダム シークレット キーを生成します。 これを `FLASKSECRETKEY` としてキー コンテナーに保存します。 アプリケーション フレームワークは、このキーを使用してセッションを作成します。
        シークレット キーを生成する方法については、[Flask セッション](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)に関するページを参照してください。

5. サインインを構成したら、ユーザーを Azure AD リンクに追加して、ユーザーがリソースにサインインできるようにする必要があります。 ユーザーを追加するには、Azure ADの **[ユーザー]** タブに移動し、 **[すべてのユーザー]** を選択して、 **[新しいユーザー]** または **[新しいゲスト ユーザー]** を選択します。 テストのために、ゲスト ユーザーを追加して、ユーザーをディレクトリに招待することができます。 または、アプリが実行されているドメインが検証済みの場合は、新しいユーザーを追加することもできます。 この例では、Azure AD テナントに登録されているユーザーのみをアクセスのために登録できます。 マルチテナント サインイン アクセスの詳細については、ドキュメントを参照してください。

   ![既定のドメインにユーザーを追加する](./media/secure-web-app/ad-auth-add-user.png)

   "*既定の Azure Active Directory ドメインにユーザーを追加する*"

キー コンテナーに Azure AD 構成とシークレットを追加すると、Azure OAuth 認証を使用してユーザーをアプリに認証できるようになります。
アプリ コードでは、これは Azure Active Directory 認証ライブラリ (ADAL) によって処理されます。

シークレットがキー コンテナーに格納され、アプリケーションがシークレットとデータベースにアクセスできるようになると、ブレードから取得できるゲートウェイのアプリケーション URL (https:\//GATEWAY_HASH.cloudapp.net) を介してアプリケーション サービスに到達できます。

Azure AD にサインインしたときに、"User is not registered in the directory you're trying to log into"\(ログインしようとしているディレクトリにユーザーが登録されていません\) というエラーが表示される場合は、ユーザーを追加する必要があります。 ユーザーを追加するには、Azure AD の **[ユーザー]** タブに移動し、ユーザーの詳細を入力して手動でユーザーを追加するか、 **[ゲストを招待する]** ブレードで電子メール アドレスを入力してゲスト ユーザーとして Azure AD にユーザーを招待します。

#### <a name="deploy-application-insights"></a>Application Insights のデプロイ
アプリがデプロイされて動作するようになったので、アプリ内で発生したエラーを、ログ記録とトレース データ コレクションと共に処理する必要があります。
ログ記録とトレース データ コレクションによって、アプリで発生する監査イベントを確認できるビューが提供されます。

Application Insights は、ユーザーまたはシステムによって生成される可能性のあるログを収集するサービスです。

Application Insights インスタンスを作成するには、次の手順を実行します。

1. Azure portal の検索ボックスを使用して「**Application Insights**」を検索します。
2. **[Application Insights]** を選択します。 インスタンスを作成するには、ここに示されている詳細を指定します。

   ![Application Insights インスタンスを作成する](./media/secure-web-app/app-insights-data.png)

デプロイが完了すると、Application Insights インスタンスが作成されます。

Application Insights インスタンスを作成したら、アプリにインストルメンテーション キーを認識させて、アプリがクラウドにログを送信できるようにする必要があります。 これを行うには、Application Insights キーを取得し、Azure が Application Insights に提供するアプリケーション ライブラリ内で使用します。 キーとシークレットは Azure Key Vault に格納し、安全に保管することをお勧めします。

基本的なサンプル アプリでは、Application Insights インスタンスを作成したら、アプリにインストルメンテーション キーを認識させて、アプリがクラウドにログを送信できるようにする必要があります。
キー コンテナーで、`APPINSIGHTSKEY` シークレットを設定し、その値をインストルメンテーション キーとして設定します。 これにより、アプリは Application Insights にログとメトリックを送信できるようになります。

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory に Multi-Factor Authentication を実装する

管理者は、ポータルのサブスクリプション アカウントが保護されていることを確認する必要があります。 作成したリソースはサブスクリプションで管理されているため、サブスクリプションは攻撃に対して脆弱です。 サブスクリプションを保護するには、サブスクリプションの **[Azure Active Directory]** タブで Multi-Factor Authentication を有効にします。

Azure AD は、特定の条件に合致するユーザーまたはユーザー グループに適用されるポリシーに基づいて動作します。
Azure では、管理者がポータルにサインインするために 2 要素認証を必要とすることを指定する既定のポリシーが作成されます。
このポリシーを有効にすると、いったん Azure portal からサインアウトして、再度サインインするように求められる場合があります。

管理者のサインイン用に MFA を有効にするには、次の手順を実行します。

1. Azure portal の **[Azure Active Directory]** タブに移動します
2. [セキュリティ] カテゴリで、[条件付きアクセス] を選択します。 次の画面が表示されます。

   ![条件付きアクセス - ポリシー](./media/secure-web-app/ad-mfa-conditional-add.png)

新しいポリシーを作成できない場合は、次の手順を実行します。

1. **[MFA]** タブに移動します。
2. 無料試用版をサブスクライブするには、Azure AD Premium の**無料試用版**のリンクを選択します。

   ![Azure AD Premium の無料試用版](./media/secure-web-app/ad-trial-premium.png)

[条件付きアクセス] 画面に戻ります。

1. [新しいポリシー] タブを選択します。
2. ポリシー名を入力します。
3. MFA を有効にするユーザーまたはグループを選択します。
4. **[アクセス制御]** で **[許可]** タブを選択し、 **[多要素認証が必要です]** (および必要に応じてその他の設定) を選択します。

   ![Require MFA (MFA が必須)](./media/secure-web-app/ad-mfa-conditional-add.png)

このポリシーを有効にするには、画面上部にあるチェック ボックスをオンにするか、 **[条件付きアクセス]** タブで同じことを実行します。ポリシーが有効になると、ユーザーは、ポータルへのサインインには MFA が必要になります。

すべての Azure 管理者に対して MFA を要求するベースライン ポリシーがあります。 ポータルですぐにこれを有効にすることができます。 このポリシーを有効にすると、現在のセッションが無効になり、再度サインインするように強制される可能性があります。

ベースライン ポリシーが有効になっていない場合は、次の手順を実行します。

1. **[管理者に MFA を要求する]** を選択します。
2. **[ポリシーをすぐに使用する]** を選択します。

   ![[ポリシーをすぐに使用する] を選択する](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel を使用してアプリとリソースを監視する

アプリケーションの規模が拡大するにつれて、リソースから受信したすべてのセキュリティ信号とメトリックを集約し、アクション指向なやり方で役立てることが困難になります。

Azure Sentinel は、データを収集し、考えられる脅威の種類を検出し、セキュリティ インシデントを可視化するように設計されています。
Azure Sentinel では、ユーザーが手動で介入する前に、記述済みのプレイブックを使用してアラートおよびインシデント管理プロセスを開始できます。

このサンプル アプリは、Azure Sentinel の監視対象となる複数のリソースで構成されています。
Azure Sentinel を設定するには、さまざまなリソースから収集されたすべてのデータを格納する Log Analytics ワークスペースをまず作成する必要があります。

このワークスペースを作成するには、次の手順を実行します。

1. Azure portal の検索ボックスで、「**Log Analytics**」を検索します。 **[Log Analytics ワークスペース]** を選択します。

   ![Log Analytics ワークスペースを検索する](./media/secure-web-app/sentinel-log-analytics.png)

    "*Log Analytics ワークスペースを検索する*"

2. 次のページで **[追加]** を選択し、ワークスペースの名前、リソース グループ、および場所を指定します。
   ![Log Analytics ワークスペースの作成](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Log Analytics ワークスペースの作成*

3. 検索ボックスを使用して、「**Azure Sentinel**」を検索します。

   ![Azure Sentinel を検索する](./media/secure-web-app/sentinel-add.png)

    "*Azure Sentinel を検索する*"

4. **[追加]** を選択し、先ほど作成した Log Analytics ワークスペースを選択します。

   ![Log Analytics ワークスペースを追加する](./media/secure-web-app/sentinel-workspace-add.png)

    "*Log Analytics ワークスペースを追加する*"

5. **[Azure Sentinel - データ コネクタ]** ページの **[構成]** で、 **[データ コネクタ]** を選択します。 Azure Sentinel での分析対象として Log Analytics ストレージ インスタンスにリンクできる Azure サービスの配列が表示されます。

   ![Log Analytics データ コネクタ](./media/secure-web-app/sentinel-connectors.png)

    "*Azure Sentinel にデータ コネクタを追加する*"

   たとえば、アプリケーション ゲートウェイを接続するには、次の手順を実行します。

   1. Azure Application Gateway インスタンスのブレードを開きます。
   2. **[監視]** で **[診断設定]** を選択します。
   3. **[診断設定の追加]** を選択します。

      ![アプリケーション ゲートウェイの診断を追加する](./media/secure-web-app/sentinel-gateway-connector.png)

      "*アプリケーション ゲートウェイの診断を追加する*"

   4. **[診断設定]** ページで、作成した Log Analytics ワークスペースを選択し、収集して Azure Sentinel に送信するすべてのメトリックを選択します。 **[保存]** を選択します。

        ![Azure Sentinel のコネクタ設定](./media/secure-web-app/sentinel-connector-settings.png)

        "*Azure Sentinel のコネクタ設定*"

  リソースのメトリックは Azure Sentinel に格納されるので、ここにクエリを実行して調査することができます。

   Azure Key Vault、パブリック IP アドレス、Azure Database for PostgreSQL、および使用しているアカウントで診断ログをサポートする任意のサービスについて、診断設定に同じメトリックを追加します。

メトリックを設定すると、分析するデータを Azure Sentinel で使用できるようになります。

## <a name="evaluate-and-verify"></a>評価と検証

アーキテクチャを開発してデプロイした後、コードとデプロイしたサービスがセキュリティ標準を満たしていることを確認する必要があります。 ソフトウェアを検証するために実行できる手順を次に示します。

- スタティック コード分析
- 脆弱性のスキャン
- アプリケーションの依存関係における脆弱性の検出と修正

これらは、セキュリティで保護された開発におけるベスト プラクティスの基本的な構成要素です。

### <a name="static-code-analysis"></a>スタティック コード分析

サンプル アプリでは、スタティック分析ツールによる検証において、テイント チェックやデータフロー分析などの手法を使用して、アプリ コードの脆弱性を検出する必要があります。 Python スタティック分析ツールを使用すると、アプリのセキュリティを向上できます。

**リンティング**

Python のリンティング ライブラリである PyFlakes は、次に示すように、実行されないコードや未使用の関数をアプリから削除する際に役立ちます。

![PyFlakes](./media/secure-web-app/pyflakes.png)

リンティングでは、コードをすっきりさせたり、実行時にエラーの発生を抑えたりできるヒントや変更が提供されます。

**PyLint**

このプロジェクトでは、PyLint が最も有用でした。 コード標準のチェック、エラー チェック、リファクタリングのヒントを実行して、サーバーで実行されているコードが安全であることを確認します。 PyLint を使用してコードを更新することで、次の図に示すように、バグを排除し、PyLint 評価を向上させることができます。

![PyLint の実行前](./media/secure-web-app/before-pylint.png)

"*PyLint の実行前*"

これらのリンティング ツールによって検出されたコード エラーの一部を修正すると、コードでのエラーが発生しにくくなっているという確信が得られます。 エラーを修正すると、コードが運用環境にデプロイされたときに発生する可能性があるセキュリティ上のリスクが大幅に軽減されます。

![PyLint の実行後](./media/secure-web-app/after-pylint.png)

"*PyLint の実行後*"

### <a name="vulnerability-scanning"></a>脆弱性のスキャン

[OWASP の ZAP](https://www.zaproxy.org/) ツールは、サンプル アプリの脆弱性を確認するために使用できる、オープンソースの Web アプリケーション脆弱性スキャナーです。 このツールをサンプル アプリで実行すると、考えられるエラーと攻撃ベクトルが明らかになります。

![ZAP ツール](./media/secure-web-app/zap-tool.png)

"*ZAP ツール*"

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>アプリの依存関係の脆弱性を発見して修正する

アプリケーションの依存関係を検索して修正するには、[OWASP のDependency Check](https://owasp.org/www-project-dependency-check/) を使用します。

Safety も、依存関係を確認する類似のアプリケーションです。 これは [GitHub](https://github.com/pyupio/safety) にあります。 Safety では、既知の脆弱性データベースに登録されている脆弱性が検出されます。

![Safety](./media/secure-web-app/pysafety.png)

"*Safety*"

## <a name="next-steps"></a>次のステップ

次の記事は、セキュリティで保護されたアプリケーションを設計、開発、およびデプロイする際に役立ちます。

- [Design (デザイン)](secure-design.md)
- [開発](secure-develop.md)
- [デプロイする](secure-deploy.md)
