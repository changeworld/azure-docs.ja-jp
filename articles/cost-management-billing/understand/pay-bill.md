---
title: Microsoft Azure の請求書の支払いを行う
description: Azure portal での請求書の支払い方法について説明します。 ポータルで支払いを行うには、課金プロファイルの所有者、共同作成者、または請求書管理者である必要があります。
keywords: 請求, 支払い期限を過ぎている, 残高, 今支払う,
author: banders
ms.reviewer: judupont
tags: billing, past due, pay now, bill, invoice, pay
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 70d104853db5634e70dfbdd7bc01c1c2b8fd9589
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476150"
---
# <a name="how-to-pay-your-bill-for-microsoft-azure"></a>Microsoft Azure の請求書の支払い方法

この記事は、Microsoft 顧客契約 (MCA) をご利用のお客様を対象としています。

[Microsoft Customer Agreement にアクセスできるかどうかを確認](#check-access-to-a-microsoft-customer-agreement)してください。

Azure の請求書には 2 とおりの支払い方法があります。 課金プロファイルの既定の支払い方法を使用する方法と、 **[今支払う]** による一括払いを使用する方法です。

Microsoft 担当者を通じて Azure にサインアップした場合、既定の支払い方法が必ず "*小切手または電信送金*" に設定されています。 Microsoft 担当者を通じて Azure にサインアップした場合、クレジットカードでの自動支払いは選択できません。 その代わりに、[クレジットカードを使用して個別の請求書の支払い](#pay-now-in-the-azure-portal)を行うことはできます。

Azure クレジットがある場合は、請求期間ごとに請求書に自動的に適用されます。

## <a name="pay-by-default-payment-method"></a>既定の支払い方法を使用する

課金プロファイルの既定の支払い方法として、クレジット カード、デビット カード、小切手 (または電信送金) のいずれかを指定できます。

### <a name="credit-or-debit-card"></a>クレジット カードまたはデビット カード

課金プロファイルの既定の支払い方法がクレジット カードまたはデビット カードである場合、請求期間ごとに自動的に課金されます。

クレジット カードまたはデビット カードの自動請求がなんらかの理由によって拒否された場合は、Azure portal から **[今支払う]** を使用し、クレジット カードまたはデビット カードで一括払いすることができます。

既定の支払い方法を小切手または電信送金に変更したい場合は、[請求書による支払い方法](../manage/pay-by-invoice.md)に関するページを参照してください。

デビット カードの使用が許可されていない国がいくつかありますが、一般には、Azure の料金支払いにデビット カードを使用できます。 バーチャルおよびプリペイドのデビット カードは、Azure の料金支払いに使用できません。

### <a name="check-or-wire-transfer"></a>小切手または電信送金

課金プロファイルの既定の支払い方法が小切手または電信送金である場合は、請求書の PDF ファイルに記載の支払い手順に従ってください。

請求書の金額がご利用の通貨のしきい値を下回っている場合は、Azure portal から **[今支払う]** を使用し、クレジット カードまたはデビット カードで一括払いすることができます。 請求書の金額がしきい値を超えている場合は、クレジット カードやデビット カードを使用して請求書の支払いを行うことはできません。 該当する通貨のしきい値の金額は、Azure portal で **[今支払う]** を選択すると表示されます。

#### <a name="bank-details-used-to-send-wire-transfer-payments"></a>電信送金支払いに使用する銀行の詳細
<a name="wire-bank-details"></a>

既定の支払い方法が電信送金の場合、請求書の支払指示をチェックしてください。 次のリストから、該当する国またはリージョンの支払指示を見つけます。

> [!div class="op_single_selector"]
> - **国または地域の選択**
> - [アフガニスタン](/legal/pay/afghanistan)
> - [アルバニア](/legal/pay/albania)
> - [アルジェリア](/legal/pay/algeria)
> - [アンゴラ](/legal/pay/angola)
> - [アルゼンチン](/legal/pay/argentina)
> - [アルメニア](/legal/pay/armenia)
> - [オーストラリア](/legal/pay/australia)
> - [オーストリア](/legal/pay/austria)
> - [アゼルバイジャン](/legal/pay/azerbaijan)
> - [バハマ](/legal/pay/bahamas)
> - [バーレーン](/legal/pay/bahrain)
> - [バングラデシュ](/legal/pay/bangladesh)
> - [バルバドス](/legal/pay/barbados)
> - [ベラルーシ](/legal/pay/belarus)
> - [ベルギー](/legal/pay/belgium)
> - [ベリーズ](/legal/pay/belize)
> - [バミューダ](/legal/pay/bermuda)
> - [ボリビア](/legal/pay/bolivia)
> - [ボスニア・ヘルツェゴビナ](/legal/pay/bosnia-and-herzegovina)
> - [ボツワナ](/legal/pay/botswana)
> - [ブラジル](/legal/pay/brazil)
> - [ブルネイ](/legal/pay/brunei)
> - [ブルガリア](/legal/pay/bulgaria)
> - [カメルーン](/legal/pay/cameroon)
> - [カナダ](/legal/pay/canada)
> - [カーボベルデ](/legal/pay/cape-verde)
> - [ケイマン諸島](/legal/pay/cayman-islands)
> - [チリ](/legal/pay/chile)
> - [中国 (PRC)](/legal/pay/china-prc)
> - [コロンビア](/legal/pay/colombia)
> - [コスタリカ](/legal/pay/costa-rica)
> - [コートジボワール](/legal/pay/cote-divoire)
> - [クロアチア](/legal/pay/croatia)
> - [キュラソー島](/legal/pay/curacao)
> - [キプロス](/legal/pay/cyprus)
> - [チェコ共和国](/legal/pay/czech-republic)
> - [コンゴ民主共和国](/legal/pay/democratic-republic-of-congo)
> - [デンマーク](/legal/pay/denmark)
> - [ドミニカ共和国](/legal/pay/dominican-republic)
> - [エクアドル](/legal/pay/ecuador)
> - [エジプト](/legal/pay/egypt)
> - [エルサルバドル](/legal/pay/el-salvador)
> - [エストニア](/legal/pay/estonia)
> - [エチオピア](/legal/pay/ethiopia)
> - [フェロー諸島](/legal/pay/faroe-islands)
> - [フィジー](/legal/pay/fiji)
> - [フィンランド](/legal/pay/finland)
> - [フランス](/legal/pay/france)
> - [仏領ギアナ](/legal/pay/french-guiana)
> - [ジョージア](/legal/pay/georgia)
> - [ドイツ](/legal/pay/germany)
> - [ガーナ](/legal/pay/ghana)
> - [ギリシャ](/legal/pay/greece)
> - [グレナダ](/legal/pay/grenada)
> - [グアドループ](/legal/pay/guadeloupe)
> - [グアム](/legal/pay/guam)
> - [グアテマラ](/legal/pay/guatemala)
> - [ガイアナ](/legal/pay/guyana)
> - [ハイチ](/legal/pay/haiti)
> - [ホンジュラス](/legal/pay/honduras)
> - [香港特別行政区](/legal/pay/hong-kong)
> - [ハンガリー](/legal/pay/hungary)
> - [アイスランド](/legal/pay/iceland)
> - [インド](/legal/pay/india)
> - [インドネシア](/legal/pay/indonesia)
> - [イラク](/legal/pay/iraq)
> - [アイルランド](/legal/pay/ireland)
> - [イスラエル](/legal/pay/israel)
> - [イタリア](/legal/pay/italy)
> - [ジャマイカ](/legal/pay/jamaica)
> - [日本](/legal/pay/japan)
> - [ヨルダン](/legal/pay/jordan)
> - [カザフスタン](/legal/pay/kazakhstan)
> - [ケニア](/legal/pay/kenya)
> - [韓国](/legal/pay/korea)
> - [クウェート](/legal/pay/kuwait)
> - [キルギスタン](/legal/pay/kyrgyzstan)
> - [ラトビア](/legal/pay/latvia)
> - [レバノン](/legal/pay/lebanon)
> - [リビア](/legal/pay/libya)
> - [リヒテンシュタイン](/legal/pay/liechtenstein)
> - [リトアニア](/legal/pay/lithuania)
> - [ルクセンブルク](/legal/pay/luxembourg)
> - [マカオ](/legal/pay/macao)
> - [マケドニア・旧ユーゴスラビア共和国](/legal/pay/macedonia)
> - [マレーシア](/legal/pay/malaysia)
> - [マルタ](/legal/pay/malta)
> - [モーリシャス](/legal/pay/mauritius)
> - [メキシコ](/legal/pay/mexico)
> - [モルドバ](/legal/pay/moldova)
> - [モナコ](/legal/pay/monaco)
> - [モンゴル](/legal/pay/mongolia)
> - [モンテネグロ](/legal/pay/montenegro)
> - [モロッコ](/legal/pay/morocco)
> - [ナミビア](/legal/pay/namibia)
> - [ネパール](/legal/pay/nepal)
> - [オランダ](/legal/pay/netherlands)
> - [ニュージーランド](/legal/pay/new-zealand)
> - [ニカラグア](/legal/pay/nicaragua)
> - [ナイジェリア](/legal/pay/nigeria)
> - [ノルウェー](/legal/pay/norway)
> - [オマーン](/legal/pay/oman)
> - [パキスタン](/legal/pay/pakistan)
> - [パレスチナ自治政府](/legal/pay/palestinian-authority)
> - [パナマ](/legal/pay/panama)
> - [パラグアイ](/legal/pay/paraguay)
> - [ペルー](/legal/pay/peru)
> - [フィリピン](/legal/pay/philippines)
> - [ポーランド](/legal/pay/poland)
> - [ポルトガル](/legal/pay/portugal)
> - [プエルトリコ](/legal/pay/puerto-rico)
> - [カタール](/legal/pay/qatar)
> - [ルーマニア](/legal/pay/romania)
> - [ロシア](/legal/pay/russia)
> - [ルワンダ](/legal/pay/rwanda)
> - [セントクリストファー・ネーヴィス](/legal/pay/saint-kitts-and-nevis)
> - [セントルシア](/legal/pay/saint-lucia)
> - [セントビンセント及びグレナディーン諸島](/legal/pay/saint-vincent-and-the-grenadines)
> - [サウジアラビア](/legal/pay/saudi-arabia)
> - [セネガル](/legal/pay/senegal)
> - [セルビア](/legal/pay/serbia)
> - [シンガポール](/legal/pay/singapore)
> - [スロバキア](/legal/pay/slovakia)
> - [スロベニア](/legal/pay/slovenia)
> - [南アフリカ](/legal/pay/south-africa)
> - [スペイン](/legal/pay/spain)
> - [スリランカ](/legal/pay/sri-lanka)
> - [スリナム](/legal/pay/suriname)
> - [スウェーデン](/legal/pay/sweden)
> - [スイス](/legal/pay/switzerland)
> - [台湾](/legal/pay/taiwan)
> - [タジキスタン](/legal/pay/tajikistan)
> - [タンザニア](/legal/pay/tanzania)
> - [タイ](/legal/pay/thailand)
> - [トリニダード・トバゴ](/legal/pay/trinidad-and-tobago)
> - [トルクメニスタン](/legal/pay/turkmenistan)
> - [チュニジア](/legal/pay/tunisia)
> - [トルコ](/legal/pay/turkey)
> - [ウガンダ](/legal/pay/uganda)
> - [ウクライナ](/legal/pay/ukraine)
> - [アラブ首長国連邦](/legal/pay/united-arab-emirates)
> - [イギリス](/legal/pay/united-kingdom)
> - [米国](/legal/pay/united-states)
> - [ウルグアイ](/legal/pay/uruguay)
> - [ウズベキスタン](/legal/pay/uzbekistan)
> - [ベネズエラ](/legal/pay/venezuela)
> - [ベトナム](/legal/pay/vietnam)
> - [米領バージン諸島](/legal/pay/virgin-islands)
> - [イエメン](/legal/pay/yemen)
> - [ザンビア](/legal/pay/zambia)
> - [ジンバブエ](/legal/pay/zimbabwe)

## <a name="pay-now-in-the-azure-portal"></a>Azure portal で今支払う

Azure portal で請求書の支払いを行うには、適切な [MCA アクセス許可](../manage/understand-mca-roles.md)があるか、課金アカウントの管理者である必要があります。MCA アカウントにサインアップしたユーザー本人が、課金アカウントの管理者となります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** で検索します。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書の支払い期限来ている場合や支払い期限が過ぎた場合、その請求書に青色の **[今支払う]** リンクが表示されます。 **[今支払う]** を選択します。
1. [今支払う] ウィンドウで **[支払い方法の選択]** を選択して、既存のクレジット カードを選択するか、新しいカードを追加します。
1. 支払い方法を選択したら、 **[今支払う]** を選択します。

24 時間以内に、請求書の状態が "*支払い済み*" と表示されます。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>次の手順

- 小切手または電信送金による支払いの対象となるには、[請求書による支払い方法](../manage/pay-by-invoice.md)に関する記事を参照してください。
