---
title: Azure Front Door に対する Web アプリケーション ファイアウォール規則で IP 制限規則を構成する
description: 既存の Front Door エンドポイントに対する IP アドレス制限 WAF 規則の構成方法について説明します。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: b129579916330a34a2a78d98f2c7653f129d3319
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523705"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Azure Front Door に対する Web アプリケーション ファイアウォールで IP 制限規則を構成する (プレビュー)
 この記事では、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使って、Front Door 向けの Azure Web アプリケーション ファイアウォール (WAF) で IP 制限規則を構成する方法を示します。

IP アドレス ベースのアクセス制御ルールはカスタム WAF 規則であり、クラスレス ドメイン間ルーティング (CIDR) 形式で IP アドレスのリストまたは IP アドレスの範囲を指定することにより、Web アプリケーションへのアクセスを制御できます。

既定では、Web アプリケーションにはインターネットからアクセスできます。 Web アプリケーションへのアクセスを既知の IP アドレスのリストまたは IP アドレスの範囲からのクライアントのみに制限したい場合は、2 つの IP 一致規則を作成する必要があります。 1 番目の IP 一致規則には一致する値として IP アドレスのリストが含まれ、"許可する" アクションを設定します。 2 番目の優先度が低いものは、"All" 演算子を使って他のすべての IP アドレスをブロックし、"ブロック" するアクションを設定します。 IP 制限規則が適用されると、この許可リストに含まれないアドレスからのすべての要求は、403 (禁止) 応答を受け取ります。  

> [!IMPORTANT]
> 現在、Azure Front Door に対する WAF IP 制限の機能は、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="configure-waf-policy-with-azure-cli"></a>Azure CLI で WAF ポリシーを構成する

### <a name="prerequisites"></a>前提条件
IP 制限ポリシーの構成を始める前に、ご自分の CLI 環境をセットアップして、Front Door プロファイルを作成します。

#### <a name="set-up-azure-cli-environment"></a>Azure CLI 環境をセットアップする
1. [Azure CLI](/cli/azure/install-azure-cli) をインストールするか、または Azure Cloud Shell を使用します。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 これには Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 次の CLI コマンドの **[使ってみる]** ボタンを選択します。 **[使ってみる]** を選択すると Cloud Shell が起動され、お使いの Azure アカウントにサインインできます。 Cloud Shell セッションが開始したら、「`az extension add --name front-door`」と入力して Front Door 拡張機能を追加します。
 2. Bash でローカルに CLI を使用している場合は、`az login` を使って Azure にサインインします。

#### <a name="create-front-door-profile"></a>Front Door プロファイルを作成する
Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います

### <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

[az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) コマンドで WAF ポリシーを作成します。 次の例で、ポリシー名 *IPAllowPolicyExampleCLI* を一意のポリシー名に置き換えます。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>カスタム IP アクセス制御ルールを追加する

前のステップで作成した WAF ポリシーに、[az network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) コマンドでカスタム IP アクセス制御規則を追加します。 

次の例で:
-  *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。
-  *ip-address-range-1*、*ip-address-range-2* を、独自の範囲に置き換えます。

最初に、指定したアドレスに対する IP 許可規則を作成します。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
次に、前の IP 許可規則より優先度の低い、すべての IP をブロックする規則を作成します。 *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>WAF ポリシー ID を見つける
[az network waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) コマンドで WAF ポリシーの ID を検索します。 *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Front Door フロントエンド ホストに WAF ポリシーをリンクする

[az network front-door update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) コマンドを使用して、フロントドアの *WebApplicationFirewallPolicyLink* ID にポリシー ID を設定します。 *IPAllowPolicyExampleCLI* を、前に作成した一意のポリシーに置き換えます。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
この例では、WAF ポリシーが FrontendEndpoints[0] に適用されます。 ご自分の任意のフロントエンドに WAF ポリシーをリンクできます。
> [!Note]
> Front Door フロントエンドに WAF ポリシーをリンクするために **WebApplicationFirewallPolicyLink** プロパティを設定する必要があるのは 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。

## <a name="configure-waf-policy-with-azure-powershell"></a>Azure PowerShell を使用して WAF ポリシーを構成する

### <a name="prerequisites"></a>前提条件
IP 制限ポリシーの構成を始める前に、ご自分の PowerShell 環境をセットアップして、Front Door プロファイルを作成します。

#### <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する
```
Connect-AzAccount

```
Front Door モジュールをインストールする前に、現在のバージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door プロファイルを作成する
Front Door プロファイルを作成するには、[Front Door プロファイルの作成に関するクイック スタート](quickstart-create-front-door.md)で説明されている手順に従います

### <a name="define-ip-match-condition"></a>IP の一致条件を定義する
IP の一致条件を定義するには、[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) コマンドを使います。 次の例では、*ip-address-range-1* と *ip-address-range-2* を独自の範囲に置き換えます。

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
IP のすべて一致条件規則を作成します
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>カスタム IP 許可規則を作成する
   アクションを定義して優先順位を設定するには、[New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) コマンドを使用します。 次の例では、リストに一致するクライアント IP からの要求が許可されます。 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
前の IP 許可規則より優先度の低い、すべての IP をブロックする規則を作成します。

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>WAF ポリシーを構成する
`Get-AzResourceGroup` を使用して、Front Door プロファイルが含まれているリソース グループの名前を見つけます。 次に、[New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用して、IP ブロック規則で WAF ポリシーを構成します。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Front Door フロントエンド ホストに WAF ポリシーをリンクする

WAF ポリシー オブジェクトを既存の Front Door フロントエンド ホストにリンクして、Front Door のプロパティを更新します。 最初に、[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) を使用して Front Door オブジェクトを取得します。 次に、[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) コマンドを使用して、フロントエンドの *WebApplicationFirewallPolicyLink* プロパティを、前のステップで作成した *$IPAllowPolicyExamplePS* の resourceId に設定します。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> この例では、WAF ポリシーが FrontendEndpoints[0] に適用されます。 自分の任意のフロントエンドに WAF ポリシーをリンクできます。Front Door フロントエンドに WAF ポリシーをリンクさせるために必要な *WebApplicationFirewallPolicyLink* プロパティの設定は 1 回だけです。 それ以降のポリシーの更新は、自動的にフロントエンドに適用されます。


## <a name="configure-waf-policy-with-resource-manager-template"></a>Resource Manager テンプレートを使用して WAF ポリシーを構成する
カスタム IP 制限規則を使用して Front Door と WAF ポリシーを作成するテンプレートについては、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)をご覧ください。


## <a name="next-steps"></a>次の手順

- [Front Door プロファイルを作成する](quickstart-create-front-door.md)方法について説明します。
