---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 7705ea3b60d8c8e2b189ad555d1b53fa395aa1fb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912512"
---
Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。  これらの仮想マシン サイズは、コンプライアンスや規制上の要件などの要素に関連するワークロードについて、他の顧客からの高いレベルの分離を必要とするワークロードに最適です。  お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。

分離されたサイズを利用することで、お使いの仮想マシンがその特定のサーバー インスタンス上で実行されている唯一のマシンであることが保証されます。  現在の分離された仮想マシンのプランには、以下が含まれます。
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

利用可能な分離されたサイズごとの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)を参照してください。

## <a name="retiring-d15_v2ds15_v2-isolation-on-february-15-2020"></a>2020 年 2 月 15 日の D15_v2/DS15_v2 分離の廃止
Azure Dedicated Host の一般提供が開始されました。これを利用すると、シングル テナントの物理サーバー上で、組織の Linux と Windows の仮想マシンを実行できます。 これにより分離された Azure VM を Azure Dedicated Host と完全に置き換えることを計画しています。 **2020 年 2 月 15 日**以降、D15_v2/DS15_v2 Azure VM はハードウェア分離されなくなります。

## <a name="how-does-this-affect-me"></a>どのような影響がありますか?
2020 年 2 月 15 日以降、D15_v2/DS15_v2 Azure 仮想マシンの分離保証は提供されなくなります。 

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?
ハードウェア分離が不要な場合は、対応は不要です。 

分離が必要な場合は、2020 年 2 月 15 日より前に、次のいずれかの対応が必要です。

•   ワークロードを Azure Dedicated Host に[移行](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)します。

•   D15i_v2 と DS15i_v2 Azure VM への[アクセスを要求](https://aka.ms/D15iRequestAccess)し、同じ価格のパフォーマンスを実現します。 このオプションは、従量課金制と 1 年間の予約インスタンスのシナリオでのみ使用できます。    

•   ワークロードを別の Azure 分離仮想マシンに[移行](https://azure.microsoft.com/blog/resize-virtual-machines/)します。 

詳細については、以下を参照してください。

## <a name="timeline"></a>タイムライン
| Date | アクション | 
| --- | --- |
| 2019 年 11 月 18 日  | D/DS15i_v2 (従量課金制、1 年間の予約インスタンス) を入手可能です |
| 2020 年 2 月 14 日  | D/DS15i_v2 (1 年間の予約インスタンス) を購入できる最終日です | 
| 2020 年 2 月 15 日   | D/DS15_v2 分離保証が削除されます | 
| 2021 年 5 月 15 日  | D/DS15i_v2 が廃止されます (2019 年 11 月 18 日より前に D/DS15_v2 の 3 年間の予約インスタンスを購入したお客様を除くすべてのお客様)| 
| 2022 年 11 月 17 日   | 3 年間の予約インスタンスの完了時に D/DS15i_v2 が廃止されます (2019 年 11 月 18 日より前に D/DS15_v2 の 3 年間の予約インスタンスを購入したお客様の場合) | 

## <a name="faq"></a>よく寄せられる質問
### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Q:ポータルに新しい D/DS15i_v2 サイズが表示されないのはなぜですか?
**A**: 現在 D/DS15_v2 のお客様で、新しい D/DS15i_v2 サイズを使用する場合は、こちらの[フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u)に記入してください

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Q:新しい D/DS15i_v2 サイズのクォータが表示されないのはなぜですか?
**A**: 現在 D/DS15_v2 のお客様で、新しい D/DS15i_v2 サイズを使用する場合は、こちらの[フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u)に記入してください

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q:他の分離されたサイズはいつ廃止される予定ですか?
**A**: サイズが正式に廃止される 12 か月前にリマインダーを提供します。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q:分離されていないハードウェアに VM がある場合、ダウンタイムは発生しますか?
**A**: 分離が不要な場合は、何もする必要はありません。また、ダウンタイムは発生しません。

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Q:分離されていない仮想マシンに移行する場合、コストの変更はありますか?
**A**: いいえ 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Q:D15_v2 または Ds15_v2 の 1 年間または 3 年間の予約インスタンスを既に購入しています。 VM の使用量に割引はどのように適用されますか?
**A**: 2019 年 11 月 18 日より前に購入した予約インスタンスは、新しい分離された VM シリーズに自動的に拡張されます。 

| 予約インスタンス |  インスタンス サイズの柔軟性 | 特典の対象 |   
| --- | --- | --- |
|   D15_v2  |   Off     |   D15_v2 と D15i_v2 |    
|   D15_v2  |   On  |   D15_v2 シリーズと D15i_v2 のすべてが予約インスタンス特典を受けられます。 |    
|   D14_v2  |   On  |   D15_v2 シリーズと D15i_v2 のすべてが予約インスタンス特典を受けられます。 |    
 
Dsv2 シリーズの場合も同様です。
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Q:Dv2 の予約インスタンスの追加購入を検討しています。 どれを選択すればよいですか?
**A**: 2019 年 11 月 18 日以降に購入されたすべての予約インスタンスの動作は次のようになります。 

| 予約インスタンス |  インスタンス サイズの柔軟性 | 特典の対象 |   
| --- | --- | --- |
| D15_v2 |  Off |   D15_v2 のみ  
| D15_v2 |  On |    D15_v2 シリーズは予約インスタンス特典を受けられます。 新しい D15i_v2 は、この予約インスタンスの種類の予約インスタンス特典の対象になりません。 | 
| D15i_v2 |     Off | D15i_v2 のみ |  
| D15i_v2 |     On  | D15i_v2 のみ | 
 
インスタンス サイズの柔軟性を使用して、D2_v2、D4_v2、D15_v2 などの他のサイズに適用することはできません。 Dsv2 シリーズの場合も同様です。  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Q:D15i_v2 と DS15i_v2 のために新しい 3 年間の予約インスタンスを購入できますか?
**A**: 残念ながら、新規購入できるのは 1 年間の予約インスタンスのみです。
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Q:既存の D15_v2/DS15_v2 予約インスタンスを、分離されたサイズの予約インスタンスに移行できますか?
**A**: この特典は分離サイズと分離されていないサイズの両方に適用されるため、必要ありません。 ただし、Azure は既存の D15_v2/DS15_v2 予約インスタンスから D15i_v2/DS15i_v2 への変更をサポートしています。 他のすべての Dv2/Dsv2 予約インスタンスについては、既存の予約インスタンスを使用するか、分離されたサイズの新しい予約インスタンスを購入します。

### <a name="q-im-a-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q:シルバーまたはゴールドの持続性層を利用している Azure Service Fabric ユーザーです。 この変更の影響はありますか?
**A**: いいえ。 Service Fabric の[持続性層](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)で提供されている保証は、この変更の後も引き続き機能します。 その他の理由で物理的なハードウェアの分離が必要な場合、上記のいずれかのアクションを実行する必要があります。 
