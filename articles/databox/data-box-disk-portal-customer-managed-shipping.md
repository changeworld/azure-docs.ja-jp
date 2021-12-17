---
title: Microsoft Azure Data Box Disk の自己管理型の発送 | Microsoft Docs in data
description: Azure Data Box Disk デバイスの自己管理型の発送ワークフローについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 06/22/2021
ms.author: alkohli
ms.openlocfilehash: 66ff9018371de309b61824895492335e0d3bb763
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112517656"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Azure portal で Azure Data Box Disk の自己管理型の発送を使用する

この記事では、Azure Data Box Disk の注文、受け取り、および返却を行う、自己管理型の発送タスクについて説明します。 Azure portal を使用して Data Box Disk を管理できます。

## <a name="prerequisites"></a>前提条件

自己管理型の発送は、[Azure Data Box Disk の注文](data-box-disk-deploy-ordered.md)時にオプションとして利用できます。 自己管理型の発送は、次のリージョンでのみご利用いただけます。

* 米国政府
* イギリス
* 西ヨーロッパ
* オーストラリア
* 日本
* シンガポール
* 韓国
* 南アフリカ
* インド (プレビュー)
* ブラジル

## <a name="use-self-managed-shipping"></a>自己管理の出荷の使用

Data Box Disk を注文するときに、自己管理型の発送オプションを選択できます。

1. Azure Data Box Disk の注文の **[連絡先の詳細]** で、 **[+ 配送先住所の追加]** を選択します。

   ![[連絡先の詳細] 手順を示す [注文] ウィザードのスクリーンショット。[配送先住所の追加] オプションが強調表示されています。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 発送の種類を選択するときに、 **[Self-managed shipping]\(自己管理型の発送\)** オプションを選択します。 このオプションは、「前提条件」で説明されているように、サポートされているリージョンでのみ使用できます。

3. 発送先住所を入力したら、確認して注文を完了する必要があります。

   ![[配送先住所の追加] ダイアログ ボックスのスクリーンショット。[出荷方法] オプションと [配送先住所の追加] オプションが強調表示されています。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. デバイスの準備が完了し、電子メール通知を受け取ったら、受け取りをスケジュールします。 Azure Data Box Disk の注文の **[概要]** に移動し、 **[Schedule pickup]\(受け取りのスケジュールを設定\)** を選択します。

   ![Data Box デバイスの受け取りを注文する](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** の説明に従ってください。 認証コードを取得する前に、[adbops@microsoft.com](mailto:adbops@microsoft.com) にメールを送信して、リージョンのデータセンターからデバイスを受け取りのスケジュールを設定する必要があります。

   ![集荷のスケジュール](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

   **ブラジルの手順:** ブラジルでデバイスの受け取りをスケジュールする場合、メールに次の情報を含めます。 データセンターは、インバウンド `Nota Fiscal` を受信した後に受け取りをスケジュールします。これには、最大で 4 営業日かかります。

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

6. デバイスの受け取りをスケジュールすると、 **[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\)** に認証コードが表示されます。

   ![[Schedule pickup for Azure]\(Azure の受け取りのスケジュール\) ダイアログボックスのスクリーンショット。[引き取りの認証コード] テキスト ボックスが強調表示されています。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   この認証コードをメモしておきます。 デバイスを受け取るユーザーは、それを所持する必要があります。

   受け取りをスケジュールするとき、セキュリティ要件に従い、受け取りのために来訪するユーザーの名前と詳細を提供する必要があります。 お客様または連絡担当者は政府発行の写真付きの ID を携帯する必要があり、これはデータセンターで検証されます。

7. 予定時刻にデータセンターで Data Box Disk を受け取ります。

   デバイスを受け取るユーザーは、以下を用意する必要があります。

   * データセンターに来訪するための、Microsoft Operations からの確認メールのコピー。

   * 承認コード。 参照番号は、受け取りまたは返却ごとに固有であり、データセンターでの確認に使用されます。

   * 政府発行の写真付きの ID。 ID はデータセンターでの確認に使用されます。受け取りのスケジュールを設定する際に、デバイスを受け取るユーザーの名前と詳細を指定する必要があります。

   > [!NOTE]
   > スケジュールした予約を忘れた場合、新しい予約をスケジュールする必要があります。

8. デバイスをデータセンターから受け取った後、注文は自動的に **[Picked up]\(受け取り済み\)** 状態になります。

   ![集荷されました](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

9. デバイスを受け取ったら、サイトのデータを Data Box Disk にコピーできます。 データのコピーが完了したら、Data Box Disk の発送の準備をします。

   データのコピーが終了したら、操作部門に連絡して返却の予定をスケジュールします。 データセンターにディスクを返却しに来る人物の詳細を共有する必要があります。 データセンターは、返却時に認証コードを確認する必要もあります。 返却の承認コードは、Azure portal の **[Schedule drop off]\(返却のスケジュールを設定\)** にあります。

   > [!NOTE]
   > この認証コードは、メールで共有しないでください。 これは、データセンターでの返却時にのみ検証されます。

   **ブラジルの手順:** ブラジルでデバイス返却をスケジュールするには、次の情報を含むメールを [adbops@microsoft.com](mailto:adbops@microsoft.com) に送信します。

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 返却の予定を受け取ると、Azure portal に表示されるその注文の状態が **[Ready to receive at Azure datacenter]\(Azure データセンターで受け取る準備が完了しました\)** になります。

    ![[配送先住所の追加] ダイアログ ボックスのスクリーンショット。[出荷方法] オプションと [配送先住所の追加] オプションが強調表示されています。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

11. ID と認証コードが検証され、データセンターにデバイスを返却すると、注文ステータスが **[Received]\(返却済み\)** になります。

    ![返却完了](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. デバイスを返却すると、データのコピーが続行されます。 コピーが完了すると、注文が完了します。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure portal を使用して Azure Data Box Disk をデプロイする](data-box-disk-quickstart-portal.md)
