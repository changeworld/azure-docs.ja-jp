---
title: 医療向けの名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599317"
---
[Text Analytics for Health](../../how-tos/text-analytics-for-health.md) では、構造化されていない医療データから分析情報を処理および抽出します。 このサービスでは、医療の概念を検出して明らかにし、アサーションを概念に割り当て、概念間の意味の関係を推測し、それらを一般的な医療オントロジにリンクします。

Text Analytics for Health を使用すると、次のカテゴリの医療概念が検出されます。 このプレビューでは英語のテキストのみがサポートされており、1 つのモデル バージョンのみが使用可能です。

| カテゴリ  | 説明  |
|---------|---------|
| [ANATOMY](#anatomy) | 身体および解剖学的な系統、部位、位置、または領域に関する情報を取得するための概念。 |
 | [DEMOGRAPHICS](#demographics) | 性別と年齢に関する情報を取得するための概念。 |
 | [EXAMINATION](#examinations) | 診断手順と検査に関する情報を取得するための概念。 |
 | [GENERAL ATTRIBUTES](#general-attributes) | 上記のカテゴリの他の概念についての詳細情報を提供する概念。 |
 | [GENOMICS](#genomics) | 遺伝子と変異に関する情報を取得するための概念。 |
 | [HEALTHCARE](#healthcare) | 投薬イベント、医療環境、医療関係職に関する情報を取得するための概念。 |
 | [MEDICAL CONDITION](#medical-condition) | 診断、症状、兆候に関する情報を取得するための概念。 |
 | [MEDICATION](#medication) | 薬の名前、クラス、投薬量、投与方法など、薬に関する情報を取得するための概念。 |
 | [SOCIAL](#social) | 家族関係など、医療関連の社会的な側面に関する情報を取得するための概念。 |
 | [TREATMENT](#treatment) | 治療手段に関する情報を取得するための概念。 |

詳細と例については、以下をご覧ください。

## <a name="anatomy"></a>構造

### <a name="entities"></a>エンティティ

**BODY_STRUCTURE** - 身体の系統、解剖学的な位置または領域、身体の部位。 たとえば、腕、膝、腹部、鼻、肝臓、頭部、呼吸器系、リンパ球など。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="身体構造エンティティの例。":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="身体構造エンティティの拡張された例。":::

## <a name="demographics"></a>人口統計データ

### <a name="entities"></a>エンティティ

**AGE** - 患者や家族などを含む、年齢に関するすべての用語と語句。 たとえば、40 歳、51 才、3 か月、成人、乳児、初老、若者、未成年、中年など。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="年齢エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="年齢エンティティの別の例。":::


**GENDER** - 対象者の性別を示す用語。 たとえば、男性、女性、女、紳士、淑女など。

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="性別エンティティの例。":::

## <a name="examinations"></a>検査

### <a name="entities"></a>エンティティ

**EXAMINATION_NAME** - バイタル サインや身体測定を含む、診断手順と検査。 たとえば、MRI、ECG、HIV 検査、ヘモグロビン、血小板数、"*ブリストル便性状スケール*" などのスケール システム。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="検査エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="検査名エンティティのもう 1 つの例。":::

## <a name="general-attributes"></a>一般的な属性

### <a name="entities"></a>エンティティ

**DATE** - 病状、検査、治療、薬、または投薬イベントに関連する完全な日付。

**DIRECTION** - 身体の構造、病状、検査、または治療に関係する可能性のある、左、横、上、後部などの方向関連の用語。

**FREQUENCY** - 病状、検査、治療、または投薬の発生頻度 (過去、現在、未来) を示します。

**MEASUREMENT_VALUE** - 検査または病状の測定に関連する値。

**MEASUREMENT_UNIT** - 検査または病状の測定に関連する測定単位。

**RELATIONAL_OPERATOR** - エンティティと追加情報の間の定量的関係を表す語句。

**TIME** - 病状、検査、治療、薬、または投薬イベントの開始や長さ (期間) に関する時間関連の用語。 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>エンティティ

**GENE_OR_PROTEIN** - 人間の遺伝子、染色体、染色体とタンパク質の一部の名前と記号に関するすべての言及。 たとえば、MTRR、F2 など。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="遺伝子エンティティの例。":::

**VARIANT** - 遺伝子のバリエーションと変異に関するすべての言及。 たとえば、`c.524C>T`、`(MTRR):r.1462_1557del96` など
  
## <a name="healthcare"></a>医療

### <a name="entities"></a>エンティティ
  
**ADMINISTRATIVE_EVENT** – 医療システムに関連するが、管理または半ば管理の性質を持つイベント。 たとえば、登録、受付、試験、研究エントリ、移植、退院、入院など。 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="医療イベント エンティティの例。":::

**CARE_ENVIRONMENT** – 患者が治療を受ける環境または場所。 たとえば、緊急救命室、医師の診察室、有酸素運動ユニット、ホスピス、病院など。

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="このスクリーンショットは、医療環境エンティティの例を示しています。":::

**HEALTHCARE_PROFESSION** – 有資格または無資格の医療関係者。 たとえば、歯科医、病理学者、神経科医、放射線科医、薬剤師、栄養士、理学療法士、カイロプラクターなど。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="このスクリーンショットは、医療環境エンティティの別の例を示しています。":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="医療環境エンティティの別の例。":::

## <a name="medical-condition"></a>病状

### <a name="entities"></a>エンティティ

**DIAGNOSIS** – 病気、症候群、中毒。 たとえば、乳がん、アルツハイマー病、HTN、CHF、脊髄損傷など。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="病状エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="病状エンティティの別の例。":::

**SYMPTOM_OR_SIGN** – 病気またはその他の診断の主観的または客観的な証拠。 たとえば、胸痛、頭痛、目まい、発疹、SOB、柔らかい腹部、正常な腸音、栄養良好など。

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="病状の兆候または症状エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="病状の兆候または症状エンティティの別の例。":::

**CONDITION_QUALIFIER** - 病状の説明に使用される定性的用語。 次のすべてのサブカテゴリは修飾子と見なされます。

1.  時間に関連する表現: これらは、時間を定性的に説明する用語です。たとえば、突然、急性、慢性、長期など。 
2.  性質の表現: これらは、病状の "性質" を説明する用語です。たとえば、ヒリヒリする、鋭いなど。
3.  重症度の表現: 重度、軽度、わずか、抑えられない。
4.  範囲の表現: 局所、病巣、広範性。
5.  放射の表現: 放散する、放射。
6.  状態のスケール: 場合によっては、状態がスケールによって特徴付けられることがあります。これは、有限個の順序付けられた値のリストです。 たとえば、ステージ III の膵臓がんの患者。
7.  状態の経過: 状態の経過や進行に関連する用語。たとえば、改善、悪化、解消、鎮静など。 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="状態の修飾子属性と診断エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="状態の修飾子属性と診断エンティティの別の例。":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="状態の修飾子属性と症状および薬剤エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="このスクリーンショットは、状態の修飾子属性と診断エンティティの別の例を示しています。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="このスクリーンショットは、状態の修飾子属性と診断エンティティの追加の例を示しています。":::

## <a name="medication"></a>薬剤

### <a name="entities"></a>エンティティ

**MEDICATION_CLASS** – 類似した作用機序、関連する作用形態、類似した化学構造を持ち、同じ疾病の治療に使用される薬剤のセット。 たとえば、ACE 抑制剤、オピオイド、抗生物質、鎮痛剤など。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="薬剤クラス エンティティの例。":::

**MEDICATION_NAME** – 著作権を持つブランド名やブランド名以外の名前など、薬剤に関する言及。 イブプロフェンなど。

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="薬剤名エンティティの例。":::

**DOSAGE** - 処方された薬剤の量。 たとえば、点滴塩化ナトリウム溶液 *1000 mL* など。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="薬剤投薬量属性の例。":::

**MEDICATION_FORM** - 薬剤の形態。 たとえば、溶液、錠剤、カプセル、タブレット、パッチ、ジェル、ペースト、フォーム、スプレー、ドロップ、クリーム、シロップなど。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="薬剤形態属性の例。":::

**MEDICATION_ROUTE** - 薬剤の投与方法。 たとえば、経口、膣内、IV、硬膜外、局所、吸入など。

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="薬剤投与方法属性の例。":::

## <a name="social"></a>ソーシャル

### <a name="entities"></a>エンティティ

**FAMILY_RELATION** – 対象者の家族関係に関する言及。 たとえば、父、娘、兄弟、親など。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="治療時間属性の別の例を示すスクリーンショット。":::

## <a name="treatment"></a>処理方法

### <a name="entities"></a>エンティティ

**TREATMENT_NAME** – 治療手段。 たとえば、膝置換手術、骨髄移植、TAVI、ダイエットなど。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="治療名エンティティの例。":::
