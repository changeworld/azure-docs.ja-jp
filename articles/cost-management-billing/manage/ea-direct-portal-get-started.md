---
title: ダイレクト Enterprise Agreement のお客様向けの Azure portal の概要
description: この記事では、Azure ダイレクト Enterprise Agreement (Azure EA) のお客様が Azure portal を使用する方法について説明します。
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: bf238f43bca00a4f797f2acc65dc05a7e44030d4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718509"
---
# <a name="get-started-with-the-azure-portal-for-direct-enterprise-agreement-customers"></a>ダイレクト Enterprise Agreement のお客様向けの Azure portal の概要

この記事は、ダイレクト Azure Enterprise Agreement (Azure EA) のお客様が [Azure portal](https://portal.azure.com) を使い始めるときに役立ちます。 次のことに関する基本的な情報を得られます。

- Azure portal で Enterprise 課金アカウントを管理するために使用されるロール。
- サブスクリプションの作成。
- Azure portal でのコスト分析。

ダイレクト Enterprise Agreement の Azure portal の概要について説明するビデオがいくつか用意されています。 [Azure portal でのダイレクト Enterprise のお客様の請求エクスペリエンス](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) シリーズをご覧ください。

最初のビデオを次に示します。

>[!VIDEO https://www.youtube.com/embed/sfJyTZZ6RxQ]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>EA のユーザー ロールとユーザー階層の概要

組織の使用量や支出を管理しやすいように、Azure Enterprise Agreement (EA) 管理者は次の異なる管理ロールを割り当てることができます。

- エンタープライズ管理者
- エンタープライズ管理者 (読み取り専用)
- 部門管理者
- 部門管理者 (読み取り専用)
- アカウント所有者

各ロールには、さまざまなレベルのユーザー制限とアクセス許可があります。 詳細については、「[組織の構造とロール別のアクセス許可](understand-ea-roles.md#organization-structure-and-permissions-by-role)」を参照してください。

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>登録のアクティブ化、サブスクリプションの作成、その他の管理タスク

登録のアクティブ化、部署またはサブスクリプションの作成、管理者とアカウント所有者の追加、およびその他の管理タスクについて詳しくは、[Azure ダイレクト EA の管理](direct-ea-administration.md)に関する記事をご覧ください。

エンタープライズ サブスクリプションを従量課金制サブスクリプションに変更する方法の詳細については、「[Azure エンタープライズ転送](ea-transfers.md)」を参照してください。

## <a name="view-your-enterprise-department-and-account-lists"></a>エンタープライズの部署とアカウントの一覧を表示する

課金アカウント内のすべての部署とアカウントを表示する場合は、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで **[課金スコープ]** を選択し、課金アカウントのスコープを選択します。
1. ナビゲーション メニューで、 **[部署]** または **[アカウント]** を選びます。

## <a name="view-usage-summary-and-download-reports"></a>使用状況の概要を表示してレポートをダウンロードする

Azure portal で、使用状況データ、消費された Azure 前払い、および他の使用量に関連する料金の概要を表示します。 料金は、加入契約のすべてのアカウントとサブスクリプションにわたって概要レベルで表示されます。
使用状況の概要、価格シート、レポートのダウンロードについては、「[使用料金を確認する](direct-ea-azure-usage-charges-invoices.md#review-usage-charges)」をご覧ください。

## <a name="view-and-download-invoice"></a>請求書の表示とダウンロード

Azure portal で Azure EA の請求書を表示してダウンロードします。 これはセルフサービス機能であり、登録の EA 管理者は請求書を管理するためのアクセス権を持っています。 請求書は料金明細を表示したものであるため、正確に記載されていることを確認する必要があります。 詳しくは、「[Azure の請求書をダウンロードまたは表示する](direct-ea-azure-usage-charges-invoices.md#download-or-view-your-azure-billing-invoice)」をご覧ください。

## <a name="view-microsoft-azure-consumption-commitment-macc"></a>Microsoft Azure の消費コミットメント (MACC) を表示する

Azure portal で Microsoft Azure の消費コミットメント(MACC) を表示して追跡します。 EA の請求アカウントの MACC をお持ちの組織の場合、開始日、終了日、残りのコミットメント、対象となる費用など、コミットメントの重要な側面を Azure portal で確認できます。 詳しくは、[MACC の概要](track-consumption-commitment.md?tabs=portal.md#track-your-macc-commitment)に関するページをご覧ください。

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-more-links-to-help-you-get-onboarded"></a>基本的なことがわかったので、オンボードに役立つその他のリンクを示します

「[Azure EA の価格](./ea-pricing-overview.md)」では、使用量の計算方法と、計算が複雑になるマイクロソフト エンタープライズ契約のさまざまな Azure サービスに対する料金の詳細について説明されています。

VM 予約インスタンスに対する Azure 予約が、エンタープライズ登録にかかる金額の節約にどのように役立つかについて知りたい場合は、「[Azure EA VM 予約インスタンス](ea-portal-vm-reservations.md)」を参照してください。


「[Azure EA 契約と修正](./ea-portal-agreements.md)」では、Azure EA 契約と修正が、Azure サービスへのアクセス、使用、および支払いの方法にどのように影響するかについて説明されています。

「[Azure Marketplace](./ea-azure-marketplace.md)」では、EA のお客様とパートナーがマーケットプレースの料金を確認し、Azure Marketplace で購入したものを有効にする方法について説明されています。

パートナーの EA 管理者が Azure EA Portal で行う一般的なタスクの説明については、「[パートナー向けの Azure EA ポータル管理](ea-partner-portal-administration.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- EA ポータルの概要に関する質問と回答については、[Cost Management + Billing に関する FAQ](../cost-management-billing-faq.yml) を参照してください。
- Azure ダイレクト Enterprise の管理者は、[ダイレクト EA の管理](direct-ea-administration.md)に関する記事を読んで、一般的な管理タスクについて学習する必要があります。
- Azure エンタープライズ ポータルの問題のトラブルシューティングに関するヘルプが必要な場合は、[Azure エンタープライズ ポータルへのアクセスのトラブルシューティング](ea-portal-troubleshoot.md)に関する記事を参照してください。