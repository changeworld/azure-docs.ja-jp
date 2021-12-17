---
title: Secure Network Communications (SNC) を使用して Microsoft Sentinel SAP データ コネクタをデプロイする | Microsoft Docs
description: NetWeaver/ABAP インターフェイス ベースのログのために、SNC を介してセキュリティで保護された接続を使用して、SAP 環境用の Microsoft Sentinel データ コネクタをデプロイする方法について説明します。
author: batamig
ms.author: bagol
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 2437859c3ee236e1dc3c641f22de5e1d3ce07fff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711889"
---
# <a name="deploy-the-microsoft-sentinel-sap-data-connector-with-snc"></a>SNC を使用して Microsoft Sentinel SAP データ コネクタをデプロイする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、NetWeaver/ABAP インターフェイス ベースのログのために、Secure Network Communications (SNC) を介して SAP へのセキュリティで保護された接続がある場合に、Microsoft Sentinel SAP データ コネクタをデプロイする方法について説明します。

> [!NOTE]
> Microsoft Sentinel SAP データ コネクタをデプロイするための、既定かつ最も推奨されるプロセスは、[Azure VM を使用する](sap-deploy-solution.md)ことです。 この記事は、上級ユーザーを対象にしています。

> [!IMPORTANT]
> Microsoft Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。

## <a name="prerequisites"></a>前提条件

Microsoft Sentinel SAP データ コネクタをデプロイするための基本的な前提条件は、デプロイ方法に関係なく同じです。

