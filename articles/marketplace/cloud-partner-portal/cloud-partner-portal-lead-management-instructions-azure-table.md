---
title: "Azure Marketplace の Azure テーブルでの潜在顧客管理の手順 | Microsoft Docs"
description: "この記事では、パブリッシャーが Azure テーブル ストレージによる潜在顧客管理をセットアップする方法について説明します。"
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 45aa2e0b178969215df9ae305d0a13a350ae0fda
ms.lasthandoff: 04/19/2017


---

# <a name="lead-management-instructions-for-azure-table"></a>Azure テーブルでの潜在顧客管理の手順

本書では、Azure テーブルをセットアップして Microsoft がセールス リードを提供できるようにする方法の手順を説明します。 Azure テーブルは、潜在顧客を格納する方法をカスタマイズする場合に最適な既定の選択肢です。

1. Azure アカウントを持っていない場合は、最初に [1 つ作成](https://azure.microsoft.com/pricing/free-trial/)してください。

2. Azure アカウントを準備したら、[Azure ポータル](https://portal.azure.com)にログインし、ストレージ アカウントを作成します。 手順については次のスクリーンショットを参照してください。[ストレージの料金設定の詳細についてはこちら](https://azure.microsoft.com/pricing/details/storage/)をご覧ください。 <br/>
  ![Azure でストレージを作成する流れのイメージ](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

3. 次に、キーの接続文字列をコピーして、クラウド パートナー ポータル上の <b>[Storage Account Connection String](ストレージ アカウント接続文字列)</b> フィールドに貼り付けます。 <br/> 
  ![Azure でのストレージ キーのイメージ](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

  サンプルの最終的な接続文字列は次のようになります。 <br/>
`{"connectionString":"DefaultEndpointsProtocol=https;AccountName=leadaccount;AccountKey=ObS0EW7tDmXrC8oNeG6IRHpx2IUioBQTQynQcR/MUMqrNqQ/RC6zctP8HfucNJO+ond7dJHTROO9ziiPNspjEg=="}`

[Azure のストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/) (サード パーティ製アプリケーション) またはその他任意のツールを使用して、ストレージ テーブル内のデータを表示するか、データをエクスポートします。

### <a name="optional-azure-functions--azure-table"></a>**(省略可能)** Azure Functions と Azure テーブルの併用
これらの潜在顧客を受信する方法をカスタマイズする場合は、[Azure Functions](https://azure.microsoft.com/services/functions/) サービスを利用すると、潜在顧客の生成プロセスを非常に柔軟に自動化することができます。 以下に、タイマーを備えた Azure 関数の作成方法の例を示します。このタイマーは5 分ごとに実行され、新しいレコードがないか Azure テーブルを検索し、SendGrid サービスの無料レベルを使用してシンプルな電子メール通知を送信します。

1. Azure サブスクリプションで無料の SendGrid サービス アカウントを[作成](https://portal.azure.com/#create/SendGrid.SendGrid)します。
  
    ![SendGrid の作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)
  
2. SendGrid API キーを作成します ([キーの管理] をクリックして SendGrid の UI に移動し、[設定]、[API キー] の順にクリックします。[電子メールの送信] を [フル アクセス] に設定したキーを作成し、その API キーを保存します)。
 
    ![SendGrid API キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)

3. “Consumption Plan” という [ホスティング プラン] オプションを使用して Azure 関数アプリを[作成](https://portal.azure.com/#create/Microsoft.FunctionApp)します。 
  
    ![Azure 関数の作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)

4. 新しい関数定義を作成します。 
  
    ![Azure 関数定義の作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)

5. 特定の時点に関数から更新を送信する場合は、ここで、スターター オプションとして TimerTrigger-CSharp を選択します。
  
    ![Azure 関数の時間トリガー オプション](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)
  
6. “開発用” コードを、下の表のコードで置き換えます。 電子メール アドレスは、必要な送信者および受信者に合わせて編集してください。
これはサンプル コードにすぎないため、必要に応じて改善のために変更できます。
  
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

7. [統合] と [入力] と をクリックして Azure テーブルの接続を定義します。
  
    ![Azure 関数の統合](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)

8. テーブル名を入力し、[new](新規) をクリックして接続文字列を定義します。
  
    ![Azure 関数のテーブルとの接続](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9. ここで [出力] を SendGrid として定義し、すべて既定値のままにします。
  
    ![SendGrid 出力](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)
    ![SendGrid 出力の既定値](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. SendGrid UI 内で、"SendGridApiKey"という名前と API キーから取得した値を使用して、[Function App の設定] に SendGrid API キーを追加します。
  
    ![SendGrid のイメージ](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid の managekey](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

これらがすべて構成されると、[統合] セクションには次のようなコードが含まれています。

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
  
最後に、関数の開発用 UI に戻り、**[実行]** をクリックしてタイマーを開始します。 これで、新しい潜在顧客を受信するたびに通知されるようにすべて設定されました。

