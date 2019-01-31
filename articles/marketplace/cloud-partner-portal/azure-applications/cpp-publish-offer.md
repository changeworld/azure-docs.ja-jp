---
title: Azure アプリケーション オファーを発行する - Azure Marketplace | Microsoft Docs
description: Azure Marketplace で Azure アプリケーション オファーを発行するプロセスと手順について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: 2adf07cf2337611b9136af47ce6a35b617e2e9ff
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177034"
---
# <a name="publish-azure-application-offer"></a>Azure アプリケーション オファーを公開する

**[新しいプラン]** ページに情報を指定してオファーを作成した後、オファーを発行できます。 **[発行]** を選択して、プロセスの発行を開始します。

次の図は、"Go Live" へのプランの発行処理の主な手順を示しています。

![オファー発行の手順](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>発行手順の詳しい説明

次の表は各発行手順を説明した一覧です。また、各手順を完了するための推定所要時間を示しています。  "日" 単位の見積時間は営業日として定義されており、週末と祝日を含めていません。

|  **発行ステップ**           | **Time**    | **説明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 前提条件の検証         | 15 分未満    | プラン情報とプラン設定が有効化されます。                        |
| 影響を受ける収益設定の検証 | 15 分未満  | プランの Azure リソース使用状況属性がチェックされます。             |
| 認定                  | 1 日未満     | オファーが Azure 認定チームによって分析されます。 ウイルス、マルウェア、安全性のコンプライアンス、およびセキュリティの問題についてオファーがスキャンされます。 適格性の条件がすべて満たされていることを確認するため、オファーがチェックされます。 詳細については、「[前提条件](./cpp-prerequisites.md)」を参照してください。 問題が見つかった場合、フィードバックが提供されます。 |
| 体験版の検証          | 2 時間未満   | (省略可能) 体験版が存在する場合、Microsoft では、デプロイおよび複製できることが検証されます。  |
| パッケージ化とリード生成の登録 | 1 時間未満  | オファーの技術資産が顧客の使用のためにパッケージ化され、リード システムが構成されデプロイされます。 |
|  発行元のサインオフ             |  manual    | オファーが稼働状態になる前に、最終的な発行元のレビューと確認が行われます。 オファーはプレビューでご利用いただけるようになりました。  (オファー情報の手順で) 選択されたサブスクリプション内にオファーをデプロイして、すべての要件を満たしていることを確認できます。  オファーを検証した後、オファーが次の手順に進めるように **[起動]** を選択します。 |
| Microsoft によるレビュー                | 7 - 14 日 | Microsoft はお客様の Azure アプリケーションを包括的にレビューし、問題が発見された場合はお客様にメールを送信します。  この手順の長さは、アプリケーションの複雑さ、検出された問題、その問題に対してお客様がどのくらい迅速に対応するかによって変わります。  |
| ライブ                           | 1 日未満 | オファーがリリースされ、指定されたリージョンに複製され、一般公開されます。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

 
Cloud パートナー ポータルのオファーの **[状態]** タブで発行プロセスを監視できます。

![Azure アプリ オファーの [状態] タブ](./media/offer-status-tab.png)

発行プロセスが完了した後、オファーが [Microsoft Azure Marketplace のアプリケーション カテゴリ](https://azuremarketplace.microsoft.com/marketplace/apps/)に一覧表示されます。



## <a name="errors-and-review-feedback"></a>エラーとフィードバックの確認

**[状態]** タブには、オファーの発行状態の表示に加え、エラー メッセージと **Microsoft レビュー**手順のフィードバックも表示されます。  通常、レビューの問題はプル要求 (PR) と呼ばれます。  各 PR は、問題に関する詳細を含むオンラインの Visual Studio Team Services (VSTS、[Azure DevOps](https://azure.microsoft.com/services/devops/) に名称変更) 項目にリンクされています。  次の図に、レビュー PR の参照の例を示します。  より複雑な状況では、レビューおよびサポート チームからお客様にメールが送信されることがあります。 

![レビューのフィードバックが表示される [状態] タブ](./media/status-tab-ms-review.png)

オファーの発行プロセスを続行するには、報告された各問題を解決する必要があります。  次の図は、このフィードバック プロセスと発行プロセスの関係を示しています。

![発行手順と VSTS フィードバック](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS アクセス

レビュー フィードバックで参照されている VSTS 項目を表示するには、パブリッシャーに適切な承認が付与されている必要があります。  そうしないと、新しいパブリッシャーは `401 - Not Authorized` 応答ページを受け取ります。  オファー レビュー VSTS システムへのアクセスを要求するには、次の手順を実行します。

1. 次の情報を収集します。
    - パブリッシャー名または ID
    - オファーの種類 (Azure アプリ)、オファー名、SKU ID
    - プル要求リンク。次に例を示します。`https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  この URL は、通知メッセージまたは 401 応答ページのアドレスから取得できます。
    - アクセスを付与する発行元組織の個人のメール アドレス。  これらには、Cloud パートナー ポータルでパブリッシャーとして登録するときに指定した所有者アドレスを含める必要があります。
2. サポート インシデントを作成する  Cloud パートナー ポータルのタイトル バーで **[ヘルプ]** ボタンを選択し、メニューから **[サポート]** を選択します。  既定の Web ブラウザーが起動し、Microsoft の新しいサポート インシデント ページに移動します  (場合によっては、最初にサインインする必要があります)。
3. **[問題の種類]** に **[Marketplace Onboarding]\(マーケットプレースのオンボード\)**、**[カテゴリ]** に **[Access problem]\(アクセスの問題\)** を指定し、**[Start request]\(要求の開始\)** を選択します。

    ![サポート チケットのカテゴリ](./media/support-incident1.png)

4. **[手順 1/2]** ページに連絡先情報を入力し、**[続行]** を選択します。
5. **[手順 2/2]** ページにインシデントのタイトル (`Request VSTS access` など) を指定し、最初の手順 (前述) で収集した情報を入力します。  条項を読み、同意してから、**[送信]** を選択します。

インシデントの作成に成功すると、確認ページが表示されます。  後で参照できるように確認情報を保存します。  Microsoft サポートは、数営業日以内にお客様のアクセス要求に返信します。


## <a name="next-steps"></a>次の手順

Azure アプリが発行された後は、[既存のオファーを更新](./cpp-update-existing-offer.md)して、ビジネスや技術面の要件の変更を反映することができます。 
