---
title: Salesforce 用にリード管理を構成する | Azure Marketplace
description: Azure Marketplace のお客様向けに Salesforce でリード管理を構成します。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263096"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce 用にリード管理を構成する

この記事では、商業マーケットプレース オファーからリードを処理するように Salesforce システムをセットアップする方法について説明します。

> [!Note]
> Marketplace では、 **[Country]\(国\)** フィールドの値の一覧など、事前設定済みのリストはサポートされていません。 続行する前に、リストが設定されていないことを確認してください。 代わりに、リードを受信するために [HTTPS エンドポイント](./commercial-marketplace-lead-management-instructions-https.md)や [Azure テーブル](./commercial-marketplace-lead-management-instructions-azure-table.md)を構成できます。

## <a name="set-up-your-salesforce-system"></a>Salesforce システムを設定する

1. Salesforce にサインインします。
2. Salesforce Lighting Experience を使用している場合。
    1. Salesforce ホーム ページから **[セットアップ]** を選択します。
    ![Salesforce のセットアップ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. [セットアップ] ページで、左側のナビゲーションを使用して **[プラットフォーム ツール] -> [機能の設定] -> [マーケティング] -> [Web-to-Lead]\(Web-to-Lead\)** に移動します。

        ![Salesforce の Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Salesforce Classic Experience を使用している場合:
    1. Salesforce ホーム ページから **[セットアップ]** を選択します。
    ![Salesforce Classic の [セットアップ]](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. [セットアップ] ページで、左側のナビゲーションを使用して、 **[ビルド] -> [カスタマイズ] -> [リード] -> [Web-to-Lead]\(Web-to-Lead\)** に移動します。

        ![Salesforce Classic の Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

残りの手順は、使用している Salesforce エクスペリエンスに関係なく同じです。

4. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** ページで、 **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** ボタンを選択します。
5. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** で **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** を選択します。
    ![Salesforce - [Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** で、`the Include reCAPTCHA in HTML` 設定がオフになっていることを確認し、 **[生成]** を選択します。 
    ![Salesforce - [Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. HTML テキストが表示されます。 "oid" というテキストを検索し、HTML テキストから **oid 値** (引用符で囲まれたテキストのみ) をコピーして保存します。 この値を発行ポータル上の **[組織 ID]** フィールドに貼り付けます。
    ![Salesforce - [Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **[完了]** を選択しました。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>リードを Salesforce に送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
1. [リード管理] セクションで **[接続]** を選択します。
    ![[リード管理] - [接続]](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. [接続の詳細] ポップアップ ウィンドウで、 **[リードのターゲット]** に対して **[Salesforce]** を選択し、前の手順で作成した Web-to-Lead フォームの `oid` を **[組織 ID]** フィールドに貼り付けます。

1. **連絡先の電子メール** - 新しいリードを受信したときに、メール通知を受け取る必要のある、社内のユーザーにメールを送信します。 複数の電子メールを指定するには、セミコロンで区切ります。

1. **[OK]** を選択します。

リードの宛先に正常に接続したことを確認するには、[検証] ボタンをクリックします。 成功した場合は、リードの宛先にテスト リードが表示されます。

>[!Note]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

![[接続の詳細] - [リードのターゲット] の選択](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![[接続の詳細] - [リードのターゲット] の選択](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
