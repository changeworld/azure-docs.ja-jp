---
title: "Azure での潜在顧客の獲得 | Microsoft ドキュメント"
description: "この記事では、Microsoft 潜在顧客創出に関するすべてと、CRM システムをつなげてクラウド パートナー ポータルを使用した販売パイプラインに統合する方法について説明します。"
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6207695ede6c01f9423ddde7467d2a9b1ed35bd3
ms.lasthandoff: 04/19/2017


---

# <a name="get-customer-leads"></a>潜在顧客の獲得 
顧客は、常に優良ビジネスの中心にあります。 今日の製品購入の変革の中で、マーケティング担当者は顧客と直接つながること、関係を築くことに専念する必要があります。 これが、良質の潜在顧客を創出することが販売サイクルにとって重要なツールである理由です。
クラウド パートナー ポータルにより、顧客が自社の商品に関心を示したり導入した後**すぐ**、顧客問い合わせ情報をプログラムで受け取ることが可能になりました。 このドキュメントでは、これらの創出された潜在顧客、これらを自社の CRM システムに接続する方法、販売パイプラインに統合する方法に関して知っておく必要があることを説明します。

## <a name="what-are-these-leads"></a>潜在顧客とは何か
潜在顧客とは、自社製品を Marketplace からデプロイしている顧客のことです。 製品の一覧が [Azure Marketplace](https://azuremarketplace.microsoft.com/) にある場合も [AppSource](https://appsource.com/) にある場合も、製品に関心を持つ顧客のリードを受け取れるようになります。
### <a name="azure-marketplace"></a>Azure Marketplace
1.    顧客が自社のプランの「体験版」を使用しました。 体験版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。 すべての体験版が、自社製品を試して詳しく知ることに関心を持った潜在顧客を創出します。  体験版の詳細は、[Azure Marketplace 体験版](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)をご覧ください。

    ![体験版プラン](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

2.    顧客は、[今すぐ入手する] ボタンをクリックすると、情報を共有することに同意します。 この潜在顧客は最初の関心リードです。ここでは、製品を入手することに関心を示した顧客が共有され、取得ファンネルの最上位になります。
 
    ![[今すぐ入手する] ボタン](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.    顧客は、製品を正式に購入するとき、[Azure Portal](https://portal.azure.com/) で [購入] をクリックします。 このリードはアクティブ リードです。この場合、Azure サブスクリプションを持ち、実際の製品のデプロイを開始した顧客を共有します。

    ![テキスト購入ボタン](./media/cloud-partner-portal-get-customer-leads/purchase-button.png) 

### <a name="appsource"></a>AppSource
1.    顧客が自社のプランの「体験版」を使用しました。 体験版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。 すべての体験版が、自社製品を試して詳しく知ることに関心を持った潜在顧客を創出します。  体験版の詳細は、[AppSource 体験版](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)をご覧ください。

    ![体験版プラン](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.    顧客は、[今すぐ入手する] ボタンをクリックすると、情報を共有することに同意します。 このリードは最初の関心リードです。この場合、製品を入手することに関心を示した顧客が共有され、取得ファンネルの最上位になります。

    ![[今すぐ入手する] ボタン](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)

3.    顧客がプランで [Contact me (お問い合わせ)] ボタンをクリックします。 このリードはアクティブ リードです。この場合、製品に関するフォローアップを積極的に求めた顧客を共有します。

    ![お問い合わせ](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

## <a name="lead-data"></a>リードのデータ
パートナーが受け取る各リードには、次のフィールドで渡されるデータがあります。 各顧客の製品入手過程に沿った複数のステップからリードを取得するので、リードに対応する最良の方法は、フォローアップの重複を排除し、フォローアップをパーソナライズすることです。 これにより、顧客はそれぞれ適切なメッセージを受け取ることができ、常に独自の関係性を築くことができます。

|フィールド名|例|
|----------|--------|
|LeadSource| 形式は、**Source**-**Action**&#124;**Offer** <br/><br/>**Sources**:<br/>“AzureMarketplace”、<br/>“AzurePortal”、<br/>“TestDrive”、 <br/>"SPZA" (AppSource の頭字語)<br/><br/>**Actions**:<br/>“INS” – インストールを表します。 Azure Marketplace または AppSource で、顧客が製品を入手するボタンをクリックしたときに適用されます。<br/><br/>"PLT" - パートナー主導の試用版を表します。 AppSource で顧客が [Contact me (お問い合わせ)] をクリックしたときに適用されます。<br/><br/>"DNC" - 連絡不要を表します。 AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたときに適用されます。 この顧客がアプリでクロスリストされたことが通知されますが、顧客に連絡する必要はありません。<br/><br/>"Create" - Azure Portal 内でのみ適用されます。顧客がアカウントにプランを購入したときに適用されます。<br/><br/>"StartTestDrive" - 体験版専用です。顧客が体験版を起動したときに適用されます。<br/><br/>**プラン**:<br/>    “checkpoint.check-point-r77-10sg-byol”,<br/>“bitnami.openedxcypress”,<br/>“docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a”<br/> <br/> これらは、パートナーとパートナーの特定のプランに割り当てられた一意の識別子です。 |
|CustomerInfo|    {<br/>"FirstName":"John",<br/>"LastName":"Smith",<br/>"Email":"jsmith@microsoft.com",<br/>"Phone":"1234567890",<br/>"Country":"US",<br/>"Company":"Microsoft",<br/>"Title":"CTO"<br/>}<br/><br/>注意: すべてのデータが各リードで有効なわけではありません|


Microsoft はリードの強化に積極的に取り組んでいます。ここに記載されたデータ フィールド以外に希望のデータ フィールドがある場合は、[フィードバックをお送りください](mailto:AzureMarketOnboard@microsoft.com)。

## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>CRM システムをクラウド パートナー ポータルと接続する方法 
リードの取得を開始するために、クラウド パートナー ポータルにリード管理コネクタを構築して、CRM 情報を簡単に組み込むことができるようにしました。接続は Microsoft が行います。 これにより、外部システムと統合するために多くのエンジニアリング作業を行わなくても、Marketplace で獲得したリードを簡単に活用できるようになります。
 
![リード管理コネクタ](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png) Microsoft はさまざまな CRM システムにリードを書き込むことができます。また、Azure ストレージ テーブルに直接書き込むこともできます。パートナーは書き込まれたリードを自由に管理できます。 リードの使用可能な各送信先を接続する手順を次に示します。<todo use updated markdown files as links from appsource/testdrive! Rewalk through the lead connections Non json just fields required now in separate pages>

*    Dynamics CRM Online - Dynamics CRM Online を構成してリードを取得する手順については、[ここをクリック](./cloud-partner-portal-lead-management-instructions-dynamics.md)してください。 
*    Marketo - Marketo Lead Configuration を設定してリードを取得する手順については、[ここをクリック](./cloud-partner-portal-lead-management-instructions-marketo.md)してください。
*    Salesforce - Salesforce インスタンスを設定してリードを取得する手順については、[ここをクリック](./cloud-partner-portal-lead-management-instructions-salesforce.md)してください。
*    Azure テーブル - Azure テーブルにリードを取得するための Azure ストレージ アカウントを設定する手順については、[ここをクリック](./cloud-partner-portal-lead-management-instructions-azure-table.md)してください。 

リードの送信先を適切に構成し、プランで [発行] をクリックすると、接続が検証され、テスト リードが送信されます。 公開前にプランを確認したときに、プレビュー環境でプランを自分で入手してみることで、リードの接続をテストすることもできます。  リードが失われないように、リード設定を最新の状態に保つことが重要です。そのため、パートナー側で変更を加えるたびに、これらの接続を必ず更新してください。

## <a name="what-next"></a>次のステップ 
技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込む必要があります。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに強い関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。
Marketplace Leads によって販売チームがより大きな成果を挙げることができるようにするための[フィードバック](mailto:AzureMarketOnboard@microsoft.com)や提案に関心がある場合は、ぜひお知らせください。




