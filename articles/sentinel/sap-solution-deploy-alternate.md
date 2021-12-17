---
title: Microsoft Sentinel SAP データ コネクタ エキスパートの構成オプション、オンプレミスのデプロイ、SAPControl ログ ソース | Microsoft Docs
description: エキスパートの構成オプションとオンプレミスのマシンを使用して、SAP 環境用の Microsoft Sentinel データ コネクタをデプロイする方法について説明します。 SAPControl ログ ソースの詳細も確認してください。
author: batamig
ms.author: bagol
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: a8769eb0d1fdd5cd9e1cf6c7ec3687b3516a210e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711870"
---
# <a name="expert-configuration-options-on-premises-deployment-and-sapcontrol-log-sources"></a>エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel SAP データ コネクタをエキスパートまたはカスタム プロセスにデプロイする方法について説明します。これには、オンプレミスのマシンと Azure Key Vault を使用した資格情報の格納などが含まれます。

> [!NOTE]
> Microsoft Sentinel SAP データ コネクタをデプロイするための、既定かつ最も推奨されるプロセスは、[Azure VM を使用する](sap-deploy-solution.md)ことです。 この記事は、上級ユーザーを対象にしています。

> [!IMPORTANT]
> Microsoft Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

## <a name="prerequisites"></a>前提条件

Microsoft Sentinel SAP データ コネクタをデプロイするための基本的な前提条件は、デプロイ方法に関係なく同じです。

