---
title: Azure Event Grid でキー コンテナー通知を受信して応答する
description: Key Vault と Azure Event Grid を統合する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464094"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>方法:Azure Event Grid でキー コンテナー通知を受信して応答する (プレビュー)

現在プレビュー段階の Key Vault と Azure Event Grid の統合により、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知することができます。 この機能の概要については、[Azure Event Grid での Key Vault の監視](event-grid-overview.md)に関するページをご覧ください。

このガイドでは、Azure Event Grid で Key Vault の通知を受信する方法、および Azure Automation で状態の変更に応答する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- ご自身の Azure サブスクリプションのキー コンテナー。 新しいキー コンテナーをすばやく作成するには、[Azure CLI を使用した Azure Key Vault からのシークレットの設定と取得](quick-create-cli.md)に関するページの手順に従います

## <a name="concepts"></a>概念

Azure Event Grid は、クラウドのイベント処理サービスです。 このガイドでは、キー コンテナーのイベントをサブスクライブして、イベントを Azure Automation にルーティングします。 キー コンテナー内でいずれかのシークレットの有効期限がまもなく切れる場合、状態の変更が Event Grid に通知されます。また、エンドポイントへの HTTP POST が実行されます。 その後、Web hook によって PowerShell スクリプトの Azure Automation 実行がトリガーされます。

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

