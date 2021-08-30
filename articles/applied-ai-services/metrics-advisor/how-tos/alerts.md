---
title: Metrics Advisor アラートを構成する
titleSuffix: Azure Cognitive Services
description: 電子メール、Web、Azure DevOps のフックを使用して Metrics Advisor アラートを構成する方法。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: f63ea23cce0ab43dd5c9e0864619ff32d5dae50d
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342212"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>方法: フックを使用してアラートを構成し、通知を取得する

Metrics Advisor によって異常が検出されると、フックを使用し、アラート設定に基づいてアラート通知がトリガーされます。 アラート設定は、複数の検出構成で使用でき、さまざまなパラメーターを使用して警告ルールをカスタマイズできます。

## <a name="create-a-hook"></a>フックを作成する

Metrics Advisor では、email、Teams、webhook、Azure DevOps という 4 つの異なる種類のフックがサポートされています。 特定のシナリオに適したものを選択できます。      

### <a name="email-hook"></a>email hook

> [!Note]
> Metrics Advisor リソース管理者は、異常アラートを送信する前に、電子メールの設定を構成し、**SMTP 関連の情報** を Metrics Advisor に入力する必要があります。 リソース グループ管理者またはサブスクリプション管理者は、Metrics Advisor リソースの [アクセス制御] タブで、少なくとも 1 つの "*Cognitive Services Metrics Advisor 管理者*" ロールを割り当てる必要があります。 [電子メール設定の構成の詳細をご確認ください](../faq.yml#how-to-set-up-email-settings-and-enable-alerting-by-email-)。 


email hook は、 **[メールの送信先]** セクションで指定された電子メール アドレスに送信される異常アラートのチャネルです。 2 種類のアラート電子メールが送信されます。1 つは **アラートを使用できないデータ フィード** であり、もう 1 つは 1 つ以上の異常が含まれた **インシデント レポート** です。 

email hook を作成するには、次のパラメーターを使用できます。 

|パラメーター |説明  |
|---------|---------|
| 名前 | email hook の名前 |
| 電子メールの送信先| アラートの送信先の電子メールアドレス|
| 外部リンク | オプションのフィールド。トラブルシューティングに関する説明など、カスタマイズされたリダイレクトを有効にします。 |
| カスタマイズされた異常アラートのタイトル | タイトル テンプレートでは、`${severity}`、`${alertSettingName}`、`${datafeedName}`、`${metricName}`、`${detectConfigName}`、`${timestamp}`、`${topDimension}`、`${incidentCount}`、`${anomalyCount}` がサポートされています

**[OK]** をクリックすると、email hook が作成されます。 これを任意のアラート設定で使用すると、異常アラートを受け取ることができます。 詳細な手順については、[Metrics Advisor での異常通知の有効化](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)に関するチュートリアルを参照してください。

### <a name="teams-hook"></a>Teams フック

Teams フックとは、Microsoft Teams のチャネルに異常アラートが送信されるチャネルです。 Teams フックは、"受信 Webhook" コネクタによって実装されます。 ターゲット Teams チャネルで「受信 Webhook」コネクタを事前に作成し、その URL を取得する必要が生じる場合があります。 次に、Metrics Advisor ワークスペースに戻ります。 

左側のナビゲーション バーで [フック] タブを選択し、ページの上部右にある [フックの作成] ボタンを選択します。 「Teams」のフックの種類を選択します。次のパラメーターが用意されています。 

|パラメーター |説明  |
|---------|---------|
| 名前 | Teams フックの名前 | 
| コネクタの URL | ターゲット Teams チャネルで作成された「受信 webhook」コネクタからコピーした URL。 |

**[OK]** をクリックすると、Teams フックが作成されます。 任意のアラート設定でこのオプションを使用して、ターゲットの Teams チャネルに異常アラートを通知できます。 詳細な手順については、[Metrics Advisor での異常通知の有効化](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)に関するチュートリアルを参照してください。

### <a name="web-hook"></a>Web hook

Web hook は、顧客から提供されるエンドポイントを使用する別の通知チャネルです。 時系列で検出された異常は、Web hook を通じて通知されます。 Metrics Advisork 内のアラート通知チャネルとして Web hook を有効にするには、いくつかの手順を実行します。 

**手順1.**  Metrics Advisor リソースでマネージド ID を有効化する

システム割り当てマネージド ID は、1 つのリソースにつき 1 つに限定されており、このリソースのライフサイクルに関連付けられています。 マネージド ID には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してアクセス許可を付与できます。 マネージド ID は Azure AD で認証されるため、資格情報をコードに保存する必要はありません。 

Azure portal で Metrics Advisor リソースに移動し、[ID] を選択してオンに切り替えると、マネージド ID が有効になります。 

**手順 2.** Metrics Advisor ワークスペースで Web hook を作成する

ワークスペースにログインし、[フック] タブを選択してから、[フックの作成] ボタンを選択します。 


Web hook を作成するには、次の情報を追加する必要があります。

|パラメーター |説明  |
|---------|---------|
|エンドポイント     | アラートがトリガーされたときに呼び出される API アドレス。 **Https である必要があります**。       |
|ユーザー名/パスワード | API アドレスの認証用。 認証が不要な場合は、これを空白のままにします。         |
|ヘッダー     | API 呼び出しのカスタム ヘッダー。        |
|Azure Key vault の証明書識別子| エンドポイントへのアクセスが、証明書によって認証される必要がる場合、その証明書は Azure Key vault に格納されている必要があります。 ここに、識別子を入力します。

> [!Note]
> Web hook を作成または変更すると、**空の要求本文** を使用して、テストとしてエンドポイントが呼び出されます。 認証を正常に完了するには、API で 200 HTTP コードが返される必要があります。

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="Web hook の作成ウィンドウ。":::

- 要求メソッドは **POST**
- タイムアウト 30 秒
- 5xx エラーの場合は再試行し、他のエラーは無視します。 301/302 リダイレクト要求には従わないでください。
- 要求本文: 
```
{
"value": [{
    "hookId": "b0f27e91-28cf-4aa2-aa66-ac0275df14dd",
    "alertType": "Anomaly",
    "alertInfo": {
        "anomalyAlertingConfigurationId": "1bc6052e-9a2a-430b-9cbd-80cd07a78c64",
        "alertId": "172536dbc00",
        "timestamp": "2020-05-27T00:00:00Z",
        "createdTime": "2020-05-29T10:04:45.590Z",
        "modifiedTime": "2020-05-29T10:04:45.590Z"
    },
    "callBackUrl": "https://kensho2-api.azurewebsites.net/alert/anomaly/configurations/1bc6052e-9a2a-430b-9cbd-80cd07a78c64/alerts/172536dbc00/incidents"
}]
}
```

**手順3. (オプション)** Azure Key vault に証明書を格納し、前述のように識別子を取得します。エンドポイントへのアクセスが、証明書によって認証される必要がある場合、証明書は Azure Key vault に格納する必要があります。 

- 「[Azure portal を使用して Azure Key Vault から証明書の設定と取得を行う](../../../key-vault/certificates/quick-create-portal.md)」を確認してください
- 追加した証明書をクリックすると、「証明書識別子」をコピーできるようになります。 
- 次に、[アクセス ポリシー] と [アクセス ポリシーの追加] を選択し、「キーのアクセス許可」、「秘密のアクセス許可」、「証明書のアクセス許可」の「取得」アクセス許可を付与します。 Metrics Advisor リソースの名前としてプリンシパルを選択します。 [アクセス ポリシー] ページで [追加] ボタンと [保存] ボタンを選択します。 

**手順 4.** Web hook を介して通知がプッシュされると、異常の通知を受け取ります。Webhook 要求で「callBackUrl」を呼び出すことで、インシデント データをフェッチできます。 この API の詳細については、以下を参照してください。

-   [/alert/anomaly/configurations/{configurationId}/alerts/{alertId}/incidents](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/operations/getIncidentsFromAlertByAnomalyAlertingConfiguration)

Web hook と Azure Logic Apps を使用することで、**SMTP サーバーを構成しなくても** 電子メール通知を送信できます。 詳細な手順については、[Metrics Advisor での異常通知の有効化](../tutorials/enable-anomaly-notification.md#send-notifications-with-logic-apps-teams-and-smtp)に関するチュートリアルを参照してください。

### <a name="azure-devops"></a>Azure DevOps

Metrics Advisor では、異常が検出されたときに問題またはバグを追跡するために、Azure DevOps で作業項目を自動的に作成することもできます。 Azure DevOps hook を介してすべてのアラートを送信できます。

Azure DevOps hook を作成するには、次の情報を追加する必要があります

|パラメーター |説明  |
|---------|---------|
| 名前 | フックの名前 |
| 組織 | DevOps が属している組織 |
| Project | DevOps 内の特定のプロジェクト。 |
| Access Token |  DevOps の認証用トークン。 | 

> [!Note]
> Metrics Advisor で異常アラートに基づいて作業項目を作成する場合は、書き込みアクセス許可を付与する必要があります。 フックを作成した後、それらを任意のアラート設定で使用できます。 フックは、 **[hook settings]\(フックの設定\)** ページで管理します。 

## <a name="add-or-edit-alert-settings"></a>アラート設定の追加または編集

メトリックの詳細ページに移動し、そのページの左下隅にある **[アラート設定]** セクションを確認します。 ここには、選択した検出構成に適用されるすべてのアラート設定が一覧表示されます。 新しい検出構成を作成した時点では、アラート設定はなく、アラートは送信されません。  
アラート設定を変更するには、 **[追加]** 、 **[編集]** 、 **[削除]** アイコンを使用します。

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="[アラートの設定] メニュー項目。":::

**[追加]** または **[編集]** ボタンを選択して、アラート設定を追加または編集するウィンドウを表示します。

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="アラート設定の追加または編集":::

**アラート設定名**: アラート設定の名前。 これは、アラート電子メールのタイトルに表示されます。

**フック**: アラートの送信先となるフックの一覧。

上記のスクリーンショットでマークされているセクションは、1 つの検出構成の設定です。 さまざまな検出構成に対してさまざまなアラート設定を設定できます。 このウィンドウの 3 番目のドロップダウン リストを使用して、ターゲット構成を選択します。 

### <a name="filter-settings"></a>フィルターの設定 

1 つの検出構成のフィルター設定を次に示します。

**[アラート対象]** には、異常をフィルター処理するための 4 つのオプションがあります。

* **すべての系列の異常**: すべての異常がアラートに含まれます。         
* **系列グループの異常**: 系列をディメンション値でフィルター処理します。 いくつかのディメンションに特定の値を設定します。 系列が指定された値と一致する場合のみ、異常がアラートに含まれます。       
* **お気に入りの系列の異常**: お気に入りとしてマークされた系列のみがアラートに含まれます。        |
* **すべての系列の上位 N 個の異常**: このフィルターは、値が上位 N 個に含まれている系列のみに関心がある場合に使用します。Metrics Advisor は、以前のタイムスタンプを調べて、それらのタイムスタンプの系列の値が上位 N 個に含まれているかどうかを確認します。"上位 n" のカウントが指定された数より大きい場合、異常はアラートに含まれます。        |

**異常をフィルター処理するオプションは、追加のフィルターであり、次のオプションがあります。**

- **重大度**: 異常の重大度が指定された範囲内にある場合のみ、異常が含まれます。
- **再通知**: アラートでトリガーされたときに、次の N 個のポイント (期間) 内の異常に対してアラートを一時的に停止します。
    - **再通知の種類**: **[系列]** に設定すると、トリガーされた異常によってその系列のみが再通知されます。 **[メトリック]** の場合、1 つのトリガーされた異常によって、このメトリック内のすべての系列が再通知されます。
    - **再通知回数**: 再通知するポイント (期間) の数。
    - **連続しない場合にリセット**: 選択した場合、トリガーされた異常によって、次の n 個の連続した異常のみが再通知されます。 次のデータ ポイントのいずれかが異常でない場合、そのポイントから再通知がリセットされます。選択しない場合、連続するデータ ポイントが異常ではない場合でも、1 つのトリガーされた異常によって、次の n 個のポイント (期間) が再通知されます。
- **値** (オプション): 値でフィルター処理します。 条件を満たすポイント値のみ、異常が含まれます。 別のメトリックの対応する値を使用する場合、2 つのメトリックのディメンション名が一致している必要があります。

フィルターで除外されなかった異常は、アラートで送信されます。

### <a name="add-cross-metric-settings"></a>クロスメトリック設定を追加する

[アラート設定] ページで **[クロスメトリック設定を追加する]** を選択し、別のセクションを追加します。

**[演算子]** セレクターは、各セクションの論理的な関係で、アラートを送信するかどうかを決定します。


|演算子  |説明  |
|---------|---------|
|AND     | 系列が各アラート セクションと一致し、すべてのデータ ポイントが異常である場合にのみアラートを送信します。 メトリックのディメンション名が異なる場合、アラートはトリガーされません。         |
|OR     | 1 つ以上のセクションに異常がある場合にアラートを送信します。         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="複数のアラート設定セクションの演算子":::

## <a name="next-steps"></a>次のステップ

- [フィードバックを使用して異常検出を調整する](anomaly-feedback.md)
- [インシデントを診断する](diagnose-an-incident.md)
- [メトリックを構成して検出構成を微調整する](configure-metrics.md)
