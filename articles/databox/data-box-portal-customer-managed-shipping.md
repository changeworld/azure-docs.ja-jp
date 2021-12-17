---
title: Microsoft Azure Data Box の自己管理型の発送 |Microsoft Docs in data
description: Azure Data Box デバイスの自己管理型の発送ワークフローについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 07/22/2021
ms.author: alkohli
ms.openlocfilehash: cafea36f45dcc063fadc1562428735ee0ac9fe20
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652822"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Azure portal で Azure Data Box の自己管理型の発送を使用する

この記事では、Azure Data Box デバイスの注文、受け取り、および返却を行う、自己管理型の発送タスクについて説明します。 Data Box デバイスは、Azure portal を使用して管理できます。

> [!NOTE]
> Data Box の注文と出荷に関するよくあるご質問の回答については、「[Data Box の FAQ](data-box-faq.yml)」を参照してください。

## <a name="prerequisites"></a>前提条件

自己管理型の発送は、[Azure Data Box の注文](data-box-deploy-ordered.md)時にオプションとして利用できます。 自己管理型の発送は、次のリージョンでのみご利用いただけます。

* 米国政府
* イギリス
* 西ヨーロッパ
* 日本
* シンガポール
* 韓国
* インド
* 南アフリカ
* オーストラリア
* ブラジル

## <a name="use-self-managed-shipping"></a>自己管理の出荷の使用

Data Box を注文するときに、自己管理型の発送オプションを選択できます。

1. Azure Data Box の注文の **[連絡先の詳細]** で、 **[+ 配送先住所の追加]** を選択します。
 
   ![自己管理型の発送、配送先住所の追加](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 発送の種類を選択する時に、 **[Self-managed shipping]\(自己管理型の発送\)** オプションを選択します。 このオプションは、「前提条件」で説明されているように、サポートされているリージョンでのみ使用できます。

3. 配送先住所を入力したら、確認して注文を完了する必要があります。

   ![自己管理型の発送、確認して住所を追加する](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. デバイスの準備が完了し、その電子メール通知を受け取ったら、受け取りをスケジュールします。

   Azure Data Box の注文の **[概要]** に移動し、 **[Schedule pickup]\(受け取りのスケジュールを設定\)** を選択します。

   ![Data Box 注文、受け取りオプションのスケジュール](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** の説明に従ってください。

   認証コードを取得する前に、[adbops@microsoft.com](mailto:adbops@microsoft.com) にメールを送信して、リージョンのデータセンターからデバイスを受け取りのスケジュールを設定する必要があります。

   ![Azure の受け取りのスケジュールの手順](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

   **ブラジル向けの手順:** ブラジルでのデバイスの受け取りのスケジュールを設定する場合は、メールに次の情報を含めます。 データセンターは、インバウンド `Nota Fiscal` を受信した後に受け取りのスケジュールを設定します。これには、最大で 4 営業日かかります。

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - CNPJ (Business Tax ID, format: 00.000.000/0000-00) or CPF (Individual Tax ID, format: 000.000.000-00)
   - Address
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. デバイスの受け取りをスケジュールすると、 **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** ウィンドウにデバイスの認証コードが表示されます。

   ![デバイス認証コードの表示](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   この **認証コード** をメモしておきます。 デバイスを受け取るユーザーがそれを提示する必要があります。

   セキュリティ要件に従って、受け取りのスケジュールを設定する際に、受け取りのために来訪するユーザーの名前と詳細を指定する必要があります。 お客様または連絡担当者は、データセンターでの確認のために、政府発行の写真付きの ID を携帯する必要があります。

7. 予定時刻にデータセンターで Data Box を受け取ります。

   デバイスを受け取るユーザーは、次のものを用意する必要があります。

   * データセンターに来訪するための、Microsoft Operations からの確認メールのコピー。

   * 承認コード。 参照番号は、受け取りまたは返却ごとに固有であり、データセンターでの確認に使用されます。

   * 政府発行の写真付きの ID。 ID はデータセンターでの確認に使用されます。受け取りのスケジュールを設定する際に、デバイスを受け取るユーザーの名前と詳細を指定する必要があります。

   > [!NOTE]
   > スケジュールを設定した予約に間に合わなかった場合は、新しい予約のスケジュールを設定する必要があります。

8. デバイスをデータセンターから受け取ると、注文は自動的に **[Picked up]\(受け取り済み\)** 状態になります。

    ![受け取り状態の注文](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

9. デバイスを受け取ったら、サイトのデータを Data Box にコピーします。 データのコピーが完了したら、Data Box の発送の準備をします。 詳細については、「[配送の準備](data-box-deploy-picked-up.md#prepare-to-ship)」を参照してください。

   重大なエラーが発生することなく、「**発送準備**」の手順を完了する必要があります。 そうでなければ、必要な修正を行ってから、この手順をもう一度実行する必要があります。 「**発送準備**」の手順が正常に完了したら、デバイスのローカル ユーザー インターフェイスで、返却用の認証コードを確認します。

   > [!NOTE]
   > この認証コードは、メールで共有しないでください。 これは、データセンターでの返却時にのみ検証されます。

   **ブラジル向けの手順:** ブラジルでのデバイス返却のスケジュールを設定するには、次の情報が含まれるメールを [adbops@microsoft.com](mailto:adbops@microsoft.com) に送信します。

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 返却の予定を受け取ると、Azure portal に表示されるその注文の状態が **[Ready to receive at Azure datacenter]\(Azure データセンターで受け取る準備が完了しました\)** になります。 **[Schedule drop-off]\(返却のスケジュール\)** の指示に従って、デバイスを返却します。

    ![デバイスの返却の手順](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

11. ID と認証コードが検証され、データセンターにデバイスを返却すると、注文ステータスが **[Received]\(返却済み\)** になります。

    ![返却済みステータスの注文](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

12. デバイスを返却すると、データのコピーが続行されます。 コピーが完了すると、注文が完了します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Box を使ってみる](data-box-quickstart-portal.md)
