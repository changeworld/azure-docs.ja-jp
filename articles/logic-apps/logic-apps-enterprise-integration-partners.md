---
title: ワークフローの取引先を定義する
description: Enterprise Integration Pack を使用して、Azure Logic Apps のワークフロー用の統合アカウントに取引先を追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549837"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps のワークフローの統合アカウントに取引先を追加する

企業間 (B2B) エンタープライズ統合ワークフローで自身の組織と別の組織を表すために、ビジネス リレーションシップにおける各参加者を表す "*取引先*" を[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)に作成します。 パートナーとは、企業間 (B2B) のトランザクションに参加し、互いにメッセージを交換するビジネス エンティティです。

> [!IMPORTANT]
> これらのパートナーを定義する前に、互いに送信するメッセージを識別して検証する方法についてパートナーと話し合います。 契約に参加し、互いにメッセージを交換するために、統合アカウントのパートナーは、同じまたは互換性のある "*ビジネス修飾子*" を使用する必要があります。 これらの詳細に同意できたら、統合アカウントにパートナーを作成する準備が整います。

この記事では、パートナー間でメッセージを交換するための特定の業界標準プロトコルを定義する契約を後で作成するために使用できる、パートナーを作成して管理する方法について説明します。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、[Azure Logic Apps を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、ワークフローで成果物を使用するには、統合アカウントに[ロジック アプリ リソースへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)が必要です。

  * [**ロジック アプリ (Standard)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、統合アカウントにロジック アプリ リソースへのリンクは必要ありませんが、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、および [EDIFACT](logic-apps-enterprise-integration-edifact.md) の操作を使用すると共に、パートナー、契約、証明書などの他の成果物を保存する必要があります。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

  > [!NOTE]
  > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** のリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作が含まれていません。

<a name="add-partner"></a>

## <a name="add-a-partner"></a>パートナーを追加する

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、パートナーを追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[パートナー]** を選択します。

1. **[パートナー]** ペインで、 **[追加]** を選択します。

1. **[パートナーの追加]** ペインで、パートナーに関する次の情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **名前** | はい | パートナーの名前 |
   | **修飾子** | はい | 組織に固有のビジネス ID を提供する認証機関。たとえば、**D-U-N-S (Dun & Bradstreet)** 。 <p>パートナーは、相互に定義されたビジネス ID を選ぶことができます。 これらのシナリオでは、EDIFACT に対して **[相互定義]** を選び、X12 に対して **[相互定義 (X12)]** を選びます。 <p>RosettaNet の場合は、標準の **[DUNS]** のみを選びます。 <p>**重要**: 統合アカウントのパートナーが契約に参加し、互いにメッセージを交換するには、同じまたは互換性のある修飾子を使用する必要があります。 |
   | **Value** | はい | 自分のロジック アプリが受け取るドキュメントを識別する値。 <p>RosettaNet を使用するパートナーの場合、この値は DUNS 番号に対応する 9 桁の数字である必要があります。 最初にパートナーを作成してから、[それらパートナーの定義を後で編集する](#edit-partner)ことで、RosettaNet パートナーの分類や連絡先情報などの追加情報を指定できます。 |
   ||||

1. 終了したら、 **[OK]** を選択します。

   パートナーが **[パートナー]** の一覧に表示されます。

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>パートナーを編集する

1. [Azure portal](https://portal.azure.com) で、統合アカウントを開きます。

1. 統合アカウント メニューの **[設定]** で、 **[パートナー]** を選択します。

1. **[パートナー]** ペインでパートナーを選択し、 **[編集]** を選択して変更を加えます。

   RosettaNet を使用するパートナーの場合は、 **[RosettaNet パートナーのプロパティ]** の下で、次の表に示す詳細を指定できます。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **パートナーの分類** | いいえ | パートナーの組織の種類 |
   | **サプライ チェーン コード** | いいえ | パートナーのサプライ チェーン コード。たとえば、"情報技術"、"電子部品" |
   | **Contact Name (連絡先の名前)** | いいえ | パートナーの連絡先の名前 |
   | **Email** | いいえ | パートナーのメール アドレス |
   | **Fax** | いいえ | パートナーの fax 番号 |
   | **電話** | いいえ | パートナーの電話番号 |
   ||||

1. 終了したら、 **[OK]** を選択します。

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>パートナーを削除する

1. [Azure portal](https://portal.azure.com) で、統合アカウントを開きます。

1. 統合アカウント メニューの **[設定]** で、 **[パートナー]** を選択します。

1. **[パートナー]** ペインで削除するパートナーを選択し、 **[削除]** を選択します。

1. パートナーの削除を確定するには、 **[はい]** を選択します。

## <a name="next-steps"></a>次の手順

* [パートナー間に契約を追加する](logic-apps-enterprise-integration-agreements.md)