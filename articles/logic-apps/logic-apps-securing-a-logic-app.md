---
title: データへのアクセスをセキュリティで保護する
description: Azure Logic Apps で、入力、出力、要求ベースのトリガー、実行履歴、管理タスク、その他のリソースへのアクセスを保護するためのセキュリティを追加します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 4f8c20534cdd5abdf5ae97bb097238cf508480c7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843550"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps におけるアクセスとデータのセキュリティ保護

Azure Logic Apps におけるアクセスを制御し、データを保護するために、次の領域のセキュリティを設定できます。

* [要求ベースのトリガーへのアクセス](#secure-triggers)
* [ロジック アプリの操作へのアクセス](#secure-operations)
* [実行履歴の入出力へのアクセス](#secure-run-history)
* [パラメーターの入力へのアクセス](#secure-action-parameters)
* [ロジック アプリから呼び出されたサービスとシステムへのアクセス](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>要求ベースのトリガーへのアクセス

着信した呼び出しや要求を受け取る要求ベースのトリガー ([Request](../connectors/connectors-native-reqres.md) トリガーや [Webhook](../connectors/connectors-native-webhook.md) トリガーなど) がロジック アプリで使用されている場合、承認されたクライアントだけがそのロジック アプリを呼び出すことができるように、アクセスを制限できます。 ロジック アプリで受信されるすべての要求は、Secure Sockets Layer (SSL) プロトコルで暗号化され、セキュリティ保護されます。

このトリガーの種類へのアクセスをセキュリティ保護する方法を次に示します。

* [Shared Access Signature の生成](#sas)
* [受信 IP アドレスの制限](#restrict-inbound-ip-addresses)
* [Azure Active Directory OAuth、またはその他のセキュリティの追加](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Shared Access Signature (SAS) の生成

ロジック アプリの各要求エンドポイントでは、エンドポイントの URL に次の形式で [Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) を含みます。

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

次の表で説明するように、各 URL にはクエリ パラメーター `sp`、`sv`、`sig` が含まれています。

| Query parameter (クエリ パラメーター) | [説明] |
|-----------------|-------------|
| `sp` | 使用が許可された HTTP メソッドのアクセス許可を指定します。 |
| `sv` | 署名の生成に使用する SAS バージョンを指定します。 |
| `sig` | トリガーへのアクセスの認証に使用する署名を指定します。 この署名は、SHA256 アルゴリズムとシークレット アクセス キーを使用してすべての URL パスとプロパティで生成されます。 このキーは決して公開されることはなく、暗号化された状態でロジック アプリと共に格納されます。 お客様のロジック アプリでは、秘密鍵を使用して作成された有効な署名を含むそれらのトリガーのみが承認されます。 |
|||

SAS によるアクセスのセキュリティ保護の詳細については、このトピック内の次の各セクションを参照してください。

* [アクセス キーを再生成する](#access-keys)
* [有効期限付きのコールバック URL を作成する](#expiring-urls)
* [プライマリまたはセカンダリのキーを使用して URL を作成する](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

新しい安全なアクセス キーを随時生成するには、Azure REST API または Azure portal を使用します。 古いキーを使用する、過去に生成された URL はすべて無効になり、ロジック アプリのトリガーが承認されなくなります。 再生成後に取得する URL は、新しいアクセス キーで署名されます。

1. [Azure portal](https://portal.azure.com) で、再生成したいキーを備えたロジック アプリを開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[アクセス キー]** を選択します。

1. 再生成したいキーを選択して、プロセスを完了します。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>有効期限付きのコールバック URL を作成する

要求ベースのトリガーのエンドポイント URL を他のパーティと共有する場合は、特定のキーを使用する有効期限付きのコールバック URL を生成できます。 そうすることで、キーをシームレスに交換したり、ロジック アプリのトリガーに対するアクセスを特定の期間に基づいて制限したりできます。 URL の有効期限を指定するには、[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) を使用します。その例を次に示します。

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

本文には、JSON 日付文字列を使用して `NotAfter` プロパティを含めます。 このプロパティは、`NotAfter` の日付と時刻までに限って有効なコールバック URL を返します。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>プライマリまたはセカンダリの秘密鍵を使用して URL を作成する

要求ベースのトリガーに対するコールバック URL を生成または一覧表示する場合、URL の署名に使用するキーを指定することができます。 特定のキーで署名された URL を生成するには、[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) を使用します。その例を次に示します。

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

本文では、`KeyType` プロパティに `Primary` または `Secondary` を指定します。 このプロパティは、指定された安全なキーによって署名された URL を返します。

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>受信 IP アドレスの制限

Shared Access Signature (SAS) と共に、ロジック アプリを呼び出すことができるクライアントを明確に制限したい場合があります。 たとえば、Azure API Management を使用して要求エンドポイントを管理する場合は、API Management インスタンスの IP アドレスからの要求のみを受け入れるようお使いのロジック アプリを制限できます。

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Azure portal で受信 IP 範囲を制限する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]**  >  **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[トリガーの IP 範囲]** で、トリガーで受け入れられる IP アドレス範囲を指定します。

   有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。

お使いのロジック アプリを、入れ子にされたロジック アプリとしてのみトリガーするようにしたい場合は、 **[許可された着信 IP アドレス]** の一覧で **[他のロジック アプリのみ]** を選択します。 このオプションにより、ロジック アプリのリソースに空の配列が書き込まれます。 そのため、入れ子にされたロジック アプリをトリガーできるのは、Logic Apps サービス (親ロジック アプリ) からの呼び出しだけになります。

> [!NOTE]
> IP アドレスに関わらず、Azure REST API または API Management を通じて `/triggers/<trigger-name>/run` を使用することで、要求ベースのトリガーを備えたロジック アプリを引き続き実行できます。 ただし、このシナリオでも Azure REST API に対する認証が必要です。 すべてのイベントが Azure の監査ログに表示されます。 アクセス制御ポリシーを適切に設定するようにしてください。

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートで受信 IP 範囲を制限する

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合、次の例のように、ロジック アプリのリソース定義の `accessControl` セクションで `triggers` セクションを使用して、IP 範囲を指定できます。

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory OAuth、またはその他のセキュリティの追加

ロジック アプリにさらに承認プロトコルを追加するには、[Azure API Management](../api-management/api-management-key-concepts.md) サービスの使用を検討してください。 このサービスでは、ロジック アプリを API として公開でき、あらゆるエンドポイント向けの監視、セキュリティ、ポリシー、ドキュメントが豊富に提供されています。 API Management では、ロジック アプリ用にパブリック エンドポイントまたはプライベート エンドポイントを公開できます。 このエンドポイントへのアクセスを承認するために、[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)、[クライアント証明書](#client-certificate-authentication)、またはその他のセキュリティ標準を使用できます。 API Management で要求が受け取られると、サービスによって要求がお客様のロジック アプリに送信されます。さらに、必要な変換または制限もその過程で行われます。 API Management のみがロジック アプリをトリガーできるようにするには、ロジック アプリの受信 IP 範囲の設定を使用できます。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>ロジック アプリの操作へのアクセス

ロジック アプリの管理、編集、表示など、特定のタスクの実行を特定のユーザーまたはグループのみに許可することができます。 それらのアクセス許可を制御するには、カスタマイズされたロールまたは組み込みロールを Azure サブスクリプションのメンバーに割り当てることができるように、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) を使用します。

* [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor): ロジック アプリを管理できますが、アクセス権を変更することはできません。

* [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator): ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

他のユーザーがお客様のロジック アプリを変更したり削除したりしないようにするには、[Azure のリソース ロック](../azure-resource-manager/management/lock-resources.md)を使用できます。 この機能を使用すると、他のユーザーは運用リソースを変更または削除できなくなります。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>実行履歴データへのアクセス

ロジック アプリが実行されている間、すべてのデータの[暗号化が転送中](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) (トランスポート層セキュリティ (TLS) を使用) と[保存時](../security/fundamentals/encryption-atrest.md)に行われます。 ロジック アプリの実行が完了したら、実行されたステップを含め、その実行の履歴を、各アクションの状態、期間、入力、出力と共に確認できます。 この豊富な詳細情報から、ロジック アプリがどのように実行されたか、発生した問題のトラブルシューティングをどこから始めるかに関する分析情報が得られます。

ロジック アプリの実行履歴を表示するときは、Logic Apps によってアクセスの認証が行われた後、各実行の要求と応答に対する入力および出力へのリンクが提供されます。 ただし、パスワード、シークレット、キーなどの秘匿性の高い情報を処理するアクションについては、他のユーザーがそのデータを表示したり利用したりできないようにします。 たとえば、ロジックアプリが HTTP アクションの認証時に使用する [Azure Key Vault](../key-vault/key-vault-overview.md) のシークレットを取得する場合、そのシークレットが見えないようにする必要があります。

ロジック アプリの実行履歴にある入力と出力へのアクセスを制御するには、次のオプションがあります。

* [IP アドレス範囲でアクセスを制限する](#restrict-ip)。

  このオプションでは、特定の IP アドレス範囲からの要求に基づいて実行履歴へのアクセスをセキュリティで保護できます。

* [難読化を使用して実行履歴のデータを非表示にする](#obfuscate)。

  多くのトリガーおよびアクションでは、ロジック アプリの実行履歴から、入力と出力のどちらかまたは両方を非表示にすることができます。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP アドレス範囲でアクセスを制限する

ロジック アプリの実行履歴に含まれる入力と出力へのアクセスは、特定の IP アドレス範囲からの要求のみでそのデータを表示できるように制限できます。 たとえば、すべてのユーザーが入力および出力にアクセスできないようにするには、IP アドレス範囲を「`0.0.0.0-0.0.0.0`」のように指定します。 この制限を削除できるのは管理者アクセス許可を持つ人物のみであるため、自分のロジック アプリのデータに対する "ジャストインタイム" アクセスが可能になります。 制限する IP 範囲を指定するには、Azure portal を使用するか、ロジック アプリのデプロイに使用する Azure Resource Manager テンプレートで実行することができます。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure portal で IP 範囲を制限する

1. Azure portal のロジック アプリ デザイナーでお客様のロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。

1. **[アクセス制御の構成]**  >  **[許可された着信 IP アドレス]** で、 **[特定の IP 範囲]** を選択します。

1. **[コンテンツの IP 範囲]** で、入力と出力からの内容にアクセスできる IP アドレス範囲を指定します。 

   有効な IP 範囲では、*x.x.x.x/x* または *x.x.x.x-x.x.x.x* の形式が使用されます。

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートで IP 範囲を制限する

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合、次の例のように、ロジック アプリのリソース定義の `accessControl` セクションで `contents` セクションを使用して、IP 範囲を指定できます。

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>難読化を使用して実行履歴のデータを非表示にする。

多くのトリガーおよびアクションには、ロジック アプリの実行履歴から、入力と出力のどちらかまたは両方を非表示にするための設定があります。 ここでは、それらの設定を使用してこのデータのセキュリティを保護する際に[確認すべきいくつかの考慮事項](#obfuscation-considerations)について取り上げます。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>デザイナーで入力と出力のセキュリティを保護する

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

   ![ロジック アプリ デザイナーでロジック アプリを開く](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. データをセキュリティで保護したいトリガーまたはアクションで省略記号 ( **...** ) ボタンを選択し、 **[設定]** を選択します。

   ![トリガーまたはアクションの設定を開く](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **[セキュリティで保護された入力]** と **[セキュリティで保護された出力]** のいずれかまたは両方をオンにします。 完了したら、 **[完了]** をクリックします。

   ![[セキュリティで保護された入力] または [セキュリティで保護された出力] をオンにする](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   アクションまたはトリガーのタイトル バーにロック アイコンが表示されます。

   ![アクションまたはトリガーのタイトル バーにロック アイコンが表示される](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   前のアクションからのセキュリティで保護された出力を表すトークンにも、ロック アイコンが表示されます。 たとえば、そのような出力をアクションで使用するために動的コンテンツ リストから選択すると、そのトークンにロック アイコンが表示されます。

   ![セキュリティで保護された出力用のトークンを選択する](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. ロジック アプリの実行後、その実行の履歴を表示できます。

   1. ロジック アプリの **[概要]** ウィンドウで、表示する実行を選択します。

   1. **[ロジック アプリの実行]** ウィンドウで、確認したいアクションを展開します。

      入力と出力の両方をセキュリティで保護することを選択した場合は、それらの値が非表示になります。

      ![実行履歴で非表示になっている入力と出力](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>コード ビューで入力と出力のセキュリティを保護する

基になるトリガーまたはアクションの定義で、次の値のいずれかまたは両方を含んだ `runtimeConfiguration.secureData.properties` 配列を追加または更新します。

* `"inputs"`:実行履歴における入力のセキュリティを保護します。
* `"outputs"`:実行履歴における出力のセキュリティを保護します。

ここでは、それらの設定を使用してこのデータのセキュリティを保護する際に[確認すべきいくつかの考慮事項](#obfuscation-considerations)について取り上げます。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>入力と出力を非表示にする際の考慮事項

* トリガーまたはアクションの入力または出力をセキュリティで保護すると、Logic Apps から Azure Log Analytics にそのセキュリティで保護されたデータが送信されません。 また、そのトリガーまたはアクションに[追跡対象プロパティ](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)を追加して監視することもできません。

* セキュリティで保護された出力は、[ワークフロー履歴を処理するための Logic Apps API](https://docs.microsoft.com/rest/api/logic/) から返されません。

* 入力をセキュリティで保護するアクション、またはセキュリティで保護された出力を明示的に使用するアクションから、出力をセキュリティで保護するには、そのアクションで **[セキュリティで保護された出力]** を手動で有効にします。

* ダウンストリームのアクションで、実行履歴にそのデータのセキュリティ保護を求める場合は、 **[セキュリティで保護された入力]** または **[セキュリティで保護された出力]** を確実にオンにしてください。

  **[セキュリティで保護された出力] の設定**

  トリガーまたはアクションで **[セキュリティで保護された出力]** を手動でオンにすると、実行履歴において、その出力がセキュリティで保護されます。 それらのセキュリティで保護された出力がダウンストリームのアクションで明示的に入力として使用されている場合、そのアクションの入力が実行履歴では非表示となりますが、アクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。

  ![入力としてのセキュリティで保護された出力とダウンストリームによるほとんどのアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  [作成]、[JSON の解析]、[応答] の各アクションには、 **[セキュリティで保護された入力]** の設定しかありません。 この設定をオンにした場合、それらのアクションの出力も非表示になります。 セキュリティで保護されたアップストリームの出力がそれらのアクションの入力として明示的に使用された場合、Logic Apps によってそれらのアクションの入力と出力は非表示となりますが、それらのアクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。 [作成]、[JSON の解析]、[応答] のいずれかのアクションからの非表示の出力がダウンストリームのアクションで明示的に入力として使用された場合、"*そのダウンストリームのアクションの入力と出力は非表示になりません*"。

  ![入力としてのセキュリティで保護された出力とダウンストリームによる特定のアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **[セキュリティで保護された入力] の設定**

  トリガーまたはアクションで **[セキュリティで保護された入力]** を手動でオンにすると、Logic Apps により、実行履歴でその入力がセキュリティで保護されます。 そのトリガーまたはアクションからの表示可能な出力がダウンストリームのアクションの入力として明示的に使用されている場合、そのダウンストリームのアクションの入力が実行履歴では非表示となりますが、このアクションの **[セキュリティで保護された入力]** は "*有効にならず*"、そのアクションの出力が非表示になることもありません。

  ![セキュリティで保護された入力とダウンストリームのほとんどのアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  セキュリティで保護された入力を持つトリガーまたはアクションからの表示可能な出力が、[作成]、[JSON の解析]、[応答] の各アクションで明示的に使用された場合、これらのアクションの入力と出力は非表示となりますが、そのアクションの **[セキュリティで保護された入力]** の設定は "*有効になりません*"。 [作成]、[JSON の解析]、[応答] のいずれかのアクションからの非表示の出力がダウンストリームのアクションで明示的に入力として使用された場合、"*そのダウンストリームのアクションの入力と出力は非表示になりません*"。

  ![セキュリティで保護された入力とダウンストリームの特定のアクションへの影響](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>パラメーターの入力へのアクセス

デプロイ先が複数の異なる環境にまたがる場合、環境に応じて変わる値は、ワークフロー定義内でパラメーター化することを検討してください。 そのようにすると、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用してロジック アプリをデプロイすることでデータのハードコーディングを避け、セキュリティで保護されたパラメーターを定義することで機密データを保護し、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用することで[テンプレートのパラメーター](../azure-resource-manager/templates/template-parameters.md)で個別の入力としてそのデータを渡すことができます。

たとえば、[Azure Active Directory OAuth](#azure-active-directory-oauth-authentication) で HTTP アクションの認証を行う場合、認証に使用されるクライアント ID とクライアント シークレットを受け入れるパラメーターを定義し、セキュリティで保護することができます。 ロジック アプリでこれらのパラメーターを定義するには、ロジック アプリのワークフロー定義内の `parameters` セクションと、デプロイ用の Resource Manager テンプレートを使用します。 ロジック アプリの編集時や実行履歴の確認時に表示させたくないパラメーター値を非表示にするには、`securestring` または `secureobject` 型を使用してパラメーターを定義し、必要に応じてエンコードを使用します。 この型のパラメーターはリソース定義と一緒に返されず、デプロイ後にリソースを表示するときにもアクセスできません。 それらのパラメーターの値に実行時にアクセスするには、ワークフロー定義内で `@parameters('<parameter-name>')` 式を使用します。 この式は実行時にのみ評価され、[ワークフロー定義言語](../logic-apps/logic-apps-workflow-definition-language.md)で記述されます。

> [!NOTE]
> 要求のヘッダーまたは本文でパラメーターを使用した場合、ロジック アプリの実行履歴や送信 HTTP 要求を表示したときに、そのパラメーターが表示されることがあります。 コンテンツ アクセス ポリシーも適切に設定するようにしてください。 [難読化](#obfuscate)を使用して、実行履歴の入力と出力を表示されないようにすることもできます。 Authorization ヘッダーは入力や出力では表示されません。 そのため、ここでシークレットが使用された場合はそのシークレットを取得できません。

詳細については、このトピックの以下のセクションをご覧ください。

* [ワークフロー定義内のパラメーターをセキュリティで保護する](#secure-parameters-workflow)
* [難読化を使用して実行履歴のデータを非表示にする](#obfuscate)

[Resource Manager テンプレートを使用してロジック アプリのデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)場合は、デプロイ時に評価される、セキュリティで保護された[テンプレート パラメーター](../azure-resource-manager/templates/template-parameters.md)を、`securestring` 型と `secureobject` 型を使用して定義できます。 テンプレート パラメーターを定義するには、テンプレートの最上位の `parameters` セクションを使用します。このセクションは独立しており、ワークフロー定義の `parameters` セクションとは異なります。 テンプレートのパラメーターに値を指定するには、別途[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用します。

たとえばシークレットを使用する場合、それらのシークレットをデプロイ時に [Azure Key Vault](../key-vault/key-vault-overview.md) から取得する、セキュリティで保護されたテンプレート パラメーターを定義して使用することができます。 その後、パラメーター ファイルの中で、キー コンテナーとシークレットを参照することができます。 詳細については、以下のトピックを参照してください。

* [デプロイ時に Azure Key Vault を使用して機密性の値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)
* このトピックの後にある [Azure Resource Manager テンプレート内のパラメーターをセキュリティで保護する](#secure-parameters-deployment-template) (このトピックで後出)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>ワークフロー定義内のパラメーターをセキュリティで保護する

ロジック アプリのワークフロー定義内の秘匿性の高い情報を保護するには、その情報がロジック アプリの保存後に表示されないよう、セキュリティで保護されたパラメーターを使用します。 たとえば、基本認証を必要とする HTTP アクションがあって、ユーザー名とパスワードが使用されているとします。 ワークフロー定義の `parameters` セクションでは、`securestring` 型を使用して `basicAuthPasswordParam` パラメーターと `basicAuthUsernameParam` パラメーターを定義します。 そのうえで、これらのパラメーターをアクション定義の `authentication` セクションで参照します。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのパラメーターをセキュリティで保護する

ロジック アプリ用の [Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)には、複数の `parameters` セクションが存在します。 パスワード、キー、シークレットなどの秘匿性の高い情報を保護するには、テンプレート レベルおよびワークフロー定義レベルで、`securestring` または `secureobject` 型を使用して、セキュリティで保護されたパラメーターを定義します。 その後、これらの値を [Azure Key Vault](../key-vault/key-vault-overview.md) に格納すれば、[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)を使用して Key Vault とシークレットを参照することができます。 その後、デプロイ時にご利用のテンプレートからその情報を取得します。 詳しくは、[デプロイ時に Azure Key Vault を使用して機密性の値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)方法に関する記事をご覧ください。

次に、これらの `parameters` セクションについて詳しく説明します。

* テンプレートの最上位の `parameters` セクションには、そのテンプレートが "*デプロイ*" 時に使用する値のパラメーターを定義します。 たとえば、特定のデプロイ環境の接続文字列がそのような値として考えられます。 これらの値は、独立した[パラメーター ファイル](../azure-resource-manager/templates/parameter-files.md)に格納できるので、値の変更も容易に行うことができます。

* ロジック アプリのリソース定義内、かつワークフロー定義の外側にある `parameters` セクションでは、ワークフロー定義のパラメーターの値を指定します。 このセクションでは、テンプレートのパラメーターを参照するテンプレート式を使用して、それらの値を割り当てることができます。 これらの式は、デプロイ時に評価されます。

* ワークフロー定義内の `parameters` セクションでは、ロジック アプリによって実行時に使用されるパラメーターを定義します。 これらのパラメーターは、ロジック アプリのワークフロー内から、実行時に評価されるワークフロー定義式を使用して参照できます。

この例のテンプレートには、`securestring` 型を使用する、セキュリティで保護されたパラメーターの定義が複数存在します。

| パラメーター名 | [説明] |
|----------------|-------------|
| `TemplatePasswordParam` | パスワードを受け取るテンプレート パラメーター。パスワードはその後、ワークフロー定義の `basicAuthPasswordParam` パラメーターに渡されます。 |
| `TemplateUsernameParam` | ユーザー名を受け取るテンプレート パラメーター。ユーザー名はその後、ワークフロー定義の `basicAuthUserNameParam` パラメーターに渡されます。 |
| `basicAuthPasswordParam` | HTTP アクションで基本認証用のパスワードを受け取るワークフロー定義パラメーター |
| `basicAuthUserNameParam` | HTTP アクションで基本認証用のユーザー名を受け取るワークフロー定義パラメーター |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>ロジック アプリから呼び出されたサービスとシステムへのアクセス

ロジック アプリから呼び出しまたは要求を受信するエンドポイントをセキュリティで保護するいくつかの方法を次に示します。

* 送信要求に認証を追加します。

  HTTP、HTTP + Swagger、Webhook など、送信呼び出しを行う HTTP ベースのトリガーまたはアクションを使用するときは、ロジック アプリによって送信される要求に認証を追加できます。 たとえば、次の認証の種類を使用できます。

  * [[基本認証]](#basic-authentication)

  * [クライアント証明書認証](#client-certificate-authentication)

  * [Active Directory OAuth 認証](#azure-active-directory-oauth-authentication)

  * [マネージド ID の認証](#managed-identity-authentication)
  
  詳しくは、後の「[送信呼び出しに認証を追加する](#add-authentication-outbound)」をご覧ください。

* ロジック アプリの IP アドレスからのアクセスを制限する。

  ロジック アプリからエンドポイントへの呼び出しはすべて、ロジック アプリのリージョンに基づいて指定される特定の IP アドレスが起点となります。 これらの IP アドレスからのみ要求を受け入れるフィルターを追加できます。 これらの IP アドレスを取得するには、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md#configuration)に関するページを参照してください。

* オンプレミス システムへの接続をセキュリティで保護する。

  Azure Logic Apps では、以下のサービスとの統合を行って、安全で信頼性の高いオンプレミス通信を実現できます。

  * オンプレミスのデータ ゲートウェイ

    Azure Logic Apps の多くのマネージド コネクタは、オンプレミス システム (ファイル システム、SQL、SharePoint、DB2 など) への安全な接続を提供します。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを送信します。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 [オンプレミス データ ゲートウェイのしくみ](logic-apps-gateway-install.md#gateway-cloud-service)を確認してください。

  * Azure API Management での接続

    [Azure API Management](../api-management/api-management-key-concepts.md) には、オンプレミス システムへのプロキシと通信のセキュリティ保護を実現するためのサイト間仮想プライベート ネットワークと ExpressRoute の統合など、オンプレミス接続オプションが用意されています。 オンプレミスのシステムには、API Management が公開している API を介してすばやくアクセスできます。ロジック アプリ デザイナーで、ロジック アプリのワークフローからこの API を選択できます。

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>送信呼び出しに認証を追加する

HTTP および HTTPS エンドポイントでは、さまざまな種類の認証がサポートされています。 これらのエンドポイントにアクセスする送信呼び出しまたは送信要求を行うために使用するトリガーまたはアクションに基づいて、さまざまな認証の種類から選択できます。 ロジック アプリで処理される機密情報を確実に保護するには、セキュリティで保護されたパラメーターを使用し、必要に応じてデータをエンコードします。 パラメーターの使用とセキュリティ保護の詳細については、「[パラメーターの入力へのアクセス](#secure-action-parameters)」を参照してください。

| 認証の種類 | サポートしているもの |
|---------------------|--------------|
| [Basic](#basic-authentication) | Azure API Management、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [クライアント証明書](#client-certificate-authentication) | Azure API Management、Azure App Service、HTTP、HTTP + Swagger、HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [Raw](#raw-authentication) | Azure API Management、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
| [マネージド ID](#managed-identity-authentication) (システム割り当てのみ) | Azure API Management、Azure App Service、Azure Functions、HTTP、HTTP + Swagger、HTTP Webhook |
|||

> [!NOTE]
> ロジック アプリ デザイナーでは、認証の種類を指定できる一部のトリガーやアクションで、 **[認証]** プロパティが表示されていない場合があります。 このような場合にプロパティを表示するには、トリガーまたはアクションで **[新しいパラメーターの追加]** の一覧を開き、 **[認証]** を選択します。 詳細については、「[マネージド ID を使用してアクセスを認証する](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)」を参照してください。

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>[基本認証]

[[基本]](../active-directory-b2c/secure-rest-api-dotnet-basic-auth.md) オプションを使用できる場合は、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | Required | Value | [説明] |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | Basic | 使用する認証の種類 |
| **ユーザー名** | `username` | はい | <*user-name*>| ターゲット サービス エンドポイントへのアクセスを認証するためのユーザー名 |
| **パスワード** | `password` | はい | <*password*> | ターゲット サービス エンドポイントへのアクセスを認証するためのパスワード |
||||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `Basic` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>クライアント証明書認証

[[クライアント証明書]](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) オプションを使用できる場合は、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | Required | Value | [説明] |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | **クライアント証明書** <br>or <br>`ClientCertificate` | Secure Sockets Layer (SSL) クライアント証明書に使用する認証の種類。 自己署名証明書はサポートされていますが、SSL 用の自己署名証明書はサポートされていません。 |
| **Pfx** | `pfx` | はい | <*encoded-pfx-file-content*> | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ <p><p>PFX ファイルを Base64 でエンコードされた形式に変換するには、次の手順に従って PowerShell を使用します。 <p>1.証明書の内容を変数に保存します。 <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2.`ToBase64String()` 関数を使用して証明書の内容を変換し、その内容をテキスト ファイルに保存します。 <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **パスワード** | `password`| 説明を参照してください | <*password-for-pfx-file*> | PFX ファイルにアクセスするためのパスワード。 <p><p>**注**:このプロパティ値は、ロジック アプリ デザイナーで作業するときに必要ですが、コードビューで作業するときは必要*ありません*。 |
|||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ClientCertificate` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

クライアント証明書認証を使用してサービスをセキュリティ保護する方法の詳細については、次のトピックを参照してください。

* [Azure API Management でクライアント証明書認証を使用して API を保護する](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する](../api-management/api-management-howto-mutual-certificates.md)
* [クライアント証明書を使用して RESTful サービスを保護する](../active-directory-b2c/secure-rest-api-dotnet-certificate-auth.md)
* [アプリケーションを認証するための証明書資格情報](../active-directory/develop/active-directory-certificate-credentials.md)
* [Azure App Service のアプリケーション コードに SSL 証明書を使用する](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory OAuth 認証

[[Active Directory OAuth]](../active-directory/develop/about-microsoft-identity-platform.md) オプションを使用できる場合は、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | Required | Value | [説明] |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | **Active Directory OAuth** <br>or <br>`ActiveDirectoryOAuth` | 使用する認証の種類。 現在、Logic Apps では [OAuth 2.0 プロトコル](../active-directory/develop/v2-overview.md)が使用されています。 |
| **テナント** | `tenant` | はい | <*tenant-ID*> | Azure AD テナントのテナント ID |
| **対象ユーザー** | `audience` | はい | <*resource-to-authorize*> | 承認で使用するリソース (`https://management.core.windows.net/` など) |
| **クライアント ID** | `clientId` | はい | <*client-ID*> | 承認を要求しているアプリのクライアント ID |
| **資格情報の種類** | `credentialType` | はい | Certificate <br>or <br>Secret | 承認を要求するためにクライアントで使用される資格情報の種類。 このプロパティと値はロジック アプリの基になっている定義には出現しませんが、選択した資格情報の種類に対して表示されるプロパティがそれによって決まります。 |
| **シークレット** | `secret` | はい (ただし資格情報の種類が "Secret" の場合のみ) | <*client-secret*> | 承認を要求しているクライアント シークレット |
| **Pfx** | `pfx` | はい (ただし資格情報の種類が "Certificate" の場合のみ) | <*encoded-pfx-file-content*> | Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| **パスワード** | `password` | はい (ただし資格情報の種類が "Certificate" の場合のみ) | <*password-for-pfx-file*> | PFX ファイルにアクセスするためのパスワード |
| **機関** | `authority` | いいえ | <*URL-for-authority-token-issuer*> | 認証トークンを提供する機関の URL。 この値の既定値は `https://login.windows.net` です。 <p>**注**:このプロパティがデザイナーに表示されるようにするには、トリガーまたはアクションで **[新しいパラメーターの追加]** の一覧を開き、 **[機関]** を選択します。 |
|||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ActiveDirectoryOAuth`、資格情報の種類として `Secret` が指定されており、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>Raw 認証

**[未加工]** オプションが使用可能な場合は、[OAuth 2.0 プロトコル](https://oauth.net/2/)に従っていない[認証スキーム](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)を使用する必要があるときに、この認証の種類を使用できます。 この種類では、送信要求で送信する Authorization ヘッダーの値を手動で作成し、トリガーまたはアクションでそのヘッダー値を指定します。

たとえば、[OAuth 1.0 プロトコル](https://tools.ietf.org/html/rfc5849)に従う HTTPS 要求のヘッダーの例を次に示します。

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Raw 認証をサポートするトリガーまたはアクションでは、次のプロパティ値を指定します。

| プロパティ (デザイナー) | プロパティ (JSON) | Required | Value | [説明] |
|---------------------|-----------------|----------|-------|-------------|
| **認証** | `type` | はい | Raw | 使用する認証の種類 |
| **Value** | `value` | はい | <*authorization-header-value*> | 認証に使用する Authorization ヘッダーの値 |
||||||

たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `Raw` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>マネージド ID の認証

[[マネージド ID]](../active-directory/managed-identities-azure-resources/overview.md) オプションが使用可能な場合、ロジック アプリでは、サインインせずに他の Azure Active Directory (Azure AD) テナントのリソースにアクセスするための認証用に、システム割り当ての ID を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 [Azure AD 認証用のマネージド ID がサポートされているサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)の詳細をご覧ください。

1. ロジック アプリでシステム割り当ての ID を使用するには、その前に「[Azure Logic Apps でマネージド ID を使用して認証し、リソースにアクセスする](../logic-apps/create-managed-service-identity.md)」の手順に従います。 これらの手順により、ロジック アプリでマネージド ID が有効になり、ターゲットの Azure リソースに対するその ID のアクセスが設定されます。

2. Azure 関数でシステム割り当ての ID を使用できるようにするには、先に [Azure Functions の認証を有効](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)にします。

3. マネージド ID を使用するトリガーまたはアクションで、次のプロパティ値を指定します。

   | プロパティ (デザイナー) | プロパティ (JSON) | Required | Value | [説明] |
   |---------------------|-----------------|----------|-------|-------------|
   | **認証** | `type` | はい | **Managed Identity** <br>or <br>`ManagedServiceIdentity` | 使用する認証の種類 |
   | **対象ユーザー** | `audience` | はい | <*target-resource-ID*> | アクセスするターゲット リソースのリソース ID。 <p>たとえば、`https://storage.azure.com/` では、認証用のアクセス トークンがすべてのストレージ アカウントに対して有効になります。 ただし、特定のストレージ アカウントに対する `https://fabrikamstorageaccount.blob.core.windows.net` など、ルート サービスの URL を指定することもできます。 <p>**注**:このプロパティは、一部のトリガーまたはアクションでは表示されない可能性があります。 このプロパティが表示されるようにするには、トリガーまたはアクションで **[新しいパラメーターの追加]** の一覧を開き、 **[対象ユーザー]** を選択します。 <p><p>**重要**:このターゲット リソース ID が、必要な末尾のスラッシュも含めて、Azure AD で予想される値と正確に一致するようにします。 そのため、すべての Azure Blob Storage アカウントの `https://storage.azure.com/` リソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 これらのリソース ID を調べるには、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。 |
   |||||

   たとえば、[デプロイを自動化するための Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)で、[セキュリティ保護されたパラメーター](#secure-action-parameters)を使用して機密情報を処理および保護する場合は、式を使用して実行時にこれらのパラメーター値にアクセスできます。 次の例の HTTP アクション定義では、認証の `type` として `ManagedServiceIdentity` が指定され、パラメーター値を取得するために [parameters() 関数](../logic-apps/workflow-definition-language-functions-reference.md#parameters)が使用されています。

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のデプロイを自動化する](logic-apps-azure-resource-manager-templates-overview.md)  
* [ロジック アプリを監視する](logic-apps-monitor-your-logic-apps.md)  
* [ロジック アプリの障害と問題の診断](logic-apps-diagnosing-failures.md)  
* [ロジック アプリ デプロイを自動化する](logic-apps-azure-resource-manager-templates-overview.md)
