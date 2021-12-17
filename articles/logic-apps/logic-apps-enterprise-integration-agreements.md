---
title: ワークフローにおけるパートナー間のアグリーメントを定義する
description: Enterprise Integration Pack を使用して、Azure Logic Apps のワークフロー用の統合アカウントにパートナー間のアグリーメントを追加します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 7b634da15248513ee782967eb1c86092e940ec9b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361292"
---
# <a name="add-agreements-between-partners-in-integration-accounts-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps のワークフロー用の統合アカウントにパートナー間のアグリーメントを追加する

統合アカウントにパートナーを追加したら、統合アカウントで "[*アグリーメント*](logic-apps-enterprise-integration-agreements.md)" を定義して、パートナーとメッセージを交換する方法を指定します。 アグリーメントは、メッセージを交換するための特定の業界標準プロトコルを定義して、組織がシームレスに相互通信するのに役立ちます。アグリーメントには、次の共通の利点があります。

* 組織はよく知られている形式を使用して情報を交換することができます。

* 企業間 (B2B) トランザクションを実行する際に、効率が向上します。

* エンタープライズ統合ソリューションを構築する際に、アグリーメントを簡単に作成、管理、使用できます。

アグリーメントには、"*ホスト パートナー*" (常に自分の組織) と "*ゲスト パートナー*" (自分の組織とメッセージを交換する組織) が必要です。 ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。 このアグリーメントを使用して、ホスト パートナーの観点から受信メッセージと送信メッセージを処理する方法を指定します。

この記事では、アグリーメントを作成および管理する方法について説明します。アグリーメントは、AS2、X12、EDIFACT、または RosettaNet の操作を使用して別のパートナーと B2B メッセージを交換する際に使用できます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、ワークフローで成果物を使用するには、統合アカウントに [ロジック アプリ リソースへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)が必要です。

  * [**ロジック アプリ (Standard)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、統合アカウントにロジック アプリ リソースへのリンクは必要ありませんが、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、および [EDIFACT](logic-apps-enterprise-integration-edifact.md) の操作を使用すると共に、パートナー、アグリーメント、証明書などの他の成果物を保存する必要があります。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

  > [!NOTE]
  > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** のリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作が含まれていません。

* 統合アカウント内の少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)。 アグリーメントには、ホスト パートナーとゲスト パートナーが必要です。 また、アグリーメントでは、両方のパートナーが、AS2、X12、EDIFACT、または RosettaNet アグリーメントに適した同じまたは互換性のある "*ビジネス ID*" 修飾子を使用する必要があります。

* 必要に応じて、アグリーメントを使用してメッセージを交換するロジック アプリ リソースとワークフロー。 ワークフローには、ロジック アプリのワークフローを開始するトリガーが必要です。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="add-an-agreement"></a>アグリーメントを追加する

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、パートナーを追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[契約]** を選択します。

1. **[アグリーメント]** ペインで、 **[追加]** を選択します。

1. **[追加]** ペインで、アグリーメントに関する次の情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | <*agreement-name*> | 契約の名前 |
   | **契約の種類** | はい | **AS2**、**X12**、**EDIFACT**、または **RosettaNet** | ご利用の契約でのプロトコルの種類。 契約ファイルを作成する場合は、そのファイルの内容が契約の種類と一致する必要があります。 |
   | **ホスト パートナー** | はい | <*host-partner-name*> | ホスト パートナーは自分の組織を表します |
   | **ホスト ID** | はい | <*host-partner-identifier*> | ホスト パートナーの ID |
   | **ゲスト パートナー** | はい | <*guest-partner-name*> | ゲスト パートナーは、自分の組織と通信する組織を表します |
   | **ゲスト ID** | はい | <*guest-partner-identifier*> | ゲスト パートナーの ID |
   | **受信設定** | 場合により異なる | 場合により異なる | これらのプロパティでは、アグリーメントでホスト パートナーがゲスト パートナーから受信メッセージを受信する方法を指定します。 詳細については、それぞれのアグリーメントの種類を参照してください。 <p>- [AS2 メッセージの設定](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 メッセージの設定](logic-apps-enterprise-integration-x12.md) |
   | **送信の設定** | 場合により異なる | 場合により異なる | これらのプロパティでは、アグリーメントでホスト パートナーがゲスト パートナーに送信メッセージを送信する方法を指定します。 詳細については、それぞれのアグリーメントの種類を参照してください。 <p>- [AS2 メッセージの設定](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 メッセージの設定](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet PIP 参照** | 場合により異なる | 場合により異なる | このペインでは、RosettaNet メッセージを使用する 1 つ以上の Partner Interface Process (PIP) に関する情報を指定します。 詳細については、[RosettaNet メッセージの交換](logic-apps-enterprise-integration-rosettanet.md)に関する記事を参照してください。 |
   |||||

   > [!IMPORTANT]
   > アグリーメントの解決には、パートナーと受信メッセージに定義されている次の項目が一致している必要があります。
   >
   > * 送信者の修飾子と識別子
   > * 受信者の修飾子と識別子
   >
   > パートナー用のこれらの値を変更すると、必ず契約も更新されます。

1. 終了したら、 **[OK]** を選択します。

   これで、追加したアグリーメントが **[アグリーメント]** 一覧に表示されます。

## <a name="edit-an-agreement"></a>アグリーメントを編集する

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、パートナーを追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[契約]** を選択します。

1. **[アグリーメント]** ペインでアグリーメントを選択し、 **[編集]** を選択して変更を加えます。

1. 終了したら、 **[OK]** を選択します。

## <a name="delete-an-agreement"></a>アグリーメントを削除する

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で、パートナーを追加する統合アカウントを選択します。

1. 統合アカウント メニューの **[設定]** で、 **[契約]** を選択します。

1. **[アグリーメント]** ペインで削除するアグリーメントを選択し、 **[削除]** を選択します。

1. アグリーメントの削除を確認するには、 **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

* [AS2 メッセージの交換](logic-apps-enterprise-integration-as2.md)
* [EDIFACT メッセージの交換](logic-apps-enterprise-integration-edifact.md)
* [X12 メッセージの交換](logic-apps-enterprise-integration-x12.md)
* [RosettaNet メッセージの交換](logic-apps-enterprise-integration-rosettanet.md)
