---
title: "Azure Portal での Power BI Embedded 容量の作成 | Microsoft Docs"
description: "この記事では、Microsoft Azure で Power BI Embedded 容量を作成する手順について説明します。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 1902e5c18cd7083ceeda79e6b9e779e4baaf175a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Azure Portal での Power BI Embedded 容量の作成

この記事では、Microsoft Azure で Power BI Embedded 容量を作成する手順について説明します。 Power BI Embedded は、魅力的なビジュアル、レポート、ダッシュボードをアプリにすばやく追加できるようにすることで、Power BI の機能が簡素化されます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>開始する前に

このクイック スタートを完了するには、次のものが必要です。

* **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/free/)にアクセスしてアカウントを作成します。
* **Azure Active Directory**: サブスクリプションは、Azure Active Directory (AAD) テナントに関連付けられている必要があります。 また、"***そのテナントのアカウントを使用して Azure にサインインしている必要があります***"。 Microsoft アカウントはサポートされていません。 詳細については、認証とユーザーのアクセス許可に関するページを参照してください。
* **Power BI テナント**: AAD テナント内の少なくとも 1 つのアカウントは Power BI にサインアップ済みである必要があります。
* **リソース グループ**: 既存のリソース グループを使用するか、[新しいリソース グループを作成](../azure-resource-manager/resource-group-overview.md)します。

## <a name="create-a-capacity"></a>容量の作成

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[+ (新規)]** > **[データ + 分析]** の順に選択します。

3. 検索ボックスで「*Power BI Embedded*」を検索します。

4. [Power BI Embedded] 内で、**[作成]** を選択します。

5. 必要な情報を入力し、**[作成]** を選択します。

    ![新しい容量を作成するために入力するフィールド](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |設定 |Description |
    |---------|---------|
    |**リソース名**|容量を識別する名前。 このリソース名は、Azure Portal に加え、Power BI 管理ポータル内に表示されます。|
    |**サブスクリプション**|容量作成の対象とするサブスクリプション。|
    |**[リソース グループ]**|この新しい容量が含まれるリソース グループ。 既存のリソース グループから選択するか、別のリソース グループを作成します。 詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。|
    |**Power BI 容量管理者**|Power BI 容量管理者は、Power BI 管理ポータルで容量を確認したり、他のユーザーに割り当てのアクセス許可を付与したりすることができます。 既定では、容量管理者はご自身のアカウントです。 容量管理者は、Power BI テナントに含まれている必要があります。|
    |**場所**|ご利用のテナントの Power BI がホストされている場所。 この設定は自動的に解決されます。別の場所を選択することはできません。|
    |**[価格レベル]**|ニーズに合った SKU (仮想コア数とメモリ サイズ) を選択します。  詳細については、「[Power BI Embedded の価格](https://azure.microsoft.com/pricing/details/power-bi-embedded/)」を参照してください。|

6. **[作成]**を選択します。

通常、作成にかかる時間は 1 分未満であり、たいていはわずか数秒で終わります。 **[ダッシュボードにピン留めする]** を選択した場合は、ダッシュボードに移動して新しい容量を確認します。 または、**[その他のサービス]** > **[Power BI Embedded]** の順に移動して、容量の準備ができているかどうかを確認します。

![Power BI Embedded 容量が表示された Azure Portal ダッシュボード](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>次のステップ

Power BI Embedded の新しい容量を使用するには、Power BI 管理ポータルを参照してワークスペースを割り当てます。 詳細については、「[Power BI Premium および Power BI Embedded の容量を管理する](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)」を参照してください。

この容量を使用する必要がない場合は、一時停止すると課金が停止されます。 詳細については、「[Pause and start your Power BI Embedded capacity in the Azure portal (Azure Portal での Power BI Embedded 容量の一時停止と開始)](pause-start.md)」を参照してください。

アプリケーション内に Power BI コンテンツの埋め込みを始めるには、[Power BI ダッシュボード、レポート、およびタイルを埋め込む方法](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)に関するページを参照してください。

ご質問は、 [Power BI コミュニティで質問してみてください](http://community.powerbi.com/)。