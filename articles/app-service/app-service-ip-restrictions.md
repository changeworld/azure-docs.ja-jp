---
title: Azure App Service のアクセス制限
description: Azure App Service でアクセス制限を設定することによってアプリをセキュリティで保護する方法について説明します。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e1549dda367105db34272eab8a90c1760dd5bb5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010182"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Azure App Service のアクセス制限を設定する

アクセス制限を設定することにより、アプリへのネットワーク アクセスを制御する優先度で順序付けされた許可または拒否のリストを定義することができます。 リストには、IP アドレスまたは Azure Virtual Network のサブネットを含めることができます。 1 つ以上のエントリがある場合、リストの最後にあるものは暗黙的に "*すべて拒否*" になります。

アクセス制限機能は、Azure App Service でホストされるすべてのワークロードに対して機能します。 ワークロードには、Web アプリ、API アプリ、Linux アプリ、Linux コンテナー アプリ、関数が含まれる場合があります。

アプリへの要求が行われると、送信元のアドレスがアクセス制限リスト内の IP アドレス規則に照らして評価されます。 送信元アドレスが Microsoft.Web に対するサービス エンドポイントで構成されているサブネット内にある場合は、ソース サブネットが、アクセス制限リスト内の仮想ネットワーク規則と比較されます。 アドレスがリスト内の規則に基づいてアクセスを許可されない場合、サービスから状態コード [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) の応答があります。

アクセス制限機能は、コードが実行される worker ホストの上流にある App Service フロントエンド ロールに実装されています。 そのため、アクセス制限は、事実上のネットワーク アクセス制御リスト (ACL) です。

Azure 仮想ネットワークから Web アプリへのアクセスを制限する機能は、[サービス エンドポイント][serviceendpoints]によって有効になります。 サービス エンドポイントを使用すると、選択したサブネットからマルチテナント サービスへのアクセスを制限することができます。 App Service Environment でホストされているアプリへのトラフィックを制限することはできません。 App Service Environment 内の場合は、IP アドレス規則を適用することでアプリへのアクセスを制御できます。

> [!NOTE]
> サービス エンドポイントは、ネットワーク側と、それらを有効化する Azure サービスの両方で有効になっている必要があります。 サービス エンドポイントをサポートしている Azure サービスの一覧については、「[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)」を参照してください。
>

