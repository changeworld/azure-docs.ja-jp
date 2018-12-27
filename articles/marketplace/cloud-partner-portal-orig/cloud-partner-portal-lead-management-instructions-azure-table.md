---
title: Azure テーブル | Microsoft Docs
description: Azure テーブル用にリード管理を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808467"
---
<a name="lead-management-instructions-for-azure-table"></a>Azure テーブルでの潜在顧客管理の手順
============================================

この記事では、潜在顧客を格納するために Azure テーブルを構成する方法について説明します。 Azure テーブルを使用して、顧客情報を保存およびカスタマイズできます。

## <a name="to-configure-azure-table"></a>Azure テーブルを構成するには

1.  Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。

2.  Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
3.  Azure portal でストレージ アカウントを作成します。 次の画面キャプチャでは、ストレージ アカウントを作成する方法を示します。 ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

    ![Azure ストレージ アカウントを作成する手順](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  キーのストレージ アカウント接続文字列をコピーし、Cloud パートナー ポータルの **[ストレージ アカウント接続文字列]** フィールドに貼り付けます。 `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net ` は接続文字列の例です。
    
    ![Azure ストレージ キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure のストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)またはその他任意のツールを使用して、ストレージ テーブル内のデータを表示できます。 Azure テーブルのデータをエクスポートすることもできます
。

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(省略可能)** Azure テーブルで Azure Functions を使用するには

リードを受け取る方法をカスタマイズする場合は、Azure テーブルで [Azure Functions](https://azure.microsoft.com/services/functions/) を使用します。 Azure Functions サービスを使用すると、リードの生成プロセスを自動化できます。

以下の手順では、タイマーを使用する Azure 関数を作成する方法を示します。 関数は、5 分ごとに Azure テーブルで新しいレコードを検索し、SendGrid サービスを使ってメール通知を送信します。


1.  Azure サブスクリプションで無料の SendGrid サービス アカウントを[作成](https://portal.azure.com/#create/SendGrid.SendGrid)します。

    ![SendGrid の作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  SendGrid API キーを作成する 
    - **[管理]** を選択して、SendGrid の UI に移動します
    - **[Settings]\(設定\)**、**[API Keys]\(API キー\)** を選択し、[Mail Send]\(メール送信\) -\> [Full Access]\(フル アクセス\) を指定してキーを作成します
    - API キーを保存します


    ![SendGrid API キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  "Consumption Plan" という名前の [ホスティング プラン] オプションを使用して、Azure 関数アプリを[作成](https://portal.azure.com/#create/Microsoft.FunctionApp)します。

    ![Azure Function App の作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  新しい関数定義を作成します。

    ![Azure 関数定義を作成する](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  特定の時点に関数から更新を送信するには、スターター オプションとして **TimerTrigger-CSharp** を選択します。

     ![Azure 関数の時間トリガー オプション](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  "開発" コードを、次のコード サンプルに置き換えます。 メール アドレスを、送信者と受信者が使用するアドレスで編集します。

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Azure 関数のコード スニペット](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  **[統合]** と **[入力]** を選択して、Azure テーブルの接続を定義します。

    ![Azure 関数の統合](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  テーブル名を入力し、**[新規]** を選択して接続文字列を定義します。


    ![Azure 関数のテーブルとの接続](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  ここで [出力] を SendGrid として定義し、すべて既定値のままにします。

    ![SendGrid の出力](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![SendGrid 出力の既定値](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. SendGrid UI 内で、"SendGridApiKey" という名前と API キーから取得した値を使用して、[Function App の設定] に SendGrid API キーを追加します

    ![SendGrid の画像](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid の管理キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

関数の構成が完了すると、[統合] セクションのコードは次の例のようになります。

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. 最後に、関数の開発用 UI に移動し、**[実行]** を選択してタイマーを開始します。 これで、新しいリードが届くたびに通知を受け取るようになります。
