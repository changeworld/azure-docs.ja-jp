---
title: Microsoft Power BI Embedded を使ってみる | Microsoft Docs
description: Power BI Embedded をビジネス インテリジェンス アプリケーションに埋め込みます
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 7b604f9a26fc4c9a2c76a28ca01d066fe1640718
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded の概要

**Power BI Embedded** は、独立系ソフトウェア ベンダー (ISV) および開発者が自分たちのアプリケーションに魅力的なビジュアル、レポート、ダッシュボードをすばやく追加できるサービスで、容量と利用時間に基づく課金モデルを採用しています。

![埋め込みフローの図](media/get-started/introduction.png)

Power BI Embedded は、ISV、その開発者、お客様のいずれにとっても利益をもたらすものです。 たとえば、ISV であれば、Power BI Desktop を使って無料でビジュアルの作成を始めることができます。 このため、ビジュアル分析機能の開発にかかる労力を最小化して市場投入までの時間を短縮できます。また、データに関するエクスペリエンスを差別化して競合をリードすることも可能になります。 さらに、埋め込んだ分析機能により生まれる新たな価値について料金を請求することもできます。

開発者であれば、ビジュアルや分析の開発に時間を費やすことなく、アプリケーションの核となる部分の開発に集中できるようになります。 また、レポートやダッシュボードに関するお客様からの要求にすばやく対応し、ドキュメントが充実した API と SDK を使って簡単に埋め込むこともできます。 最後に、アプリに操作がわかりやすいデータ探索機能が実現すると、ISV のお客様は、デバイスを問わずデータと文脈に基づいた意思決定をすばやく、かつ自信を持って下すことができるようになります。

## <a name="register-an-application-within-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録する

カスタム アプリケーションへの埋め込みにあたっては、Azure Active Directory (AAD) に登録されたアプリケーションが必要です。 登録済みのアプリケーションは、テナントが Power BI テナントになっている必要があります。 Power BI テナントとは、組織内のユーザーが少なくとも 1 名、Power BI にサインアップしていることを指します。 Power BI にサインアップしているユーザーがいると、登録済みのアプリケーションで Power BI API が表示されるようになります。

アプリケーションを AAD に登録する方法の詳細については、「[Azure AD アプリを登録して Power BI コンテンツを埋め込む](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)」を参照してください。

## <a name="embed-content-in-your-application"></a>アプリケーションにコンテンツを埋め込む

AAD にアプリケーションを登録したら、アプリケーションに Power BI コンテンツを埋め込みます。 コンテンツを埋め込むときは、REST API と JavaScript API を使用します。

作業開始に役立つサンプルをご用意しています。 サンプルのチュートリアルは、「[ダッシュボード、タイル、レポートをアプリケーションに統合する](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)」を参照してください。

## <a name="get-capacity-and-move-to-production"></a>容量を取得して運用環境に移行する

アプリケーションを運用環境に移行するには、Microsoft Azure に Power BI Embedded の容量を作成します。 容量を作成する方法については、「[Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Azure Portal で Power BI Embedded の容量を作成する)」を参照してください。

> [!IMPORTANT]
> 埋め込みトークンは開発テストだけを目的としているため、Power BI のマスター アカウントが生成できる埋め込みトークンの数には制限があります。 実稼働の埋め込みシナリオのためには、[容量を購入する必要があります](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical)。 容量を購入した場合、埋め込みトークンの生成に制限はありません。

容量は Power BI 管理ポータルで管理します。 アプリのワークスペースについて支援が必要な場合には、ワークスペースの割り当て担当者を割り当てます。 詳細については、「[Power BI Premium および Power BI Embedded の容量を管理する](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)」を参照してください。

## <a name="next-steps"></a>次の手順

Power BI Embedded の容量を作成する準備ができたら、「[Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Azure Portal で Power BI Embedded の容量を作成する)」を参照してください。

サンプルのチュートリアルが必要な場合には、「[ダッシュボード、タイル、レポートをアプリケーションに統合する](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)」を参照してください。

ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。
