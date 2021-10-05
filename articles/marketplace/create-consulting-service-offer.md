---
title: Microsoft コマーシャル マーケットプレースでのコンサルティング サービス オファーを作成する方法
description: Microsoft パートナー センターでコマーシャル マーケットプレース プログラムを使用して、Microsoft AppSource または Azure Marketplace 向けの新しいコンサルティング サービス オファーを作成する方法について説明します。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: af8e238d57d6168e8c9620177cbf8e97a7d08727
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083024"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースでのコンサルティング サービス オファーを作成する方法

この記事では、パートナー センターを使用して Microsoft コマーシャル マーケットプレース向けのコンサルティング サービス オファーを作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

コンサルティング サービス オファーを公開するには、該当する分野における専門知識を示すための特定の資格要件を満たしている必要があります。 まだお読みになっていな場合は、<bpt id="p1">[</bpt>コマーシャル マーケットプレース向けのコンサルティング サービス オファーの計画<ept id="p1">](./plan-consulting-service-offer.md)</ept>に関する記事をご覧ください。 コンサルティング サービスの前提条件と、パートナー センターでオファーを作成する際に必要となる資産について説明しています。

## <a name="create-a-new-consulting-service-offer"></a>新しいコンサルティング サービス オファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. ホーム ページで、 **[Marketplace offers]\(Marketplace のプラン\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のプラン\) タイルの画像。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のプラン\) ページで、 **[+ 新しいプラン]**  >  **[コンサルティング サービス]** を選択します。

    [ ![[Marketplace offers]\(Marketplace のプラン\) ページの [新しいプラン] リストの画像。](./media/new-offer-consulting-service-workspaces.png) ](./media/new-offer-consulting-service-workspaces.png#lightbox)

1. **[New Consulting service]\(新しいコンサルティング サービス\)** ダイアログ ボックスの **[プラン ID]** を入力します。 この ID は、コマーシャル マーケットプレース一覧の URL に表示されます。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは <ph id="ph1">`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`</ph> になります。

    * アカウント内の各オファーには、一意のオファー ID が必要です。
    * 使用できるのは小文字と数字だけです。 オファー ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できません。また、50 文字に制限されています。
    * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファー ID を変更することはできません。

1. <bpt id="p1">**</bpt>[オファーのエイリアス]<ept id="p1">**</ept> を入力します。 これは、パートナー センター内でオファーに使用される名前です。 オンライン ストアには表示されず、顧客に表示されるオファー名とは異なります。
1. オファーを生成して続行するには、 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. 左側のナビゲーション メニューで、 <bpt id="p1">**</bpt>[商業マーケットプレース]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> の順に選択します。
1. [概要] タブで、 <bpt id="p1">**</bpt>[+ New offer]\(+ 新しいオファー\)<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[Consulting service]\(コンサルティング サービス\)<ept id="p2">**</ept> を選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-consulting-service.png)

1. <bpt id="p1">**</bpt>[新しいオファー]<ept id="p1">**</ept> ダイアログ ボックスで、 <bpt id="p2">**</bpt>[オファー ID]<ept id="p2">**</ept> を入力します。 この ID は、コマーシャル マーケットプレース一覧の URL に表示されます。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは <ph id="ph1">`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`</ph> になります。

    * アカウント内の各オファーには、一意のオファー ID が必要です。
    * 使用できるのは小文字と数字だけです。 オファー ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できません。また、50 文字に制限されています。
    * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファー ID を変更することはできません。

1. <bpt id="p1">**</bpt>[オファーのエイリアス]<ept id="p1">**</ept> を入力します。 これは、パートナー センター内でオファーに使用される名前です。 オンライン ストアには表示されず、顧客に表示されるオファー名とは異なります。
1. オファーを生成して続行するには、 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択します。

---

## <a name="configure-lead-management"></a>リード管理の構成

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客がこのコンサルティング サービスに興味を示したときに顧客の連絡先情報を受け取れるようにします。 この接続は、オファーの作成中または作成後にいつでも変更できます。 詳細なガイダンスについては、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース オファーからの潜在顧客<ept id="p1">](./partner-center-portal/commercial-marketplace-get-customer-leads.md)</ept>」を参照してください。

パートナー センターでリード管理を構成するには、次のようにします。

1.  パートナー センターで、 <bpt id="p1">**</bpt>[オファーのセットアップ]<ept id="p1">**</ept> タブに移動します。
2.  <bpt id="p1">**</bpt>[潜在顧客]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[接続]<ept id="p2">**</ept> リンクを選択します。
3.  <bpt id="p1">**</bpt>[接続の詳細]<ept id="p1">**</ept> ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
4.  表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

    * <bpt id="p1">[</bpt>リードを Azure テーブルに送信するようにオファーを構成する<ept id="p1">](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)</ept>
    * <bpt id="p1">[</bpt>Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する<ept id="p1">](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)</ept>
    * <bpt id="p1">[</bpt>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する<ept id="p1">](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)</ept>
    * <bpt id="p1">[</bpt>リードを Marketo に送信するようにオファーを構成する<ept id="p1">](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)</ept>
    * <bpt id="p1">[</bpt>リードを Salesforce に送信するようにオファーを構成する<ept id="p1">](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)</ept>

5.  指定した構成を検証するには、 <bpt id="p1">**</bpt>[検証]<ept id="p1">**</ept> リンクを選択します。
6.  接続の詳細を構成したら、 <bpt id="p1">**</bpt>[接続]<ept id="p1">**</ept> を選択します。
7.  <bpt id="p1">**</bpt>[下書きの保存]<ept id="p1">**</ept> を選択します。

パートナー センターでの公開のためにオファーが送信されると、Microsoft で接続を検証して、テスト リードを送信します。 オファーを公開する前のプレビュー中に、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストします。

> [!TIP]
> リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

## <a name="next-steps"></a>次のステップ

* <bpt id="p1">[</bpt>コンサルティング サービス オファーのプロパティを構成する方法<ept id="p1">](./create-consulting-service-offer-properties.md)</ept>
