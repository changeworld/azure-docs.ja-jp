---
title: Azure サポート要求を作成する方法
description: サポートが必要なお客様は、Azure portal を使用して、セルフサービス ソリューションを見つけたり、サポート リクエストを作成および管理したりできます。
ms.topic: how-to
ms.custom: support-help-page
ms.date: 09/01/2021
ms.openlocfilehash: 768c778926024288b0d331b5cb6b60c8e9c16a57
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429463"
---
# <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

Azure では、サポート要求 (サポート チケットとも呼ばれる) を作成して管理できます。 この記事で説明するように、[Azure portal ](https://portal.azure.com)上で要求を作成して管理することができます。 [Azure サポート チケット REST API ](/rest/api/support)を使用して、または [Azure CLI](/cli/azure/azure-cli-support-request) を使用して、プログラムによってリクエストを作成して管理することもできます。

> [!NOTE]
> Azure portal の URL は、組織がデプロイされている Azure クラウドに固有のものです。
>
>* 商用利用の Azure portal は次のとおりです。[https://portal.azure.com](https://portal.azure.com)
>* ドイツの Azure portal は次のとおりです: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 米国政府機関向けの Azure portal は次のとおりです: [https://portal.azure.us](https://portal.azure.us)

Azure は、サブスクリプション管理 (請求、割り当て調整、アカウント振り替えなど) に無制限のサポートを提供します。 技術的なサポートについては、サポート プランが必要になります。 詳細については、「[サポート プランの比較](https://azure.microsoft.com/support/plans)」を参照してください。

## <a name="getting-started"></a>作業の開始

Azure portal で **[ヘルプとサポート]** にアクセスできます。 これは、Azure portal メニュー、グローバル ヘッダー、またはサービスのリソース メニューから使用できます。 サポート リクエストを提出するには、適切なアクセス許可が必要です。

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

サポート リクエストを作成するには、[所有者](../../role-based-access-control/built-in-roles.md#owner)か、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)か、またはサブスクリプション レベルで[サポート リクエスト共同作成者](../../role-based-access-control/built-in-roles.md#support-request-contributor)ロールが割り当てられている必要があります。 Azure Active Directory シナリオなどのサブスクリプションを使用せずにサポート リクエストを作成するには、[管理者](../../active-directory/roles/permissions-reference.md)である必要があります。

### <a name="go-to-help--support-from-the-global-header"></a>グローバル ヘッダーから [ヘルプとサポート] にアクセスする

Azure portal 内の任意の場所からサポート リクエストを開始するには、次の操作を行います。

1. **[?]** を グローバル ヘッダーで、 **[ヘルプとサポート]** を選択します。

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Azure portal の [ヘルプ] メニューのスクリーンショット。":::

1. **[サポート リクエストの作成]** を選択します。 画面の指示に従って、発生している問題に関する情報を提供します。 Microsoft は考えられる解決策をいくつか提案し、問題の詳細を収集し、サポート リクエストの送信と追跡を支援します。

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="[サポート リクエストの作成] リンクが表示されている [ヘルプとサポート] ページのスクリーンショット。":::

### <a name="go-to-help--support-from-a-resource-menu"></a>リソース メニューから [ヘルプとサポート] にアクセスする

リソースのコンテキストでサポート リクエストを開始するには、現在は次の操作を行います。

1. リソース メニューの **[サポートとトラブルシューティング]** セクションで、 **[新しいサポート リクエスト]** を選択します。

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="リソース ウィンドウの [新しいサポート リクエスト] オプションのスクリーンショット。":::

1. 画面の指示に従って、発生している問題に関する情報を Microsoft に提供します。 リソースからサポート リクエスト プロセスを開始すると、いくつかのオプションがあらかじめ選択されています。

## <a name="create-a-support-request"></a>サポート要求の作成

Microsoft は、発生している問題について情報を収集してその解決を支援するためのいくつかの手順について説明します。 各手順については、次のセクションで説明します。

### <a name="problem-description"></a>問題の説明

サポート リクエスト プロセスの最初のステップでは、問題の種類を選択します。 次に、詳細情報の入力を求められます。これは、選択した問題の種類によって異なります。 ほとんどの場合、サブスクリプションを指定し、問題の簡単な説明を入力して、問題の種類を選択する必要があります。 **[技術的]** を選択した場合は、問題に関連しているサービスを指定する必要があります。 そのサービスに応じて、 **[問題の種類]** と **[問題のサブタイプ]** の追加オプションが表示されます。

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="サポート リクエスト プロセスの [問題の説明] ステップのスクリーンショット。":::

これらの詳細をすべて指定したら、 **[次へ]** を選択します。

### <a name="recommended-solution"></a>推奨される解決策

入力された情報に基づいて、推奨される解決策が提示されます。お客様はこれを使用して、問題の解決を試みることができます。 場合によっては、クイック診断を実行することもあります。 ソリューションは Azure エンジニアによって作成されたものであり、大抵の一般的な問題はこれで解決します。

それでも問題を解決できない場合は、 **[次へ]** を選択してサポート リクエストの作成を続行してください。

### <a name="additional-details"></a>追加情報

次に、Microsoft は問題に関する追加の詳細を収集します。 この手順で徹底的かつ詳細な情報が提供していただくことで、Microsoft はお客様のサポート リクエストを適切なエンジニアに転送することができます。

1. お客様の問題に関する詳細情報を確認できるように **問題の詳細** をすべて入力します。 可能であれば、問題が発生した時期と、再現手順をお知らせください。 ログ ファイルや診断からの出力などのファイルをアップロードできます。 ファイルのアップロードの詳細については、「[ファイルのアップロードのガイドライン](how-to-manage-azure-support-request.md#file-upload-guidelines)」を参照してください。

1. **[診断情報の共有]** セクションで、 **[はい]** または **[いいえ]** を選択します。 **[はい]** を選択することで、Azure サポートで Azure リソースから[診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)を収集できるようになります。 この情報を共有しない場合は、 **[いいえ]** を選択します。 場合によっては、仮想マシンのメモリへのアクセスを許可するかどうかなど、追加のオプションを選択する必要があります。

1. **[サポート方法]** セクションで、影響の重大度を選択します。 最高重大度は[サポート プラン](https://azure.microsoft.com/support/plans)ごとに異なります。

1. 希望する連絡方法、ご都合のよい時間、および希望するサポート言語を指定します。

1. 次に、 **[連絡先情報]** セクションに入力して、ご自分への連絡方法をお知らせください。

必要なすべての情報の入力が完了したら、 **[次へ]** を選択します。

### <a name="review--create"></a>確認と作成

リクエストを作成する前に、サポートに送信する詳細情報をすべて確認します。 変更が必要な場合は、 **[前へ]** を選択して任意のタブに戻ることができます。 完了したサポート リクエストが適切であれば、 **[作成]** を選択します。

サポート エンジニアが、指定された方法を使用してお客様に連絡します。 初回の応答時間については、「[サポート内容と応答性](https://azure.microsoft.com/support/plans/response/)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure のセルフヘルプ サポート オプションの詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

詳細については、次のリンクを参照してください。

* [Azure サポート リクエストを管理する方法](how-to-manage-azure-support-request.md)
* [Azure サポート チケット REST API](/rest/api/support)
* [Twitter](https://twitter.com/azuresupport) でのご協力
* [Microsoft Q&A 質問ページ](/answers/products/azure)で同僚からのヘルプを得る
* [Azure サポートに関する FAQ で詳細を確認する](https://azure.microsoft.com/support/faq)