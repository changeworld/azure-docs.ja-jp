---
title: クラスター構成できめ細かなロールベースのアクセスに移行する - Azure HDInsight
description: クラスター構成できめ細かなロールベースのアクセスに移行するために必要な変更について学習します。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006235"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>クラスター構成できめ細かなロールベースのアクセスに移行する

機密情報を取得するための、よりきめ細かいロールベースのアクセスのサポートに対して、いくつかの重要な変更が導入されています。 影響を受ける**エンティティまたはシナリオ**の中のいずれかを使用している場合は、これらの変更の一環として、何らかの[アクションが必要になる場合があります](#am-i-affected-by-these-changes)。

## <a name="what-is-changing"></a>何が変わるのですか?

以前は、所有者、共同作成者、または閲覧者の [RBAC ロール](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) を保有するクラスター ユーザーが、HDInsight API を介してシークレットを取得することができました。
今後、ユーザーは閲覧者ロールを使用してこれらのシークレットにアクセスできなくなります。 また、共同作成者または所有者の管理アクセス許可を付与されなくても、シークレットを取得することができる新しい 'HDInisght クラスター オペレーター' ロールも導入されます。 まとめると次のようになります。

| Role                                  | 以前                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | - シークレットを含む読み取りアクセス                                                                   | - シークレットを**除く**読み取りアクセス |           |   |   |
| HDInsight クラスター オペレーター<br>(新しいロール) | 該当なし                                                                                              | - シークレットを含む読み取り/書き込みアクセス         |   |   |
| Contributor                           | - シークレットを含む読み取り/書き込みアクセス<br>- すべての種類の Azure リソースを作成および管理します。     | 変更なし |
| Owner                                 | - シークレットを含む読み取り/書き込みアクセス<br>- すべてのリソースへのフル アクセス<br>- アクセスを他のユーザーに委任する | 変更なし |

HDInsight クラスター オペレーター ロールの割り当てを特定のユーザーに追加することで、そのユーザーにクラスター シークレットへの読み取り/書き込みアクセスを付与する方法の詳細については、後のセクション「[HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加する](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)」を参照してください。

## <a name="am-i-affected-by-these-changes"></a>私はこれらの変更の影響を受けますか?

次のエンティティとシナリオには影響があります。

- [API](#api):`/configurations` エンドポイントまたは `/configurations/{configurationName}` エンドポイントを使用しているユーザー。
- ___ およびそれ以前のバージョンの [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code)。
- ___ およびそれ以前のバージョンの [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij)。
- ___ およびそれ以前のバージョンの [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse)。
- [.NET 用 SDK](#sdk-for-net)
    - [バージョン 1.x または 2.x](#versions-1x-and-2x): ConfigurationsOperationsExtensions クラスから `GetClusterConfigurations`、`GetConnectivitySettings`、`ConfigureHttpSettings`、`EnableHttp`、または `DisableHttp` メソッドを使用しているユーザー。
    - [バージョン 3.x 以降](#versions-3x-and-up): `ConfigurationsOperationsExtensions` クラスから `EnableHttp`、`DisableHttp`、`Update`、または `Get` メソッドを使用しているユーザー。
- [Python 用 SDK](#sdk-for-python): ConfigurationsOperations クラスから `get` または `update` メソッドを使用しているユーザー。
- [Java 用 SDK](#sdk-for-java): ConfigurationsInner クラスから `update` メソッドまたは `get` メソッドを使用しているユーザー。
- [Go 用 SDK](#sdk-for-go): ConfigurationsClient 構造体から `Get` メソッドまたは `Update` メソッドを使用しているユーザー。

ご利用のシナリオの移行手順を確認するには、以下のセクションを参照 (または上記のリンクを使用) してください。

### <a name="api"></a>API

次の API は変更されるか非推奨となります。

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (機密情報が削除される) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 以前は、個々の構成の種類 (機密情報を含む) を取得するために使用されていました。
    - この API 呼び出しからは、シークレットを省略した状態で個々の構成の種類が返されるようになります。 シークレットを含むすべての構成を取得するには、新しい [POST/configurations]() 呼び出しを使用します。 ゲートウェイ設定だけを取得するには、新しい [POST/getGatewaySettings]() 呼び出しを使用します。
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (非推奨) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - 以前は、すべての構成 (機密情報を含む) を取得するために使用されていました。
    - この API 呼び出しはサポートされなくなります。 今後すべての構成を取得するには、新しい [POST /configurations]() 呼び出しを使用します。 機密性の高いパラメーターを省略した状態で構成を取得するには、[GET /configurations/{configurationName}]() 呼び出しを使用します。
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (非推奨) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 以前はゲートウェイの資格情報を更新するために使用されていました。
    - この API 呼び出しは非推奨となり、サポートされなくなります。 代わりに、新しい [POST/updateGatewaySettings]() を使用してください。

次の代わりとなる API が追加されました。</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - この API は機密情報を含むすべての構成を取得する場合に使用します。
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - この API はゲートウェイ設定を取得する場合に取得します。
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - この API はゲートウェイ設定 (ユーザー名やパスワード) を更新する場合に使用します。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

1.1.1 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure HDInsight Tools for Visual Studio Code に更新](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)してください。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

3.21.0 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure Toolkit for IntelliJ プラグインに更新](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)してください。

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

2019-03-29 またはそれよりも古いリリースを使用している場合は、中断を回避するために最新バージョンの Azure Toolkit for Eclipse に更新してください。

### <a name="sdk-for-net"></a>.NET 用 SDK

#### <a name="versions-1x-and-2x"></a>バージョン 1.x および 2.x

[バージョン 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- `ClusterOperationsExtensions.GetClusterConfigurations` では、ストレージ キー (コア サイト) または HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ClusterOperationsExtensions.ListConfigurations` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。
    - HTTP ゲートウェイ資格情報だけを取得するには、`ClusterOperationsExtensions.GetGatewaySettings` を使います。

- `ClusterOperationsExtensions.GetConnectivitySettings` は非推奨になり、`ClusterOperationsExtensions.GetGatewaySettings` に置き換えられています。

- `ClusterOperationsExtensions.ConfigureHttpSettings` は非推奨になり、`ClusterOperationsExtensions.UpdateGatewaySettings` に置き換えられています。

- `ConfigurationsOperationsExtensions.EnableHttp` と `DisableHttp` は非推奨になりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

#### <a name="versions-3x-and-up"></a>バージョン 3.x 以降

[バージョン 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ConfigurationOperationsExtensions.List` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、`ClusterOperationsExtensions.GetGatewaySettings` を使います。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) は非推奨になり、`ClusterOperationsExtensions.UpdateGatewaySettings` に置き換えられています。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) と [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) は非推奨となりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

### <a name="sdk-for-python"></a>Python 用 SDK

[バージョン 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) の HDInsight SDK for Python に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ConfigurationsOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) は非推奨になり、[`ClusterOperationsExtensions.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) に置き換えられています。

### <a name="sdk-for-java"></a>Java 用 SDK

[バージョン 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) の HDInsight SDK for Java に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ConfigurationsInner.list` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、`ConfigurationsOperations.get_gateway_settings` を使います。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) は非推奨になり、`ClusterOperationsExtensions.update_gateway_settings` に置き換えられています。

### <a name="sdk-for-go"></a>Go 用 SDK

[バージョン 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) の HDInsight SDK for Go に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings) を使用します。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) は非推奨になり、[`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) に置き換えられています。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加する

[共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)ロールまたは[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)ロールを保有するユーザーは、クラスター ゲートウェイ資格情報やストレージ アカウント キーなどの HDInsight クラスター シークレットへの読み取り/書き込みアクセスを許可されるユーザーに HDInsight Cluster Operator ロールを付与することができます。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

このロール割り当てを追加するには、Azure CLI 内で次のコマンドを使用するのが最も簡単な方法です。

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> このコマンドは、共同作成者ロールまたは所有者ロールを保有するユーザーによって、それらのアクセス許可が付与される場合にのみ、実行される必要があります。 `--assignee` は HDInsight クラスター オペレーター ロールの割り当て先とするユーザーの電子メール アドレスです。

上記のコマンドでは、サブスクリプション レベルでこのロールが付与されます。 リソース グループ レベルでこのロールを付与するには、コマンドをそのように変更することができます。

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加することもできます。 詳細については、[RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する - ロール割り当ての追加](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)に関するページを参照してください。
