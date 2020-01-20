---
title: データとストレージに関する推奨事項 - Azure Security Center
description: このドキュメントでは、Azure Security Center での推奨事項に従ってデータと Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552867"
---
# <a name="protect-azure-data-and-storage-services"></a>Azure のデータ サービスとストレージ サービスを保護する
Azure Security Center によって潜在的なセキュリティの脆弱性が識別されると、リソースを堅牢化および保護するために必要な管理を構成するプロセスを説明する推奨事項が作成されます。

この記事では、Security Center のリソース セキュリティ セクションの **[データのセキュリティ]** ページについて説明します。

このページに表示される可能性のある推奨事項の完全な一覧については、「[データとストレージに関する推奨事項](recommendations-reference.md#recs-datastorage)」を参照してください。


## <a name="view-your-data-security-information"></a>データのセキュリティ情報を表示する

1. **[リソース セキュリティの検疫]** セクションで、 **[Data and storage resources] (データと記憶域のリソース)** をクリックします。

    ![データと記憶域のリソース](./media/security-center-monitoring/click-data.png)

    **[Data security] (データ セキュリティ)** ページとデータ リソースの推奨事項が開きます。

    [![データ リソース](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    このページからは、次のことを行うことができます。

    * **[概要]** タブをクリックすると、修復するすべてのデータ リソースの推奨事項が一覧表示されます。 
    * 各タブをクリックし、リソースの種類ごとに推奨事項を表示します。

    > [!NOTE]
    > ストレージの暗号化の詳細については、「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」を参照してください。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>データ リソースの推奨事項を修復する

1. いずれかのリソース タブから、リソースをクリックします。 情報ページが開き、修復する推奨事項の一覧が表示されます。

    ![リソース情報](./media/security-center-monitoring/sql-recommendations.png)

2. 推奨事項をクリックします。 推奨事項ページが開き、推奨事項を実装する **[修復手順]** が表示されます。

   ![修復手順](./media/security-center-monitoring/remediate1.png)

3. **[アクションの実行]** をクリックします。 リソースの設定ページが表示されます。

    ![推奨事項を有効にする](./media/security-center-monitoring/remediate2.png)

4. **[修復手順]** に従い、 **[保存]** をクリックします。


## <a name="next-steps"></a>次のステップ

その他の Azure リソースの種類に適用される推奨事項の詳細については、次のトピックをご覧ください。

* [Azure Security Center のセキュリティに関する詳細な参照リスト](recommendations-reference.md)
* [Azure Security Center でのマシンとアプリケーションの保護](security-center-virtual-machine-protection.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)