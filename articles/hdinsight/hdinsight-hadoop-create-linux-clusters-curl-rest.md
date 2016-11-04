---
title: cURL と Azure REST API を使用した HDInsight 用の Linux ベースの Hadoop、HBase、または Storm クラスターの作成 | Microsoft Docs
description: cURL、Azure リソース マネージャー テンプレート、および Azure REST API を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。 クラスターの種類 (Hadoop、HBase、または Storm) を指定するか、スクリプトを使用してカスタム コンポーネントをインストールすることができます。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>cURL と Azure REST API を使用して HDInsight に Linux ベースのクラスターを作成する
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure REST API を使用すると、Azure プラットフォームでホストされたサービスで、Linux ベースの HDInsight クラスターなど新しいリソースの作成を含む管理操作を実行できます。 このドキュメントでは、Azure リソース マネージャー テンプレートを作成し、HDInsight クラスターと関連するストレージを構成してから、cURL を使用してテンプレートを Azure REST API にデプロイして新しい HDInsight クラスターを作成する方法を学びます。

> [!IMPORTANT]
> この文書の手順では、HDInsight クラスターにワーカー ノードの既定数 (4) を使用します。 クラスター作成または作成後の拡大で 32 以上のワーカー ノードを予定している場合、コア数が 8 個以上で RAM が 14GB 以上のサイズのヘッド ノードを選択する必要があります。
> 
> ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure CLI**。 Azure CLI を使用してサービス プリンシパルを作成し、Azure REST API に対する要求用の認証トークンを生成します。
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]
* **cURL**。 このユーティリティは、パッケージ管理システムを通じて、または [http://curl.haxx.se/](http://curl.haxx.se/)からダウンロードして使用できます。
  
  > [!NOTE]
  > このドキュメントのコマンドを実行するのに PowerShell を使用する場合は、既定で作成される `curl` エイリアスをまず削除する必要があります。 PowerShell プロンプトの `curl` コマンドを使用した場合、このエイリアスは cURL ではなく PowerShell コマンドレット Invoke-WebRequest を使用して、このドキュメントで使用する多くのコマンドのエラーを返します。
  > 
  > このエイリアスを削除するには、PowerShell プロンプトから以下を使用します。
  > 
  > `Remove-item alias:curl`
  > 
  > エイリアスが削除されると、システムにインストールした cURL のバージョンを使用できるようになります。
  > 
  > 

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>テンプレートの作成
Azure リソース管理テンプレートは、__リソース グループ__とその中のすべてのリソース (HDInsight など) について記述する JSON ドキュメントです。このテンプレート基準の手法では、1 つのテンプレートで HDInsight に必要なすべてのリソースを定義でき、グループに変更を適用する__デプロイメント__を介して、グループに対する変更をまとめて管理できます。

通常、テンプレートは 2 つの部分 (テンプレート自体と、自身の構成に固有の値を読み込んだパラメーター ファイル) から成ります。 たとえば、クラスター名、管理者名、パスワードなどです。 直接 REST API を使用する場合、これらを 1 つのファイルにまとめる必要があります。 この JSON ドキュメントの形式は次のようになります。

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

たとえば、以下は [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)からのテンプレートとパラメーター ファイルを併せたもので、ここでは、SSH ユーザー アカウントをセキュリティ保護するためにパスワードを使用して Linux ベースのクラスターを作成します。

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {

                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {

                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

このサンプルをこのドキュメントの手順で使用します。 ドキュメント末尾の *Parameters* セクションのプレースホルダーの **値** を、クラスターに使用する値に置き換える必要があります。

## <a name="login-to-your-azure-subscription"></a>Azure サブスクリプションへのログイン
「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../xplat-cli-connect.md)」に記載されている手順に従い、`azure login` コマンドを使用してサブスクリプションに接続します。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成
> [!NOTE]
> これらの手順は、「 *Azure Resource Manager でのサービス プリンシパルの認証* 」ドキュメントの「 [パスワードによるサービス プリンシパルの認証 - Azure CLI](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) 」で提供される情報の要約版です。 これらの手順は、HDInsight クラスターなどの Azure リソースを作成するために使用する REST API 要求の認証に使用できる新しいサービス プリンシパルを作成します。
> 
> 

1. コマンド プロンプト、ターミナル セッション、またはシェルから、次のコマンドを使用して Azure サブスクリプションを一覧します。
   
        azure account list
   
    一覧で、使用するサブスクリプションを選択し、 **Id** 列を書き留めます。 これは **サブスクリプション ID** で、このドキュメントのほとんどの手順で使用されます。
2. Azure Active Directory に新しいアプリケーションを作成する
   
        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
   
    `--name`、`--home-page`、および `--identifier-uris` の値を自分の値に置き換えます。 新しい Active Directory エントリのパスワードを指定します。
   
   > [!NOTE]
   > このアプリケーションをサービス プリンシパルでの認証用に作成しているため、`--home-page` と `--identifier-uris` の値は、一意の URI である必要があるだけで、インターネットでホストされている実際の Web ページを参照する必要はありません。
   > 
   > 
   
    返されたデータの **AppId** 値を保存します。
   
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
3. 前に返された **AppId** 値を使用して、サービス プリンシパルを作成します。
   
        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
   
     返されたデータの **Object Id** 値を保存します。
   
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
4. 以前に返された **Object ID** 値を使用して、**所有者__の役割をサービス プリンシパルに割り当てます。 前に取得した __サブスクリプション ID** も使用する必要があります。
   
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
   
    このコマンドが完了すると、サービス プリンシパルには特定のサブスクリプション ID にアクセスする所有者があります。

## <a name="get-an-authentication-token"></a>認証トークンの取得
1. 次を使用して、自分のサブスクリプションの **Tenant ID** を見つけます。
   
        azure account show -s <subscription ID>
   
    返されたデータで、 __Tenant ID__を検索します。
   
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK
2. Azure REST API を使用して新しいトークンを生成します。
   
        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
   
    **TenantID**、**AppID**、__パスワード__を、以前に取得または使用した値に置き換えます。
   
    この要求が成功したら、200 シリーズの応答が届きます。応答本文に JSON ドキュメントが含まれています。
   
    この要求によって返される JSON ドキュメントには **access_token** という名前の要素が含まれます。この要素の値はアクセス トークンで、これは本ドキュメントの次のセクションで使用される要求の認証に使用する必要があります。
   
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

## <a name="create-a-resource-group"></a>リソース グループの作成
次を利用して新しいリソース グループを作成します。 リソース (HDInsight クラスターなど) を作成するには、まずグループを作成する必要があります。 

* **SubscriptionID** をサービス プリンシパルの作成時に受信するサブスクリプション ID に置き換えます。
* **AccessToken** を前の手順で受け取ったアクセス トークンに置き換えます。
* **DataCenterLocation** をリソース グループやリソースを作成するデータ センターに置き換えます。 たとえば、"South Central US" のようになります。 
* **ResourceGroupName** をこのグループに使用する名前に置き換えます。

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

この要求が成功したら、200 シリーズの応答が届きます。応答本文に含まれる JSON ドキュメントにグループに関する情報が含まれています。 `"provisioningState"` 要素には `"Succeeded"` の値が含まれます。

## <a name="create-a-deployment"></a>デプロイの作成
以下を使用して、クラスター構成 (テンプレートとパラメーター値) をリソース グループにデプロイします。

* **SubscriptionID** と **AccessToken** を前に使用した値に置き換えます。 
* **ResourceGroupName** を前のセクションで作成したリソース グループの名前に置き換えます。
* **DeploymentName** を、このデプロイに使用する名前に置き換えます。

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> テンプレートとパラメーターを含む JSON ドキュメントをファイルに保存した場合、`-d "{ template and parameters}"` の代わりに以下を使用できます。
> 
> `--data-binary "@/path/to/file.json"`
> 
> 

この要求が成功したら、200 シリーズの応答が届きます。応答本文に含まれる JSON ドキュメントにデプロイ操作に関する情報が含まれています。

> [!IMPORTANT]
> デプロイは送信済みですが、この時点では完了していないことに注意してください。 デプロイを完了するには時間がかかる場合があります (通常約 15 分)。
> 
> 

## <a name="check-the-status-of-a-deployment"></a>デプロイの状態の確認
デプロイの状態を確認するには、次のコマンドを使用します。

* **SubscriptionID** と **AccessToken** を前に使用した値に置き換えます。 
* **ResourceGroupName** を前のセクションで作成したリソース グループの名前に置き換えます。

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

これにより返される JSON ドキュメントにデプロイ操作に関する情報が含まれます。 `"provisioningState"` 要素にはデプロイの状態が含まれます。これに `"Succeeded"` の値が含まれている場合、デプロイは正常に完了しています。 この時点で、クラスターは使用可能になっています。

## <a name="next-steps"></a>次のステップ
HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のトピックを参照してください。 

### <a name="hadoop-clusters"></a>Hadoop クラスター
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター
* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター
* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

<!--HONumber=Oct16_HO2-->


