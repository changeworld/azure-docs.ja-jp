---
title: Azure Marketplace で IoT Edge モジュール オファーのプランを設定する
description: Azure Marketplace で IoT Edge モジュール オファーのプランを設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: f0b6ac3f704adb0070b6cb817da47174b44c8dfa
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2021
ms.locfileid: "111539935"
---
# <a name="set-up-plans-for-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーのプランを設定する

**[プランのセットアップ]** ページでは、プランが利用できるクラウドを構成できます。 このタブでの設定により、他のタブに表示されるフィールドが変わります。

## <a name="azure-regions"></a>Azure Azure リージョン

Azure グローバルまたは Azure Government を選択します。

### <a name="azure-global"></a>Azure Global

IoT Edge モジュール オファーのすべてのプランは、自動的に **Azure グローバル** で利用できるようになります。  マーケットプレースを使用する、すべてのグローバル Azure リージョンの顧客がプランを使用できます。 詳細については、「[利用可能な地域と通貨サポート](marketplace-geo-availability-currencies.md)」を参照してください。

### <a name="azure-government"></a>Azure Government

**[[Azure Government]](../azure-government/documentation-government-welcome.md)** を選択して、オファーがそこに表示されるようにします。 これはアメリカ合衆国の連邦政府顧客、州政府顧客、地方自治体顧客、部族政府顧客と、そのような顧客にサービスを提供する資格があるパートナーのための政府機関コミュニティ クラウドであり、アクセスが制御されています。 公開元はこのクラウド コミュニティのコンプライアンス制御、セキュリティ対策、ベスト プラクティスに責任を負います。 Azure Government では、物理的に離れた場所にあるデータ センターとネットワークが使用されます (場所は米国のみ)。 Azure Government に[公開](../azure-government/documentation-government-manage-marketplace-partners.md)する前に、その領域内でソリューションをテストし、確認します。これは結果が異なる場合があるためです。 ソリューションを計画し、テストするには、[Microsoft Azure 政府機関向け試用版](https://azure.microsoft.com/global-infrastructure/government/request/)に試用版アカウントを申請します。

> [!NOTE]
> プランが公開され、特定のリージョンで利用できるようになった後に、そのリージョンを削除することはできません。

#### <a name="azure-government-certifications"></a>Azure Government の認定資格

**[Azure Government]** を選択した場合は、**認定資格** を追加します。 Azure Government サービスでは、特定の政府の規制および要件の対象となるデータが処理されます。 FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS などです。 これらのプログラムの認定資格を認識させるため、認定資格について説明するリンクを 100 個まで提供することができます。 プログラムのリスト登録に直接リンクするか、独自の Web サイトにリンクできます。 これらのリンクは、Azure Government の顧客にのみ表示されます。

左側のナビゲーション メニューの **[プランの概要]** の次のタブである **[プランのリスト登録]** へ進む前に **[下書きの保存]** を選択してください。

## <a name="next-steps"></a>次のステップ

- [プランのリスト登録の設定](iot-edge-plan-listing.md)
