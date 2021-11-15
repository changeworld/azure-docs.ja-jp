---
title: Azure サポート要求を作成する方法
description: サポートが必要なお客様は、Azure portal を使用して、セルフサービス ソリューションを見つけたり、サポート リクエストを作成および管理したりできます。
ms.topic: how-to
ms.custom: support-help-page
ms.date: 10/20/2021
ms.openlocfilehash: 8c1a9e841c291bc6a7a6139b7aff4217975a0bcd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265000"
---
# <a name="create-an-azure-support-request"></a>Azure サポート要求を作成する

Azure では、サポート要求 (サポート チケットとも呼ばれる) を作成して管理できます。 この記事で説明するように、[Azure portal ](https://portal.azure.com)上で要求を作成して管理することができます。 [Azure サポート チケット REST API ](/rest/api/support)を使用して、または [Azure CLI](/cli/azure/azure-cli-support-request) を使用して、プログラムによってリクエストを作成して管理することもできます。

> [!NOTE]
> Azure portal の URL は、組織がデプロイされている Azure クラウドに固有のものです。
>
>- 商用利用の Azure portal は次のとおりです。[https://portal.azure.com](https://portal.azure.com)
>- ドイツの Azure portal は次のとおりです: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- 米国政府機関向けの Azure portal は次のとおりです: [https://portal.azure.us](https://portal.azure.us)

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

1. **[詳細診断情報]** セクションで、 **[はい]** または **[いいえ]** を選択します。 **[はい]** を選択することで、Azure サポートで Azure リソースから[詳細診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)を収集できるようになります。 この情報を共有しない場合は、 **[いいえ]** を選択します。 収集する可能性があるファイルの種類の詳細については、「[詳細診断情報ログ](#advanced-diagnostic-information-logs)」セクションを参照してください。

  場合によっては、追加のオプションを選択する必要があります。 たとえば、特定の種類の仮想マシンの問題の種類について、[仮想マシンのメモリへのアクセスを許可する](#memory-dump-collection)かどうかを選択できます。

1. **[サポート方法]** セクションで、影響の重大度を選択します。 最高重大度は[サポート プラン](https://azure.microsoft.com/support/plans)ごとに異なります。

1. 希望する連絡方法、ご都合のよい時間、および希望するサポート言語を指定します。

1. 次に、 **[連絡先情報]** セクションに入力して、ご自分への連絡方法をお知らせください。

必要なすべての情報の入力が完了したら、 **[次へ]** を選択します。

### <a name="review--create"></a>確認と作成

リクエストを作成する前に、サポートに送信する詳細情報をすべて確認します。 変更が必要な場合は、 **[前へ]** を選択して任意のタブに戻ることができます。 完了したサポート リクエストが適切であれば、 **[作成]** を選択します。

サポート エンジニアが、指定された方法を使用してお客様に連絡します。 初回の応答時間については、「[サポート内容と応答性](https://azure.microsoft.com/support/plans/response/)」を参照してください。

### <a name="advanced-diagnostic-information-logs"></a>詳細診断情報ログ

[詳細診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)の収集を許可すると、Microsoft サポートは、問題の迅速な解決に役立つ情報を収集できます。 この一覧はすべてを網羅しているわけではありませんが、さまざまなサービスまたは環境の詳細診断情報で収集される一般的なファイルの例が含まれています。

- [Microsoft Azure PaaS VM ログ](/troubleshoot/azure/virtual-machines/sdp352ef8720-e3ee-4a12-a37e-cc3b0870f359-windows-vm)
- [Microsoft Azure IaaS VM ログ](https://github.com/azure/azure-diskinspect-service/blob/master/docs/manifest_by_file.md)
- [Microsoft Azure Service Fabric ログ](/troubleshoot/azure/general/fabric-logs)
- [StorSimple サポート パッケージとデバイスログ](https://support.microsoft.com/topic/storsimple-support-packages-and-device-logs-cb0a1c7e-6125-a5a7-f212-51439781f646)
- [Azure Virtual Machines 上の SQL Server ログ](/troubleshoot/azure/general/sql-vm-logs)
- [Azure Active Directory ログ](/troubleshoot/azure/active-directory/support-data-collection-diagnostic-logs)
- [Azure Stack Edge サポート パッケージとデバイス ログ](/troubleshoot/azure/general/azure-stack-edge-support-package-device-logs)
- [Azure Synapse Analytics ログ](/troubleshoot/azure/general/synapse-analytics-apache-spark-pools-diagnostic-logs)

### <a name="memory-dump-collection"></a>メモリ ダンプの収集

特定の仮想マシン (VM) の問題のサポート ケースを作成すると、仮想マシンのメモリへのアクセスのサポートを許可するかどうかを確認するメッセージが表示されます。 そうすると、問題の診断に役立つメモリ ダンプを収集できます。

完全なメモリ ダンプは、最大のカーネル モード ダンプ ファイルです。 このファイルには、Windows で使用されるすべての物理メモリが含まれます。 完全なメモリ ダンプには、既定では、プラットフォーム ファームウェアで使用される物理メモリは含まれません。

ダンプは、同じデータセンター内でデバッグするために、計算ノード (Azure ホスト) から別のサーバーにコピーされます。 データはセキュリティで保護された Azure 境界の内側にあるため、顧客データは保護されます。

ダンプ ファイルは、VM の Hyper-V 保存状態を生成して作成されます。 これにより、VM が最大 10 分間一時停止し、その後 VM が再開されます。 VM は、このプロセスの一部として再起動されません。

## <a name="next-steps"></a>次のステップ

Azure のセルフヘルプ サポート オプションの詳細については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

詳細については、次のリンクを参照してください。

* [Azure サポート リクエストを管理する方法](how-to-manage-azure-support-request.md)
* [Azure サポート チケット REST API](/rest/api/support)
* [Twitter](https://twitter.com/azuresupport) でのご協力
* [Microsoft Q&A 質問ページ](/answers/products/azure)で同僚からのヘルプを得る
* [Azure サポートに関する FAQ で詳細を確認する](https://azure.microsoft.com/support/faq)