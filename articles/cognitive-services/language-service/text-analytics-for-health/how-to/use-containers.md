---
title: Text Analytics for health コンテナーの使用方法
titleSuffix: Azure Cognitive Services
description: Text Analytics for health の Docker コンテナーを使用して、オンプレミスで医療情報を抽出してラベル付けする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: b3ab4a3a3261fbd53d5294cf213823a369adbd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092177"
---
# <a name="use-text-analytics-for-health-containers"></a>Text Analytics for health コンテナーを使用する

コンテナーを使うと、独自のインフラストラクチャで Text Analytics for health API をホストできます。 Text Analytics for health をリモートで呼び出すことでは満たせないセキュリティまたはデータ ガバナンスの要件がある場合は、コンテナーが適している可能性があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Text Analytics for health コンテナーを使用する前に、次の前提条件を満たす必要があります。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

* ホスト コンピューターに [Docker](https://docs.docker.com/) がインストールされていること。 コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 
    * Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。
    * [Docker の概念](https://docs.docker.com/get-started/overview/)に関する基本的な知識が必要です。 
* Free (F0) または Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)の<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースの作成"  target="_blank">言語リソース</a>。

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>ホスト コンピューターの要件と推奨事項

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

次の表では、Text Analytics for health コンテナーの最小および推奨仕様を説明します。 各 CPU コアは、少なくとも 2.6 ギガヘルツ (GHz) 以上である必要があります。 許容される 1 秒あたりのトランザクション数 (TPS) も示されています。

|  | 最小ホスト仕様 | 推奨されるホスト仕様 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **1 ドキュメント/要求**   |  4 コア、10 GB メモリ | 6 コア、12 GB メモリ |15 | 30|
| **10 ドキュメント/要求**   |  6 コア、16 GB メモリ | 8 コア、20 GB メモリ |15 | 30|

CPU コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、Microsoft パブリック コンテナー レジストリからこのコンテナー イメージをダウンロードします。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```


[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーをホスト コンピューター上に準備できたら、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してコンテナーを実行します。 コンテナーは一度実行すると、お客様が停止するまで動作し続けます。

> [!IMPORTANT]
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
> * コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。
>   * [責任ある AI](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) 確認も `accept` の値で存在する必要があります。
> * 感情分析コンテナーと言語検出コンテナーは API の v3 を使用し、一般公開されています。 v2 の API が使用されているキー フレーズ抽出のコンテナーは、プレビュー段階です。

Text Analytics for Health コンテナーをインストールして実行するには、複数の方法があります。 

- Azure portal を使用して言語リソースを作成し、Docker を使用してコンテナーを取得します。
- Docker を搭載した Azure VM を使用して、コンテナーを実行します。 [Azure 上の Docker](../../../../docker/index.yml) に関するページを参照してください。
- 次の PowerShell と Azure CLI のスクリプトを使用して、リソースのデプロイとコンテナーの構成を自動化します。

### <a name="run-the-container-locally"></a>コンテナーをローカルで実行する

コンテナー イメージをダウンロードした後に独自の環境でコンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 言語リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

このコマンドは、次の操作を行います。

- コンテナー イメージの Text Analytics for health コンテナーを実行します
- 6 つの CPU コアと 12 ギガバイト (GB) のメモリを割り当てます
- TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
- 使用許諾契約 (Eula) と責任ある AI (RAI) の条項に同意します
- コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

### <a name="demo-ui-to-visualize-output"></a>出力を視覚化するためのデモ UI

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。  また、コンテナーのエンドポイントに `/demo` を追加するとアクセス可能となるコンテナーに、視覚化ツールを提供しています。 次に例を示します。

```
http://<serverURL>:5000/demo
```

次の cURL 要求の例を使用し、`serverURL` 変数を適切な値に置き換えて、デプロイしたコンテナーにクエリを送信します。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers を使用してコンテナーをインストールする

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) は、クラウドでコンテナーを実行する専用の Azure リソースです。 自動スケーリング、Docker コンテナーと Docker Compose のサポート、HTTPS のサポートなど、すぐに使用できる機能があります。

> [!NOTE]
> Azure Web アプリを使用すると、ドメインが自動的に `<appservice_name>.azurewebsites.net` の形式で取得されます

Azure CLI を使用してこの PowerShell スクリプトを実行し、HTTPS 経由でサブスクリプションとコンテナー イメージを使用して、Web App for Containers を作成します。 スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure コンテナー インスタンスを使用してコンテナーをインストールする

Azure コンテナー インスタンス (ACI) を使用して、デプロイを容易にすることもできます。 ACI は、サーバーレスなマネージド Azure 環境内で Docker コンテナーをオンデマンドで実行できるリソースです。 

Azure portal を使用して ACI リソースをデプロイする手順については、[Azure Container Instances の使用方法](../../../containers/azure-container-instance-recipe.md)に関する記事を参照してください。 また、Azure CLI を使用して次の PowerShell スクリプトを実行し、コンテナー イメージを使用してサブスクリプションに ACI を作成することもできます。  スクリプトが完了するまで (約 25 から 30 分) 待ってから、最初の要求を送信します。  ACI リソースあたりの CPU の最大数に制限があるため、要求あたり 5 個を超える大きいドキュメント (それぞれ約 5000 文字) を送信することが予想される場合は、このオプションを選択しないでください。
可用性の情報については、[ACI リージョンのサポート](../../../../container-instances/container-instances-region-availability.md)に関する記事を参照してください。 

> [!NOTE] 
> Azure Container Instances には、組み込みドメインに対する HTTPS のサポートは含まれません。 HTTPS が必要な場合は、証明書の作成とドメインの登録など、手動で構成する必要があります。 この手順については、以下の NGINX を参照してください。

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>ACI の接続をセキュリティで保護する

既定では、コンテナー API で ACI を使用するときに、セキュリティは提供されません。 これは、通常、コンテナーはネットワーク ブリッジによって外部から保護されるポッドの一部として実行されるためです。 ただし、前面にあるコンポーネントを使用してコンテナーを変更し、コンテナー エンドポイントを非公開にしておくこともできます。 次の例では、[NGINX](https://www.nginx.com) をイングレス ゲートウェイとして使用し、HTTPS/SSL およびクライアント証明書認証をサポートします。

> [!NOTE]
> NGINX は、オープンソースで高パフォーマンスの HTTP サーバーとプロキシです。 NGINX コンテナーは、1 つのコンテナーに対する TLS 接続を終端するために使用できます。 さらに複雑な NGINX イングレス ベースの TLS 終端ソリューションも可能です。

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX をイングレス ゲートウェイとして設定する

NGINX では、[構成ファイル](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)を使用して、実行時に機能を有効にします。 別のサービスに対する TLS 終端を有効にするには、TLS 接続を終端するための SSL 証明書と、サービスのアドレスを指定するための `proxy_pass` を、指定する必要があります。 以下ではサンプルを提供します。


> [!NOTE]
> `ssl_certificate` では、NGINX コンテナーのローカル ファイル システム内でパスが指定されている必要があります。 `proxy_pass` に指定するアドレスは、NGINX コンテナーのネットワーク内から使用できる必要があります。

NGINX コンテナーにより、`/etc/nginx/conf.d/` にマウントされている `_.conf_` 内のすべてのファイルが HTTP 構成パスに読み込まれます。

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Docker Compose ファイルの例

下の例では、[Docker Compose](https://docs.docker.com/compose/reference/overview) ファイルを作成して、NGINX と health のコンテナーをデプロイする方法を示します。

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

この Docker Compose ファイルを開始するには、ファイルのルート レベルでコンソールから次のコマンドを実行します。

```bash
docker-compose up
```

詳細については、[NGINX SSL 終端](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)に関する NGINX のドキュメントを参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]


### <a name="structure-the-api-request-for-the-container"></a>コンテナーへの API 要求を作成する

Postman を使用するか、次の cURL 要求の例を使用して、デプロイしたコンテナーにクエリを送信します。`serverURL` 変数は適切な値に置き換えます。  コンテナーへの URL での API のバージョンは、ホストされた API とは異なることに注意してください。

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

次の JSON は、Text Analytics for health 要求の POST 本文に添付されている JSON ファイルの例です。

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>コンテナーの応答本文

次の JSON は、コンテナー化された同期呼び出しからの Text Analytics for health 応答本文の例です。

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

## <a name="run-the-container-with-client-library-support"></a>クライアント ライブラリのサポートを使用してコンテナーを実行する

コンテナー バージョン `3.0.017010001-onprem-amd64` 以降 (または `latest` コンテナーを使用している場合)、[クライアント ライブラリ](../quickstart.md)を使用して Text Analytics for health コンテナーを実行できます。 これを行うには、次のパラメーターを `docker run` コマンドに追加します。

`enablelro=true`

その後、クライアント オブジェクトを認証するときに、コンテナーが実行されているエンドポイントを使用します。

`http://localhost:5000`

たとえば、C# を使用している場合は、次のコードを使用します。

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](configure-containers.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Text Analytics for health コンテナーは、Azure アカウントの "言語" リソースを使用して、Azure に課金情報を送信します。 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

## <a name="summary"></a>まとめ

この記事では、Text Analytics for health コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Text Analytics for health によって Docker 用の Linux コンテナーが提供されます
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、Text Analytics for health コンテナーのホスト URI を指定すると、コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 お客様のデータ (解析対象のテキストなど) が Cognitive Services コンテナーによって Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](configure-containers.md)に関するページを参照してください。