始める前に、メインの [SAP データ コネクタ展開手順](sap-deploy-solution.md#prerequisites)に記載されている前提条件にシステムが準拠していることを確認してください。

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
#Add Abap username
az keyvault secret set \
  --name <SID>-ABAPUSER \
  --value "<abapuser>" \
  --description SECRET_ABAP_USER --vault-name $kvname

#Add Abap Username password
az keyvault secret set \
  --name <SID>-ABAPPASS \
  --value "<abapuserpass>" \
  --description SECRET_ABAP_PASSWORD --vault-name $kvname

#Add java Username
az keyvault secret set \
  --name <SID>-JAVAOSUSER \
  --value "<javauser>" \
  --description SECRET_JAVAOS_USER --vault-name $kvname

#Add java Username password
az keyvault secret set \
  --name <SID>-JAVAOSPASS \
  --value "<javauserpass>" \
  --description SECRET_JAVAOS_PASSWORD --vault-name $kvname

#Add abapos username
az keyvault secret set \
  --name <SID>-ABAPOSUSER \
  --value "<abaposuser>" \
  --description SECRET_ABAPOS_USER --vault-name $kvname

#Add abapos username password
az keyvault secret set \
  --name <SID>-ABAPOSPASS \
  --value "<abaposuserpass>" \
  --description SECRET_ABAPOS_PASSWORD --vault-name $kvname

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

## <a name="perform-an-expert--custom-installation"></a>エキスパートまたはカスタム インストールを実行する

この手順では、オンプレミスでのインストール時など、エキスパートまたはカスタム インストールを使用して SAP データ コネクタを展開する方法について説明します。

SAP 資格情報を使用してキー コンテナーを準備した後に、この手順を実行することをお勧めします。

**SAP データ コネクタを展開するには**:

1. オンプレミスのマシンで、[[SAP Launchpad サイト]](https://support.sap.com) >  **[SAP NW RFC SDK]**  >  **[SAP NW RFC SDK 7.50]**  >  **[nwrfc750X_X-xxxxxxx.zip]** から最新の SAP NW RFC SDK をダウンロードします。

    > [!NOTE]
    > SDK にアクセスするには、SAP ユーザーのサインイン情報が必要です。また、ご使用のオペレーティング システムに対応する SDK をダウンロードする必要があります。
    >
    > 必ず **LINUX ON X86_64** オプションを選択してください。

1. オンプレミスのマシン上にわかりやすい名前の新しいフォルダーを作成し、SDK の zip ファイルを新しいフォルダーにコピーします。

1. オンプレミスのマシンに Microsoft Sentinel ソリューションの GitHub リポジトリをクローンし、Microsoft Sentinel SAP ソリューションの **systemconfig.ini** ファイルをその新しいフォルダーにコピーします。

    次に例を示します。

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini 
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 埋め込みコメントを参考にして、必要に応じて **systemconfig.ini** ファイルを編集します。 詳細については、「[SAP データ コネクタを手動で構成する](#manually-configure-the-sap-data-connector)」を参照してください。

    構成をテストするために、ユーザーとパスワードを **systemconfig.ini** 構成ファイルに直接追加したい場合があるかもしれません。 認証情報の保存には [Azure Key Vault](#add-azure-key-vault-secrets) を使用することをお勧めしますが、**env.list** ファイルや [Docker Secrets](#manually-configure-the-sap-data-connector) を使用することもできますし、**systemconfig.ini** ファイルに直接認証情報を追加することもできます。

1. **systemconfig.ini** ファイルの指示を使用して、Microsoft Sentinel に取り込むログを定義します。 例については、「[Microsoft Sentinel に送信される SAP ログを定義する](#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)」を参照してください。

1. **systemconfig.ini** ファイルの指示に従って、次の構成を定義します。

    - 監査ログにユーザーのメール アドレスを含めるかどうか
    - 失敗した API 呼び出しを再試行するかどうか
    - cexal 監査ログを含めるかどうか
    - 特に大規模なデータ抽出の際に、データ抽出の間に一定時間待機するかどうか

    詳細については、[SAL ログ コネクタの構成](#sal-logs-connector-settings)に関するセクションを参照してください。

1. 更新した **systemconfig.ini** ファイルをマシンの **sapcon** ディレクトリに保存します。

1. 資格情報として **env.list** ファイルを使用することを選択した場合は、必要な資格情報を持つ一時的な **env.list** ファイルを作成します。 Docker コンテナーが正常に動作したら、このファイルを必ず削除してください。

    > [!NOTE]
    > 以下のスクリプトでは、各 Docker コンテナーが特定の ABAP システムに接続しています。 実際の環境に合わせて、必要に応じてスクリプトを変更してください。
    >

    次を実行します。

    ```bash
    ##############################################################
    # Include the following section if you're using user authentication
    ##############################################################
    # env.list template for Credentials
    SAPADMUSER=<SET_SAPCONTROL_USER>
    SAPADMPASSWORD=<SET_SAPCONTROL_PASS>
    ABAPUSER=SET_ABAP_USER>
    ABAPPASS=<SET_ABAP_PASS>
    JAVAUSER=<SET_JAVA_OS_USER>
    JAVAPASS=<SET_JAVA_OS_USER>
    ##############################################################
    # Include the following section if you are using Azure Keyvault
    ##############################################################
    # env.list template for AZ Cli when MI is not enabled
    AZURE_TENANT_ID=<your tenant id>
    AZURE_CLIENT_ID=<your client/app id>
    AZURE_CLIENT_SECRET=<your password/secret for the service principal>
    ##############################################################
    ```

1. SAP データ コネクタがインストールされた、定義済みの Docker イメージをダウンロードして実行します。  次を実行します。

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker run --env-file=<env.list_location> -d --restart unless-stopped -v /home/$(pwd)/sapcon/<sap-sid>/:/sapcon-app/sapcon/config/system --name sapcon-<sid> sapcon
    rm -f <env.list_location>
    ```

1. Docker コンテナーが正しく実行されていることを確認します。 次を実行します。

    ```bash
    docker logs –f sapcon-[SID]
    ```

1. **Microsoft Sentinel - Continuous Threat Monitoring for SAP** ソリューションのデプロイを続行します。

    ソリューションをデプロイすると、SAP データ コネクタが Microsoft Sentinel に表示されるようになり、SAP ブックと分析ルールがデプロイされます。 完了したら、SAP ウォッチリストを手動で追加してカスタマイズします。

    詳細については、「[SAP セキュリティ コンテンツをデプロイする](sap-deploy-solution.md#deploy-sap-security-content)」を参照してください。

## <a name="manually-configure-the-sap-data-connector"></a>SAP データ コネクタを手動で構成する

Microsoft Sentinel の SAP ソリューション データ コネクタは、[デプロイ手順](#perform-an-expert--custom-installation)の一部として SAP データ コネクタ マシンにクローンした **systemconfig.ini** ファイルで構成されます。

次のコードは、**systemconfig.ini** ファイルのサンプルを示しています。

```python
[Secrets Source]
secrets = '<DOCKER_RUNTIME/AZURE_KEY_VAULT/DOCKER_SECRETS/DOCKER_FIXED>'
keyvault = '<SET_YOUR_AZURE_KEYVAULT>'
intprefix = '<SET_YOUR_PREFIX>'

[ABAP Central Instance]
##############################################################
# Define the following values according to your server configuration.
ashost = <SET_YOUR_APPLICATION_SERVER_HOST>
mshost = <SET_YOUR_MESSAGE_SERVER_HOST> - #In case different then App
##############################################################
group = <SET_YOUR_LOGON_GROUP>
msserv = <SET_YOUR_MS_SERVICE> - #Required only if the message server service is not defined as sapms<SYSID> in /etc/services
sysnr = <SET_YOUR_SYS_NUMBER>
user = <SET_YOUR_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
passwd = <SET_YOUR_PASSWORD>
snc_partnername = <SET_YOUR_SNC_PARTNER_NAME>
snc_lib = <SET_YOUR_SNC_LIBRARY_PATH>
x509cert = <SET_YOUR_X509_CERTIFICATE>
##############################################################
sysid = <SET_YOUR_SYSTEM_ID>
client = <SET_YOUR_CLIENT>

[Azure Credentials]
loganalyticswsid = <SET_YOUR_LOG_ANALYTICS_WORKSPACE_ID>
publickey = <SET_YOUR_PUBLIC_KEY>

[File Extraction ABAP]
osuser = <SET_YOUR_SAPADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
ospasswd = <SET_YOUR_SAPADM_PASS>
x509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
appserver = <SET_YOUR_SAPCTRL_SERVER IP OR FQDN>
instance = <SET_YOUR_SAP_INSTANCE NUMBER, example 10>
abapseverity = <SET_ABAP_SEVERITY 0 = All logs ; 1 = Warning ; 2 = Error>
abaptz = <SET_ABAP_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use abaptz = GMT+12>

[File Extraction JAVA]
javaosuser = <SET_YOUR_JAVAADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
javaospasswd = <SET_YOUR_JAVAADM_PASS>
javax509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
javaappserver = <SET_YOUR_JAVA_SAPCTRL_SERVER IP ADDRESS OR FQDN>
javainstance = <SET_YOUR_JAVA_SAP_INSTANCE for example 10>
javaseverity = <SET_JAVA_SEVERITY  0 = All logs ; 1 = Warning ; 2 = Error>
javatz = <SET_JAVA_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use javatz = GMT+12>
```

### <a name="define-the-sap-logs-that-are-sent-to-microsoft-sentinel"></a>Microsoft Sentinel に送信される SAP ログを定義する

次のコードを Microsoft Sentinel SAP ソリューション **systemconfig.ini** ファイルに追加して、Microsoft Sentinel に送信されるログを定義します。

詳細については、[Microsoft Sentinel SAP ソリューションのログ リファレンス (パブリック プレビュー)](sap-solution-log-reference.md) に関するページを参照してください。

```python
##############################################################
# Enter True OR False for each log to send those logs to Microsoft Sentinel
[Logs Activation Status]
ABAPAuditLog = True
ABAPJobLog = True
ABAPSpoolLog = True
ABAPSpoolOutputLog = True
ABAPChangeDocsLog = True
ABAPAppLog = True
ABAPWorkflowLog = True
ABAPCRLog = True
ABAPTableDataLog = False
# ABAP SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
ABAPFilesLogs = False
SysLog = False
ICM = False
WP = False
GW = False
# Java SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
JAVAFilesLogs = False
##############################################################
```

### <a name="sal-logs-connector-settings"></a>SAL ログ コネクタの設定

次のコードを Microsoft Sentinel SAP データ コネクタの **systemconfig.ini** ファイルに追加して、Microsoft Sentinel に取り込まれる SAP ログの他の設定を定義します。

詳細については、[エキスパートまたはカスタムの SAP データ コネクタのインストールを実行する](#perform-an-expert--custom-installation)方法に関するセクションを参照してください。

```python
##############################################################
[Connector Configuration]
extractuseremail = True
apiretry = True
auditlogforcexal = False
auditlogforcelegacyfiles = False
timechunk = 60
##############################################################
```

このセクションでは、次のパラメーターを構成できます。

|パラメーター名  |説明  |
|---------|---------|
|**extractuseremail**     |  監査ログにユーザーのメール アドレスを含めるかどうかを指定します。       |
|**apiretry**     |   API 呼び出しをフェールオーバー メカニズムとして再試行するかどうかを決定します。      |
|**auditlogforcexal**     |  システムが、SAP BASIS バージョン 7.4 などの非 SAL システムの監査ログを強制的に使用するかどうかを指定します。       |
|**auditlogforcelegacyfiles**     |  パッチ レベルの低い SAP BASIS バージョン 7.4 など、レガシ システムの機能を持つ監査ログをシステムで強制的に使用するかどうかを指定します。|
|**timechunk**     |   システムが、データ抽出の間隔として特定の分数を待機することを指定します。 このパラメータは、大量のデータが予想される場合に使用します。 <br><br>たとえば、最初の 24 時間の間の初期データの読み込み中、各データ抽出に十分な時間を与えるために、データ抽出を 30 分ごとにしか実行しないようにすることができます。 このような場合は、この値を **30** に設定します。  |
|     |         |

### <a name="configuring-an-abap-sap-control-instance"></a>ABAP SAP Control インスタンスを構成する

NW RFC と SAP Control Web サービスベースのログの両方を含む、すべての ABAP ログを Microsoft Sentinel に取り込むには、次の ABAP SAP Control の詳細を構成します。

|設定  |説明  |
|---------|---------|
|**javaappserver**     |ご使用の SAP Control ABAP サーバー ホストを入力します。 <br>例: `contoso-erp.appserver.com`         |
|**javainstance**     |ご使用の SAP Control ABAP インスタンス番号を入力します。 <br>例: `00`         |
|**abaptz**     |ご使用の SAP Control ABAP サーバーで設定されているタイム ゾーンを GMT 形式で入力します。 <br>例: `GMT+3`         |
|**abapseverity**     |ABAP ログを Microsoft Sentinel に取り込む最小限の包括的な重大度レベルを入力します。  次の値が含まれます。 <br><br>- **0** = すべてのログ <br>- **1** = 警告 <br>- **2** = エラー     |

### <a name="configuring-a-java-sap-control-instance"></a>Java SAP Control インスタンスを構成する

SAP Control Web サービス ログを Microsoft Sentinel に取り込むには、次の JAVA SAP Control インスタンスの詳細を構成します。

|パラメーター  |説明  |
|---------|---------|
|**javaappserver**     |ご使用の SAP Control Java サーバー ホストを入力します。 <br>例: `contoso-java.server.com`         |
|**javainstance**     |ご使用の SAP Control ABAP インスタンス番号を入力します。 <br>例: `10`         |
|**javatz**     |ご使用の SAP Control Java サーバーで設定されているタイム ゾーンを GMT 形式で入力します。 <br>例: `GMT+3`         |
|**javaseverity**     |Web サービス ログを Microsoft Sentinel に取り込む最小限の包括的な重大度レベルを入力します。  次の値が含まれます。 <br><br>- **0** = すべてのログ <br>- **1** = 警告 <br>- **2** = エラー     |

## <a name="next-steps"></a>次のステップ

SAP データ コネクタをインストールしたら、SAP 関連のセキュリティ コンテンツを追加できます。

詳細については、[SAP ソリューションを配置する](sap-deploy-solution.md#deploy-sap-security-content)方法に関するセクションを参照してください。

詳細については、次を参照してください。

- [SNC を使用して Microsoft Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [Microsoft Azure Sentinel SAP ソリューションの詳細な SAP 要件](sap-solution-detailed-requirements.md)
- [Microsoft Sentinel SAP ソリューションのログ リファレンス](sap-solution-log-reference.md)
- [Microsoft Sentinel SAP ソリューション: セキュリティ コンテンツ リファレンス](sap-solution-security-content.md)
- [Microsoft Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)