始める前に、メインの [SAP データ コネクタ展開手順](sap-deploy-solution.md#prerequisites)に記載されている前提条件にシステムが準拠していることを確認してください。

SNC を使用するためのその他の前提条件は次のとおりです。

- **SNC による SAP へのセキュリティ保護された接続**。 接続先の AS ABAP システムのリポジトリ定数で、接続固有の SNC パラメーターを定義します。 詳細については、関連する [SAP コミュニティの Wiki ページ](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC)を参照してください。

- **SAPCAR ユーティリティ**。SAP Service Marketplace からダウンロードします。 詳細については、[SAP インストール ガイド](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html)のページを参照してください

詳細については、「[Microsoft Azure Sentinel SAP ソリューションの詳細な SAP 要件 (パブリック プレビュー)](sap-solution-detailed-requirements.md)」を参照してください。

## <a name="create-your-azure-key-vault"></a>Azure キー コンテナーを作成する

Microsoft Sentinel SAP データ コネクタ専用の Azure キー コンテナーを作成します。

次のコマンドを実行して、Azure キー コンテナーを作成し、Azure サービス プリンシパルにアクセスを許可します。

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file

az ad sp create-for-rbac –name $spname

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp

# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

詳細については、「[クイック スタート: Azure CLI を使用してキー コンテナーを作成する](../key-vault/general/quick-create-cli.md)」を参照してください。

## <a name="add-azure-key-vault-secrets"></a>Azure Key Vault シークレットを追加する

Azure Key Vault シークレットを追加するには、自身のシステム ID と追加したい資格情報を使用して、次のスクリプトを実行します。

```azurecli
#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

詳細については、[az keyvault secret](/cli/azure/keyvault/secret) に関する CLI のドキュメントを参照してください。

## <a name="deploy-the-sap-data-connector"></a>SAP データ コネクタを展開する

この手順では、SNC 経由で接続するときに、VM に SAP データ コネクタを展開する方法について説明します。

[SAP 資格情報](#add-azure-key-vault-secrets)を使用して[キー コンテナー](#create-your-azure-key-vault)を準備した後、この手順を実行することをお勧めします。

**SAP データ コネクタを展開するには**:

1. データ コネクタの VM で、[[SAP Launchpad サイト]](https://support.sap.com) >  **[SAP NW RFC SDK]**  >  **[SAP NW RFC SDK 7.50]**  >  **[nwrfc750X_X-xxxxxxx.zip]** から最新の SAP NW RFC SDK をダウンロードします。

    > [!NOTE]
    > SDK にアクセスするには、SAP ユーザーのサインイン情報が必要です。また、ご使用のオペレーティング システムに対応する SDK をダウンロードする必要があります。
    >
    > 必ず **LINUX ON X86_64** オプションを選択してください。

1. わかりやすい名前で新しいフォルダーを作成し、SDK の ZIP ファイルを新しいフォルダーにコピーします。

1. データ コネクタ VM に Microsoft Sentinel ソリューションの GitHub リポジトリをクローンし、Microsoft Sentinel SAP ソリューションの **systemconfig.ini** ファイルをその新しいフォルダーにコピーします。

    次に例を示します。

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 埋め込みコメントを参考にして、必要に応じて **systemconfig.ini** ファイルを編集します。

    キー コンテナーのシークレット以外のすべての構成を編集する必要があります。 詳細については、「[SAP データ コネクタを手動で構成する](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector)」を参照してください。

1. **systemconfig.ini** ファイルの指示を使用して、Microsoft Sentinel に取り込むログを定義します。

    例については、「[Microsoft Sentinel に送信される SAP ログを定義する](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)」を参照してください。

    > [!NOTE]
    > SNC 通信の関連ログは、NetWeaver/ABAP インターフェイス経由で取得されるログだけです。 SAP コントロールと HANA のログは、SNC の対象外です。
    >

1. **systemconfig.ini** ファイルの指示に従って、次の構成を定義します。

    - 監査ログにユーザーのメール アドレスを含めるかどうか
    - 失敗した API 呼び出しを再試行するかどうか
    - cexal 監査ログを含めるかどうか
    - 特に大規模なデータ抽出の際に、データ抽出の間に一定時間待機するかどうか

    詳細については、[SAL ログ コネクタの構成](sap-solution-deploy-alternate.md#sal-logs-connector-settings)に関するセクションを参照してください。

1. 更新した **systemconfig.ini** ファイルを、VM の **sapcon** ディレクトリに保存します。

1. SAP データ コネクタがインストールされた、定義済みの Docker イメージをダウンロードして実行します。 次を実行します。

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```

## <a name="post-deployment-sap-system-procedures"></a>展開後の SAP システムでの手順

SAP データ コネクタを展開した後、SAP システムで次の手順を実行します。

1. [[SAP Service Marketplace]](https://launchpad.support.sap.com/#/) >  **[Software Downloads]\(ソフトウェアのダウンロード\)**  >  **[Browse our Download Catalog]\(ダウンロード カタログの参照\)**  >  **[SAP Cryptographic Software]\(SAP 暗号化ソフトウェア\)** から SAP Cryptographic Library をダウンロードします。

    詳細については、[SAP インストール ガイド](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html)のページを参照してください。

1. SAPCAR ユーティリティを使用してライブラリ ファイルを抽出し、SAP データ コネクタ VM の `<sec>` ディレクトリにそれらを展開します。

1. ライブラリ ファイルを実行するアクセス許可があることを確認します。

1. **SECUDIR** という名前の環境変数を定義し、値を `<sec>` ディレクトリへの完全なパスに設定します。

1. パーソナル セキュリティ環境 (PSE) を作成します。 SAP データ コネクタ VM の `<sec>` ディレクトリで、**sapgenspe** コマンドライン ツールを使用できます。

    次に例を示します。

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    詳細については、SAP ドキュメントの「[Creating a Personal Security Environment](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html)」(パーソナル セキュリティ環境の作成) を参照してください。

1. PSE の資格情報を作成します。 次に例を示します。

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    詳細については、SAP ドキュメントの「[Creating Credentials](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html)」(資格情報の作成) を参照してください。

1. ID センターと AS ABAP の SNC PSE との間で、公開キー証明書を交換します。

    たとえば、ID センターの公開キー証明書をエクスポートするには、次のように実行します。

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    証明書を AS ABAP の SNC PSE にインポートし、それを PSE からエクスポートした後、ID センターにインポートし直します。

    たとえば、証明書を ID センターにインポートするには、次のように実行します。

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    詳細については、SAP ドキュメントの「[Exchanging the Public-Key Certificates](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html)」(公開キー証明書の交換) を参照してください。

## <a name="edit-the-sap-data-connector-configuration"></a>SAP データ コネクタの構成を編集する

1. SAP データ コネクタ VM で、**systemconfig.ini** ファイルに移動し、関連する値を使用して次のパラメーターを定義します。

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. 使用している [Azure キー コンテナー](#create-your-azure-key-vault)で、次のシークレットを生成します。

    - `<Interprefix>-ABAPSNCPARTNERNAME`。値は `<Relevant DN details>` です。
    - `<Interprefix>-ABAPSNCLIB`。値は `<lib_Path>` です。
    - `<Interprefix>-ABAPX509CERT`。値は `<Certificate_Code>)` です。

    次に例を示します。

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > 既定では、`<Interprefix>` の値は自分の SID です (例: `A4H-<ABAPSNCPARTNERNAME>`)。
    >

構成ファイルにシークレットを直接入力する場合は、次のようにパラメーターを定義します。

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>SNC パラメーターをユーザーにアタッチする

1. SAP データ コネクタ VM で、`SM30` トランザクションを呼び出して、`USRACLEXT` テーブルを維持することを選択します。

1. 新しいエントリを追加します。 **[ユーザー]** フィールドに、ABAP システムへの接続に使用される通信ユーザーを入力します。

1. プロンプトが表示されたら、SNC 名を入力します。 SNC 名は、Identity Manager PSE の作成時に指定した一意の識別名です。 例: `CN=IDM, OU=SAP, C=DE`

    SNC 名の前に `p` を追加してください。 (例: `p:CN=IDM, OU=SAP, C=DE`)。

1. **[保存]** を選択します。

SNC がデータコネクタ VM で有効になります。

## <a name="activate-the-sap-data-connector"></a>SAP データ コネクタをアクティブにする

この手順では、この記事で前述した手順を使用して作成した、セキュリティ保護された SNC 接続を使用して、SAP データ コネクタをアクティブにする方法について説明します。

1. Docker イメージをアクティブにします。

    ```bash
    docker start sapcon-<SID>
    ```

1. 接続を確認します。 次を実行します。

    ```bash
    docker logs sapcon-<SID>
    ```

1. 接続が失敗する場合は、ログを使用して問題を把握します。

    必要に応じて、Docker イメージを無効にします。

    ```bash
    docker stop sapcon-<SID>
    ```

たとえば、**systemconfig.ini** ファイルやお使いの Azure キー コンテナーの構成に誤りがある場合や、SNC でセキュリティ保護された接続を作成する手順の一部が正常に実行されなかった場合に、問題が発生する可能性があります。

上記の手順をもう一度実行し、SNC でセキュリティ保護された接続を構成してみてください。 詳細については、[Microsoft Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)に関するページも参照してください。

## <a name="next-steps"></a>次のステップ

SAP データ コネクタがアクティブになったら、**Microsoft Sentinel - Continuous Threat Monitoring for SAP** ソリューションをデプロイして続行します。 詳細については、「[SAP セキュリティ コンテンツをデプロイする](sap-deploy-solution.md#deploy-sap-security-content)」を参照してください。

ソリューションをデプロイすると、SAP データ コネクタが Microsoft Sentinel に表示されるようになり、SAP ブックと分析ルールがデプロイされます。 完了したら、SAP ウォッチリストを手動で追加してカスタマイズします。

詳細については、次を参照してください。

- [Microsoft Azure Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [Microsoft Sentinel SAP ソリューションのログ リファレンス](sap-solution-log-reference.md)
- [Microsoft Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス](sap-solution-security-content.md)