[Azure portal](https://portal.azure.com) で、Azure Automation アカウントを作成します。

1.  portal.azure.com にアクセスして、ご自身のサブスクリプションにログインします。

1.  検索ボックスに、「Automation Accounts」と入力します。

1.  検索バーのドロップダウンの [サービス] セクションで、[Automation アカウント] を選択します。

1.  [追加] をクリックします。

    ![](media/image2.png)

1.  [Automation アカウントの追加] ブレードに必要な情報を入力し、[作成] を選択します。

## <a name="create-a-runbook"></a>Runbook を作成する

ご自身の Azure Automation アカウントの準備ができたら、Runbook を作成します。

![](media/image3.png)

1.  作成したばかりの Automation アカウントを選択します。

1.  [プロセス オートメーション] セクションで [Runbook] を選択します。

1.  [Runbook の作成] をクリックします。

1.  ご自身の Runbook に名前を付けて、Runbook の種類として [PowerShell] を選択します。

1.  作成した Runbook をクリックし、[編集] を選択します。

1.  (テストの目的で) 次のコードを入力し、[発行] をクリックします。 受信した POST 要求の結果が出力されます。

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>webhook を作成する

ここで、新しく作成された Runbook をトリガーする Webhook を作成します。

1.  発行したばかりの Runbook のリソース セクションで [Webhook] を選択します。

1.  [Webhook の追加] をクリックします。

    ![](media/image5.png)

1.  [新しい Webhook を作成します] を選択します。

1. Webhook に名前を付けて、有効期限を設定し、**URL をコピー**します。

    > [!IMPORTANT] 
    > Webhook の作成後に URL を表示することはできません。 このガイドの残りの部分で使用できるように、アクセスできる安全な場所にコピーを保管しておいてください。

1. [パラメーターと実行設定] をクリックし、[OK] を選択します。 パラメーターは入力しないでください。 これにより [作成] ボタンが有効になります。

1. [OK]、[作成] の順に選択します。

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

[Azure portal](https://portal.azure.com) を使用して Event Grid サブスクリプションを作成します。

1.  次のリンクを使用して、Azure portal を開きます: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  ご自身のキー コンテナーにアクセスし、[イベント] タブを選択します。[イベント] タブが表示されない場合は、[ポータルのプレビュー バージョン](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true)を使用していることを確認します。

    ![](media/image7.png)

1.  [+ イベント サブスクリプション] をクリックします。

1.  サブスクリプションのわかりやすい名前を作成します。

1.  [イベント グリッド スキーマ] を選択します。

1.  [トピックのリソース] は、状態の変化を監視するキー コンテナーにします。

1.  [イベントの種類のフィルター] については、すべてオンのままにします ("9 個が選択済み")。

1.  [エンドポイントの種類] については、[Webhook] を選択します。

1.  [エンドポイントの選択] を選択します。 新しいコンテキスト ウィンドウの [サブスクライバー エンドポイント] フィールドに、「[Webhook を作成する](#create-a-webhook)」手順でコピーした Webhook URL を貼り付けます。

1.  コンテキスト ウィンドウで [選択内容の確認] を選択します。

1.  [作成] を選択します。

    ![](media/image8.png)

## <a name="test-and-verify"></a>テストして検証する

Event Grid サブスクリプションが適切に構成されていることを確認します。  このテストは、「[Event Grid のサブスクリプションを作成する](#create-an-event-grid-subscription)」で "シークレットの新しいバージョンが作成されました" という通知をサブスクライブしたこと、およびシークレットの新しいバージョンをキー コンテナーに作成するのに必要な権限を持っていることを前提としています。

![](media/image9.png)

![](media/image10.png)

1.  Azure portal で、ご自身のキー コンテナーに移動します

1.  新しいシークレットを作成します。 テストの目的で、有効期限を翌日に設定します。

1.  ご自身のキー コンテナーの [イベント] タブに移動します。

1.  作成した Event Grid サブスクリプションを選択します。

1.  メトリックで、イベントがキャプチャされたかどうかを確認します。 2 つのイベントが想定されます。1 つは SecretNewVersion で、もう 1 つは SecretNearExpiry です。 これにより、キー コンテナーにあるシークレットの状態の変更が、Event Grid によって正常にキャプチャされたことが検証されます。

    ![](media/image11.png)

1.  Azure Automation アカウントに移動します。

1.  [Runbook] タブ、作成した Runbook の順に選択します。

1.  [Webhook] タブを選択し、"最終トリガー" のタイムスタンプが、新しいシークレット作成から 60 秒以内であることを確認します。  これにより、キー コンテナーの状態変更イベントの詳細を含む POST が Event Grid によって Webhook に対して実行され、Webhook がトリガーされたことが確認されます。

    ![](media/image12.png)

1. ご自身の Runbook に戻り、[概要] タブを選択します。

1. [最近のジョブ] の一覧を確認します。 ジョブが作成され、状態が "完了" になっていることがわかります。  これにより、Webhook によって Runbook がトリガーされ、そのスクリプトの実行が開始されたことが確認されます。

    ![](media/image13.png)

1. 最近のジョブを選択し、Event Grid から Webhook に送信された POST 要求を確認します。 JSON を調べ、キー コンテナーとイベントの種類のパラメーターが正しいことを確認します。 JSON オブジェクトの "イベントの種類" パラメーターが、キー コンテナーで発生したイベント (この例では Microsoft.KeyVault.SecretNearExpiry) と一致する場合、テストは成功しました。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="unable-to-create-event-subscription"></a>イベント サブスクリプションを作成できない

お使いの Azure サブスクリプションのリソース プロバイダーで、Event Grid および Key Vault プロバイダーを再登録します。 「[Azure リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」を参照してください。

## <a name="next-steps"></a>次の手順

お疲れさまでした。 上記のすべての手順を実行したら、お使いのキー コンテナーに格納されているシークレットの状態変更にプログラムで応答する準備ができています。

ポーリング ベースのシステムを使用して、キー コンテナー内のシークレットの状態変更を探す場合は、この通知機能の使用に移行します。 また、ご自身の Runbook のテスト スクリプトをコードに置き換えて、まもなく有効期限が切れるシークレットをプログラムによって更新することもできます。

詳細情報:

- [Azure Key Vault の概要](key-vault-overview.md)
- [Azure Event Grid の概要](../event-grid/overview.md)
- [Azure Event Grid での Key Vault の監視 (プレビュー)](event-grid-overview.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)](../event-grid/event-schema-key-vault.md)
- [Azure Automation の概要](../automation/index.yml)
