---
title: Enterprise Agreement の問題に関する Azure サポート リクエストを作成する方法
description: サポートが必要な Enterprise Agreement のお客様は、Azure portal を使用して、セルフサービス ソリューションを見つけたり、サポート リクエストを作成および管理したりすることができます。
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 7826b2c19a77ef3762f39ade9a80f26615f704bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660193"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>Enterprise Agreement の問題に関する Azure サポート リクエストを作成する

Azure では、Enterprise Agreement に関するサポート リクエスト (サポート チケットとも呼ばれます) を作成して管理できます。 この記事で説明するように、[Azure portal ](https://portal.azure.com)上で要求を作成して管理することができます。 [Azure サポート チケット REST API ](/rest/api/support)を使用して、または [Azure CLI](/cli/azure/azure-cli-support-request) を使用して、プログラムによってリクエストを作成して管理することもできます。

> [!NOTE]
> Azure portal の URL は、組織がデプロイされている Azure クラウドに固有のものです。
>
>- 商用利用の Azure portal は次のとおりです。[https://portal.azure.com](https://portal.azure.com)
>- ドイツの Azure portal は次のとおりです: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- 米国政府機関向けの Azure portal は次のとおりです: [https://portal.azure.us](https://portal.azure.us)

Azure は、サブスクリプション管理 (請求、割り当て調整、アカウント振り替えなど) に無制限のサポートを提供します。 テクニカル サポートの場合はサポート プランが必要です。 詳細については、「[サポート プランの比較](https://azure.microsoft.com/support/plans)」を参照してください。

## <a name="getting-started"></a>作業の開始

Azure portal で **[ヘルプとサポート]** にアクセスできます。 これは、Azure portal メニュー、グローバル ヘッダー、またはサービスのリソース メニューから使用できます。 サポート リクエストを提出するには、適切なアクセス許可が必要です。

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Enterprise Agreement に関するサポート リクエストの作成は、エンタープライズ登録に関連付けられているエンタープライズ管理者またはパートナー管理者が行う必要があります。 

### <a name="go-to-help--support-from-the-global-header"></a>グローバル ヘッダーから [ヘルプとサポート] にアクセスする

Azure portal 内の任意の場所からサポート リクエストを開始するには、次の操作を行います。

1. グローバル ヘッダーの疑問符記号を選択し、 **[ヘルプとサポート]** を選択します。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Azure portal の [ヘルプ] メニューのスクリーンショット。":::

1. **[サポート リクエストの作成]** を選択します。 画面の指示に従って、発生している問題に関する情報を提供します。 Microsoft は考えられる解決策をいくつか提案し、問題の詳細を収集し、サポート リクエストの送信と追跡を支援します。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="[サポート リクエストの作成] リンクが表示されている [ヘルプとサポート] ページのスクリーンショット。":::

### <a name="go-to-help--support-from-a-resource-menu"></a>リソース メニューから [ヘルプとサポート] にアクセスする

サポート リクエストを開始するには:

1. リソース メニューの **[サポートとトラブルシューティング]** セクションで、 **[新しいサポート リクエスト]** を選択します。

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="リソース ウィンドウの [新しいサポート リクエスト] オプションのスクリーンショット。":::

1. 画面の指示に従って、発生している問題に関する情報を Microsoft に提供します。 リソースからサポート リクエスト プロセスを開始すると、いくつかのオプションがあらかじめ選択されています。

## <a name="create-a-support-request"></a>サポート要求の作成

Microsoft は、発生している問題について情報を収集してその解決を支援するためのいくつかの手順について説明します。 各手順については、次のセクションで説明します。

### <a name="problem-description"></a>問題の説明

1. 問題の概要を入力して、 **[問題の種類]** を選択します。 
1. **[問題の種類]** の一覧で、EA ポータル関連の問題の **[加入契約の管理]** を選択します。  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="[加入契約の管理] の選択を示すスクリーンショット。" lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::
1. **[加入契約番号]** で、加入契約番号を選択します。 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="[加入契約番号] の選択を示すスクリーンショット。" :::
1. **[問題の種類]** で、発生している問題の種類に最も適した問題カテゴリを選択します。  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="[問題の種類] の選択を示すスクリーンショット。" :::
1. **[問題のサブタイプ]** で、問題のサブカテゴリを選択します。 

これらの詳細をすべて指定したら、 **[次: ソリューション]** を選択します。

### <a name="recommended-solution"></a>推奨される解決策

提供された情報に基づいて、問題の解決を試みるために使用できる推奨される解決策が提示されます。 場合によっては、クイック診断を実行することもあります。 ソリューションは Azure エンジニアによって作成されたものであり、大抵の一般的な問題はこれで解決します。

それでも問題を解決できない場合は、 **[次: 詳細]** を選択してサポート リクエストの作成を続けてください。

### <a name="additional-details"></a>追加情報

次に、Microsoft は問題に関する詳細をさらに収集します。 この手順で徹底的かつ詳細な情報が提供していただくことで、Microsoft はお客様のサポート リクエストを適切なエンジニアに転送することができます。

1. [詳細] タブで、お客様の問題に関する詳細情報を確認できるように **[問題の詳細]** セクションをすべて入力します。 可能であれば、問題が発生した時期と、再現手順をお知らせください。 ログ ファイルや診断からの出力などのファイルをアップロードできます。 ファイルのアップロードの詳細については、「[ファイルのアップロードのガイドライン](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines)」を参照してください。

1. **[診断情報の共有]** セクションで、 **[はい]** または **[いいえ]** を選択します。 **[はい]** を選択することで、Azure サポートで Azure リソースから [診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)を収集できるようになります。 この情報を共有しない場合は、 **[いいえ]** を選択します。 場合によっては、オプションをさらに選択する必要があります。

1. **[サポート方法]** セクションで、問題の重大度を選択します。 最高重大度は[サポート プラン](https://azure.microsoft.com/support/plans)ごとに異なります。

1. 希望する連絡方法、ご都合のよい時間、および希望するサポート言語を指定します。

1. 次に、 **[連絡先情報]** セクションに入力して、ご自分への連絡方法をお知らせください。  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="[詳細] タブのスクリーンショット。" lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

必要なすべての情報の入力が完了したら、 **[次: 確認および作成]** を選択します。

### <a name="review--create"></a>確認と作成

リクエストを作成する前に、サポートに送信する詳細情報をすべて確認します。 変更が必要な場合は、 **[前へ]** を選択して任意のタブに戻ることができます。 完了したサポート リクエストが適切であれば、 **[作成]** を選択します。

サポート エンジニアが、指定された方法を使用してお客様に連絡します。 初回の応答時間については、「[サポート内容と応答性](https://azure.microsoft.com/support/plans/response/)」を参照してください。

## <a name="cant-create-request-with-microsoft-account"></a>Microsoft アカウントで要求を作成できない

Microsoft アカウント (MSA) を持っている場合は、Azure サポート チケットを作成できません。 Microsoft アカウントは、Outlook、Windows Live、Hotmail などのサービス用に作成されます。

Azure サポート チケットを作成するには、"*組織アカウント*" に EA 管理者の役割またはパートナー管理者の役割が必要です。

MSA を持っている場合は、管理者に組織アカウントの作成を依頼してください。 その後、エンタープライズ管理者またはパートナー管理者は、依頼者の組織アカウントをエンタープライズ管理者またはパートナー管理者として追加する必要があります。 その後は、組織アカウントを使用してサポート リクエストを提出できます。

- エンタープライズ管理者を追加するには、「[別のエンタープライズ管理者を作成する](ea-portal-administration.md#create-another-enterprise-administrator)」を参照してください。
- パートナー管理者を追加するには、「[パートナー管理者の管理](ea-partner-portal-administration.md#manage-partner-administrators)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリンクを参照してください。

* [Azure サポート リクエストを管理する方法]()../../azure-portal/supportability/how-to-manage-azure-support-request.md)
* [Azure サポート チケット REST API](/rest/api/support)
* [Twitter](https://twitter.com/azuresupport) でのご協力
* [Microsoft Q&A 質問ページ](/answers/products/azure)で同僚からのヘルプを得る
* [Azure サポートに関する FAQ で詳細を確認する](https://azure.microsoft.com/support/faq)