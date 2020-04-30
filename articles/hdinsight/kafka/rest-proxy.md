---
title: Apache Kafka REST プロキシ - Azure HDInsight
description: Azure HDInsight で Kafka REST プロキシを使用して Apache Kafka 操作を実行する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759004"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST プロキシを使用して Azure HDInsight で Apache Kafka クラスターを操作する

Kafka REST プロキシを使用すると、HTTP 経由の REST API を使用して Kafka クラスターを操作することができます。 この操作は、Kafka クライアントを仮想ネットワークの外部に配置できることを意味します。 クライアントは、Kafka ライブラリを利用するのではなく、Kafka クラスターに対して単純な HTTP 呼び出しを行うことができます。 この記事では、REST プロキシが有効な Kafka クラスターを作成する方法について説明します。 また、REST プロキシを呼び出す方法を示すサンプル コードも提供します。

## <a name="rest-api-reference"></a>REST API リファレンス

Kafka REST API によってサポートされる操作については、[HDInsight Kafka REST Proxy の API リファレンス](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)を参照してください。

## <a name="background"></a>バックグラウンド

![Kafka REST プロキシ設計](./media/rest-proxy/rest-proxy-architecture.png)

API でサポートされている操作の詳細については、[Apache Kafka REST プロキシ API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy) を参照してください。

### <a name="rest-proxy-endpoint"></a>REST プロキシ エンドポイント

REST プロキシを使用して HDInsight Kafka クラスターを作成すると、クラスターの新しいパブリック エンドポイントが作成されます。これは、Azure portal の HDInsight クラスターの **[プロパティ]** にあります。

### <a name="security"></a>Security

Kafka REST プロキシへのアクセスは Azure Active Directory セキュリティ グループで管理されます。 Kafka クラスターを作成するときに、Azure AD セキュリティ グループに REST エンドポイント アクセスを提供します。 REST プロキシにアクセスする必要がある Kafka クライアントは、グループの所有者によってこのグループに登録される必要があります。 グループの所有者は、ポータルまたは PowerShell を使用して登録することができます。

REST プロキシ エンドポイント要求の場合、クライアント アプリケーションは OAuth トークンを取得する必要があります。 このトークンは、セキュリティ グループのメンバーシップを確認するために使用されます。 OAuth トークンを取得する方法については、後述する「[クライアント アプリケーションのサンプル](#client-application-sample)」を参照してください。 クライアント アプリケーションは、HTTP 要求の OAuth トークンを REST プロキシに渡します。

> [!NOTE]  
> AAD セキュリティ グループの詳細については、「[Azure Active Directory グループを使用したアプリとリソース アクセスの管理](../../active-directory/fundamentals/active-directory-manage-groups.md)」を参照してください。 OAuth トークンのしくみについては、「[OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する](../../active-directory/develop/v1-protocols-oauth-code.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

1. アプリケーションを Azure AD に登録する。 Kafka REST プロキシを操作するために記述するクライアント アプリケーションは、このアプリケーションの ID とシークレットを使用して Azure に対する認証を行います。

1. Azure AD セキュリティ グループを作成する。 Azure AD に登録したアプリケーションをセキュリティ グループにグループの**メンバー**として追加します。 このセキュリティ グループは、REST プロキシの操作を許可するアプリケーションを制御するために使用されます。 Azure AD グループの作成方法の詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

    グループの種類が **[セキュリティ]** であることを確認します。
    ![セキュリティ グループ](./media/rest-proxy/rest-proxy-group.png)

    アプリケーションがグループのメンバーであることを確認します。
    ![メンバーシップの確認](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST プロキシが有効な Kafka クラスターを作成する

1. Kafka クラスター作成ワークフローの実行中に、 **[セキュリティとネットワーク]** タブで、 **[Enable Kafka REST proxy]\(Kafka REST プロキシを有効にする\)** オプションをオンにします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **[セキュリティ グループの選択]** をクリックします。 セキュリティ グループの一覧から、REST プロキシにアクセスするセキュリティ グループを選択します。 検索ボックスを使用して、適切なセキュリティ グループを見つけることができます。 下部にある **[選択]** ボタンをクリックします。

     ![Kafka REST プロキシを有効にし、セキュリティ グループを選択する](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. 「[Azure portal を使用して Azure HDInsight 内に Apache Kafka クラスターを作成する](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)」の説明に従って、残りのステップを完了してクラスターを作成します。

1. クラスターが作成されたら、クラスターのプロパティにアクセスして Kafka REST プロキシ URL を記録します。

     ![REST プロキシ URL の表示](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>クライアント アプリケーションのサンプル

次の python コードを使用して、Kafka クラスターで REST プロキシを操作できます。 コード サンプルを使用するには、次のステップを実行します。

1. Python がインストールされているマシンにサンプルコードを保存します。
1. `pip3 install msal` を実行して、必要な Python 依存関係をインストールします。
1. 実際の環境に合わせて **Configure these properties** のコード セクションを変更し、次のプロパティを変更します。

    |プロパティ |説明 |
    |---|---|
    |テナント ID|該当するサブスクリプションがある Azure テナント。|
    |クライアント ID|セキュリティ グループに登録したアプリケーションの ID。|
    |クライアント シークレット|セキュリティ グループに登録したアプリケーションのシークレット。|
    |Kafkarest_endpoint|この値は、[デプロイ セクション](#create-a-kafka-cluster-with-rest-proxy-enabled)の説明に従って、クラスターの概要の **[プロパティ]** タブから取得します。 `https://<clustername>-kafkarest.azurehdinsight.net` という形式にする必要があります。|

1. コマンド ラインから、`sudo python3 <filename.py>` を実行して python ファイルを実行します

このコードは、次の操作を実行します。

1. Azure AD から OAuth トークンを取得します。
1. Kafka REST プロキシに対して要求を行う方法を示します。

Python での OAuth トークンの取得の詳細については、[Python の AuthenticationContext クラス](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)に関する記事を参照してください。 Kafka REST プロキシで作成または削除されていない `topics` がそこに反映される間、遅延が発生する可能性があります。 この遅延は、キャッシュの更新によるものです。

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

curl コマンドを使用して REST プロキシ用のトークンを Azure から取得する方法については、以下の別のサンプルを参照してください。 **トークンの取得中に指定された `scope=https://hib.azurehdinsight.net/.default` が必要であることに注意してください。**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>次のステップ

* [Kafka REST プロキシ API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
