---
title: Azure HDInsight クラスター ノードのホスト名を取得する方法
description: Azure HDInsight クラスター ノードのホスト名と FQDN 名を取得する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111406"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>クラスター ノードのホスト名を検索する

HDInsight クラスターは、パブリック DNS clustername.azurehdinsight.net を使用して作成されます。 個々のノードに SSH 接続する場合、または同じカスタム仮想ネットワーク内にあるクラスター ノードへの接続を設定する場合は、クラスター ノードのホスト名または完全修飾ドメイン名 (FQDN) を使用する必要があります。

この記事では、クラスター ノードのホスト名を取得する方法について説明します。 Ambari Web UI から手動で取得する方法と、Ambari REST API から自動で取得する方法があります。

> [!WARNING]
> クラスター ノードのホスト名を取得するには、次の推奨される方法を使用してください。 ホスト名の番号は順序が保証されていません。また、HDInsight はリリースの更新に伴い、VM に合わせてホスト名の形式を変更することがあります。 現在存在する特定の名前付け規則に依存しないようにしてください。 
>

ホスト名は、Ambari UI または Ambari REST API を使用して取得できます。 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Ambari Web UI からホスト名を取得する
Ambari Web UI を使用すると、ノードに SSH 接続するときにホスト名を取得できます。 Ambari Web UI ホスト ビューは、お使いの HDInsight クラスター (`https://CLUSTERNAME.azurehdinsight.net/#/main/hosts`) にあります。`CLUSTERNAME` はお使いのクラスターの名前です。

![Ambari-UI でホスト名を取得する](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>AmbariREST API からホスト名を取得する
自動化スクリプトを作成するときに、ホストへの接続を作成する前に、Ambari REST API を使用してホスト名を取得できます。 ホスト名の番号は順序が保証されていません。また、HDInsight はリリースの更新に伴い、VM に合わせてホスト名の形式を変更することがあります。 現在存在する特定の名前付け規則に依存しないようにしてください。 

ここでは、クラスター内のノードの FQDN を取得する方法の例をいくつか紹介します。 Ambari REST API に関する詳細については、「[Apache Ambari REST API を使用した HDInsight クラスターの管理](.\hdinsight-hadoop-manage-ambari-rest-api.md)」を参照してください

次の例では、[jq](https://stedolan.github.io/jq/) または [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) を使用して、JSON 応答ドキュメントを解析し、ホスト名だけを表示します。

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```