![アクセス制限のフローの図。](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="add-or-edit-access-restriction-rules-in-the-portal"></a>ポータルでのアクセス制限規則の追加または編集

アプリにアクセス制限規則を追加するには、次のようにします。

1. Azure portal にサインインします。

1. 左側のウィンドウで、 **[ネットワーク]** を選択します。

1. **[ネットワーク]** ウィンドウの **[アクセス制限]** で、 **[アクセス制限を構成する]** を選択します。

   ![Azure portal の App Service ネットワーク オプション ウィンドウのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions.png)  

1. **[アクセス制限]** ページで、アプリに対して定義されているアクセス制限規則の一覧を確認します。

   ![選択したアプリに対して定義されているアクセス制限規則の一覧を示す、Azure portal の [アクセス制限] ページのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-browse.png)

   一覧には、アプリに適用されている現在の制限がすべて表示されます。 アプリに仮想ネットワーク制限がある場合は、Microsoft.Web に対するサービス エンドポイントが有効かどうかが表に示されます。 アプリに対する制限が定義されていない場合は、どこからでもアプリにアクセスできます。  

### <a name="add-an-access-restriction-rule"></a>アクセス制限規則を追加する

アプリにアクセス制限規則を追加するには、 **[アクセス制限]** ウィンドウで、 **[規則の追加]** を選択します。 規則は、追加するとすぐに有効になります。 

規則は、 **[優先度]** 列の最も小さい数字から始まる優先度の順に適用されます。 追加した規則が 1 つであっても、暗黙の "*すべて拒否*" が有効になります。

**[IP 制限の追加]** ウィンドウで規則を作成するときは、次のようにします。

1. **[アクション]** で、 **[許可]** または **[拒否]** を選択します。  

   ![[IP 制限の追加] ウィンドウのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-ip-add.png)
   
1. 必要に応じて、規則の名前と説明を入力します。  
1. **[種類]** ドロップダウン リストで、規則の種類を選択します。  
1. **[優先度]** ボックスに、優先度の値を入力します。  
1. **[サブスクリプション]** の **[仮想ネットワーク]** と **[サブネット]** のドロップダウン リストで、アクセスを制限する対象を選択します。  

### <a name="set-an-ip-address-based-rule"></a>IP アドレスベースの規則を設定する

前のセクションで説明した手順に従いますが、次のような違いがあります。
* 手順 3 の **[種類]** ドロップダウン リストで、 **[IPv4]** または **[IPv6]** を選択します。 

IPv4 と IPv6 の両方のアドレスについて、クラスレス ドメイン間ルーティング (CIDR) 表記で IP アドレスを指定します。 アドレスを指定するには、*1.2.3.4/32* のようなものを使用できます。この場合、最初の 4 オクテットが IP アドレスを表し、 */32* がマスクです。 すべてのアドレスを表す IPv4 CIDR 表記は 0.0.0.0/0 です。 CIDR 表記の詳細については、「[クラスレス ドメイン間ルーティング](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)」を参照してください。 

## <a name="use-service-endpoints"></a>サービス エンドポイントの使用

サービス エンドポイントを使用することで、選択した Azure 仮想ネットワーク サブネットへのアクセスを制限できます。 特定のサブネットへのアクセスを制限するには、種類が **仮想ネットワーク** である制限規則を作成します。 その後、アクセスを許可または拒否するサブスクリプション、仮想ネットワーク、およびサブネットを選択できます。 

選択したサブネットのサービス エンドポイントが Microsoft.Web でまだ有効になっていない場合は、自動的に有効になります。ただし、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにしていない場合に限ります。 サービス エンドポイントをアプリで有効にしてサブネットでは有効にしないというシナリオは、主としてサブネット上でそれらを有効にするためのアクセス許可があるかどうかに依存します。 

サブネット上でサービス エンドポイントを有効にするために他のユーザーが必要な場合は、 **[見つからない Microsoft.Web サービス エンドポイントを無視する]** チェック ボックスをオンにします。 アプリは、サービス エンドポイントが後からサブネット上で有効にされることを想定して構成されます。 

![種類として仮想ネットワークが選択された [IP 制限の追加] ウィンドウのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

App Service Environment で実行されているアプリへのアクセスを制限するために、サービス エンドポイントを使うことはできません。 アプリが App Service Environment 内にあるときは、IP アクセス規則を適用することでアプリへのアクセスを制御できます。 

サービス エンドポイントにより、Application Gateway やその他の Web アプリケーション ファイアウォール (WAF) デバイスを使用してアプリを構成することができます。 また、セキュリティで保護されたバックエンドを使用して多層アプリケーションを構成することもできます。 詳細については、[ネットワーク機能と App Service](networking-features.md) に関する記事と、「[サービス エンドポイントと Application Gateway の統合](networking/app-gateway-with-service-endpoints.md)」を参照してください。

> [!NOTE]
> - 現在、IP Secure Sockets Layer (SSL) 仮想 IP (VIP) を使用する Web アプリに対しては、サービス エンドポイントがサポートされていません。
> - IP またはサービス エンドポイントの制限として、512 行という制限があります。 512 行の制限よりも多くが必要な場合は、Azure Front Door、Azure App Gateway、WAF などのスタンドアロン セキュリティ製品のインストールを検討することをお勧めします。
>

## <a name="manage-access-restriction-rules"></a>アクセス制限規則を管理する

既存のアクセス制限規則を編集または削除することができます。

### <a name="edit-a-rule"></a>規則を編集する

1. 既存のアクセス制限規則の編集を開始するには、 **[アクセス制限]** ページで、編集する規則をダブルクリックします。

1. **[IP 制限の編集]** ウィンドウで変更を行い、 **[ルールの更新]** を選択します。 優先度順の変更を含め、編集内容はすぐに有効になります。

   ![既存のアクセス制限規則のフィールドを示す、Azure portal の [IP 制限の編集] ウィンドウのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

   > [!NOTE]
   > 規則を編集するとき、IP アドレス規則と仮想ネットワーク規則の間で種類を変更することはできません。 

   ![仮想ネットワーク規則の設定を示す、Azure portal の [IP 制限の編集] ウィンドウのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

### <a name="delete-a-rule"></a>規則を削除する

規則を削除するには、 **[アクセス制限]** ページで、削除する規則の横にある省略記号 ( **...** ) を選択し、次に **[削除]** を選択します。

![削除するアクセス制限規則の横にある [削除] の省略記号を示す、[アクセス制限] ページのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="block-a-single-ip-address"></a>単一の IP アドレスをブロックする

最初の IP 制限規則を追加するとき、サービスによって、2147483647 の優先度で明示的な "*すべて拒否*" 規則が追加されます。 実際には、明示的な "*すべて拒否*" 規則は、最後に実行される規則であり、"*許可*" 規則を使用して明示的に許可されていないすべての IP アドレスへのアクセスをブロックするものです。

単一の IP アドレスまたは IP アドレス ブロックを明示的にブロックし、その他すべてへのアクセスを許可するシナリオの場合は、明示的な "*すべて許可*" 規則を追加します。

![ブロックされた単一の IP アドレスを示す、Azure portal の [アクセス制限] ページのスクリーンショット。](media/app-service-ip-restrictions/block-single-address.png)

## <a name="restrict-access-to-an-scm-site"></a>SCM サイトへのアクセスを制限する 

アプリへのアクセスを制御できるだけでなく、アプリによって使用される SCM サイトへのアクセスを制限することもできます。 SCM サイトは Web デプロイ エンドポイントと Kudu コンソールの両方です。 SCM サイトとアプリに異なるアクセス制限を割り当てることも、アプリと SCM サイトの両方に同じ制限のセットを使うこともできます。 **[\<app name> と同じ制限]** チェック ボックスをオンにすると、すべてのものが空になります。このチェック ボックスをオフにすると、SCM サイトの設定が再適用されます。 

![SCM サイトまたはアプリに対して設定されているアクセス制限がないことを示す、Azure portal の [アクセス制限] ページのスクリーンショット。](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="manage-access-restriction-rules-programatically"></a>プログラムによるアクセス制限規則の管理

次のいずれかを実行して、アクセス制限をプログラムによって追加できます。 

* [Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest&preserve-view=true) を使用する。 次に例を示します。
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
  --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0&preserve-view=true) を使用します。 次に例を示します。


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```

次のいずれかを行うことにより、手動で値を設定することもできます。

* Azure Resource Manager のアプリ構成で [Azure REST API](/rest/api/azure/) PUT 操作を使用します。 Azure Resource Manager におけるこの情報の場所は次のとおりです。

  management.azure.com/subscriptions/ **<サブスクリプション ID>** /resourceGroups/ **<リソース グループ>** /providers/Microsoft.Web/sites/ **<Web アプリ名>** /config/web?api-version=2018-02-01

* ARM テンプレートを使用します。 たとえば、resources.azure.com を使用して ipSecurityRestrictions ブロックを編集して、必要な JSON を追加することができます。

  前の例の JSON 構文を次に示します。

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```

## <a name="set-up-azure-functions-access-restrictions"></a>Azure Functions のアクセス制限を設定する

アクセス制限は、関数アプリでも App Service プランと同じ機能を利用できます。 アクセス制限を有効にすると、許可されていない IP に対する Azure portal コード エディターが無効にもなります。

## <a name="next-steps"></a>次のステップ
[Azure Functions のアクセス制限](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[サービス エンドポイントと Application Gateway の統合](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
