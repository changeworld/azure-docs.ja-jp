---
title: Text Analytics for Health によって認識されるエンティティのカテゴリ
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health によって認識されるカテゴリについて説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: e61589eaf90da3cb7e8310724a28bf993e8efb21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089767"
---
# <a name="supported-text-analytics-for-health-entity-categories"></a>サポートされる Text Analytics for Health のエンティティのカテゴリ

Text Analytics for Health では、構造化されていない医療データから分析情報を処理および抽出します。 このサービスでは、医療の概念を検出して明らかにし、アサーションを概念に割り当て、概念間の意味の関係を推測し、それらを一般的な医療オントロジにリンクします。

Text Analytics for Health を使用すると、次のカテゴリの医療概念が検出されます。

## <a name="anatomy"></a>構造

### <a name="entities"></a>エンティティ

**BODY_STRUCTURE** - 身体の系統、解剖学的な位置または領域、身体の部位。 たとえば、腕、膝、腹部、鼻、肝臓、頭部、呼吸器系、リンパ球など。

:::image type="content" source="../media/entities/anatomy-entities-body-structure.png" alt-text="身体構造エンティティの例。" lightbox="../media/entities/anatomy-entities-body-structure.png":::

## <a name="demographics"></a>人口統計データ

### <a name="entities"></a>エンティティ

**AGE** - 患者や家族などを含む、年齢に関するすべての用語と語句。 たとえば、40 歳、51 才、3 か月、成人、乳児、初老、若者、未成年、中年など。

**GENDER** - 対象者の性別を示す用語。 たとえば、男性、女性、女、紳士、淑女など。

:::image type="content" source="../media/entities/age-entity.png" alt-text="年齢エンティティの例。" lightbox="../media/entities/age-entity.png":::

## <a name="examinations"></a>検査

### <a name="entities"></a>エンティティ

**EXAMINATION_NAME** - バイタル サインや身体測定を含む、診断手順と検査。 たとえば、MRI、ECG、HIV 検査、ヘモグロビン、血小板数、"*ブリストル便性状スケール*" などのスケール システム。

:::image type="content" source="../media/entities/exam-name-entities.png" alt-text="検査エンティティの例。" lightbox="../media/entities/exam-name-entities.png":::

## <a name="external-influence"></a>外的影響

### <a name="entities"></a>エンティティ

**ALLERGEN** – アレルギー反応を引き起こす抗原。 たとえば猫、落花生などです。

:::image type="content" source="../media/entities/external-influence-allergen.png" alt-text="外的影響エンティティの例。" lightbox="../media/entities/external-influence-allergen.png":::


## <a name="general-attributes"></a>一般的な属性

### <a name="entities"></a>エンティティ

**COURSE** - 他のエンティティの経時的変化の説明。たとえば、状況の進行 (例: 改善、悪化、解決、緩和)、投薬または治療の経過 (例: 薬量の増加)。 

:::image type="content" source="../media/entities/course-entity.png" alt-text="経過エンティティの例。" lightbox="../media/entities/course-entity.png":::

**DATE** - 病状、検査、治療、薬、または投薬イベントに関連する完全な日付。

:::image type="content" source="../media/entities/date-entity.png" alt-text="日付エンティティの例。" lightbox="../media/entities/date-entity.png":::

**DIRECTION** - 身体の構造、病状、検査、または治療に関係する可能性のある、左、横、上、後部などの方向関連の用語。

:::image type="content" source="../media/entities/direction-entity.png" alt-text="方向エンティティの例。" lightbox="../media/entities/direction-entity.png":::

**FREQUENCY** - 病状、検査、治療、または投薬の発生頻度 (過去、現在、未来) を示します。

:::image type="content" source="../media/entities/medication-form.png" alt-text="薬剤頻度属性の例。" lightbox="../media/entities/medication-form.png":::


**TIME** - 病状、検査、治療、薬、または投薬イベントの開始や長さ (期間) に関する時間関連の用語。 

**MEASUREMENT_UNIT** - 検査または病状の測定に関連する測定単位。

**MEASUREMENT_VALUE** - 検査または病状の測定に関連する値。

:::image type="content" source="../media/entities/measurement-value-entity.png" alt-text="測定値エンティティの例。" lightbox="../media/entities/measurement-value-entity.png":::

**RELATIONAL_OPERATOR** - エンティティと追加情報の間の定量的関係を表す語句。

:::image type="content" source="../media/entities/measurement-unit.png" alt-text="測定単位エンティティの例。" lightbox="../media/entities/measurement-unit.png"::: 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>エンティティ

**VARIANT** - 遺伝子のバリエーションと変異に関するすべての言及。 たとえば、`c.524C>T`、`(MTRR):r.1462_1557del96` など
  
**GENE_OR_PROTEIN** - 人間の遺伝子、染色体、染色体とタンパク質の一部の名前と記号に関するすべての言及。 たとえば、MTRR、F2 など。

**MUTATION_TYPE** - 変位の説明 (その種類、効果、場所を含む)。 たとえば、トリソミ、生殖細胞変異、機能喪失などです。

:::image type="content" source="../media/entities/genomics-entities.png" alt-text="遺伝子エンティティの例。" lightbox="../media/entities/genomics-entities.png":::

**EXPRESSION** - 遺伝子発現レベル。 たとえば、陽性、陰性、過剰発現、高/低レベルで検出、上昇。

:::image type="content" source="../media/entities/expression.png" alt-text="遺伝子発現エンティティの例。" lightbox="../media/entities/expression.png":::


## <a name="healthcare"></a>医療

### <a name="entities"></a>エンティティ
  
**ADMINISTRATIVE_EVENT** – 医療システムに関連するが、管理または半ば管理の性質を持つイベント。 たとえば、登録、受付、試験、研究エントリ、移植、退院、入院など。 

**CARE_ENVIRONMENT** – 患者が治療を受ける環境または場所。 たとえば、緊急救命室、医師の診察室、有酸素運動ユニット、ホスピス、病院など。

:::image type="content" source="../media/entities/healthcare-event-entity.png" alt-text="医療イベント エンティティの例。" lightbox="../media/entities/healthcare-event-entity.png" :::

**HEALTHCARE_PROFESSION** – 有資格または無資格の医療関係者。 たとえば、歯科医、病理学者、神経科医、放射線科医、薬剤師、栄養士、理学療法士、カイロプラクターなど。

:::image type="content" source="../media/entities/healthcare-profession-entity-2.png" alt-text="医療環境エンティティの別の例。" lightbox="../media/entities/healthcare-profession-entity-2.png":::

## <a name="medical-condition"></a>病状

### <a name="entities"></a>エンティティ

**DIAGNOSIS** – 病気、症候群、中毒。 たとえば、乳がん、アルツハイマー病、HTN、CHF、脊髄損傷など。

**SYMPTOM_OR_SIGN** – 病気またはその他の診断の主観的または客観的な証拠。 たとえば、胸痛、頭痛、目まい、発疹、SOB、柔らかい腹部、正常な腸音、栄養良好など。

:::image type="content" source="../media/entities/medical-condition-entity.png" alt-text="病状エンティティの例。" lightbox="../media/entities/medical-condition-entity.png":::

**CONDITION_QUALIFIER** - 病状の説明に使用される定性的用語。 次のすべてのサブカテゴリは修飾子と見なされます。

* 時間に関連する表現: これらは、時間を定性的に説明する用語です。たとえば、突然、急性、慢性、長期など。 
* 性質の表現: これらは、病状の "性質" を説明する用語です。たとえば、ヒリヒリする、鋭いなど。
* 重症度の表現: 重度、軽度、わずか、抑えられない。
* 範囲の表現: 局所、病巣、広範性。

:::image type="content" source="../media/entities/condition-qualifier-diagnosis-3.png" alt-text="このスクリーンショットは、状態の修飾子属性と診断エンティティの別の例を示しています。" lightbox="../media/entities/condition-qualifier-diagnosis-3.png" :::

**CONDITION_SCALE** - 状態をスケールによって特徴づける定性的用語。有限の順序付きリストの値です。

:::image type="content" source="../media/entities/condition-scale-example.png" alt-text="状態の修飾子属性と診断エンティティの別の例。" lightbox="../media/entities/condition-scale-example.png":::

## <a name="medication"></a>薬剤

### <a name="entities"></a>エンティティ

**MEDICATION_CLASS** – 類似した作用機序、関連する作用形態、類似した化学構造を持ち、同じ疾病の治療に使用される薬剤のセット。 たとえば、ACE 抑制剤、オピオイド、抗生物質、鎮痛剤など。

:::image type="content" source="../media/entities/medication-entities-class.png" alt-text="薬剤クラス エンティティの例。" lightbox="../media/entities/medication-entities-class.png":::

**MEDICATION_NAME** – 著作権を持つブランド名やブランド名以外の名前など、薬剤に関する言及。 イブプロフェンなど。

**DOSAGE** - 処方された薬剤の量。 たとえば、点滴塩化ナトリウム溶液 *1000 mL* など。

**MEDICATION_FORM** - 薬剤の形態。 たとえば、溶液、錠剤、カプセル、タブレット、パッチ、ジェル、ペースト、フォーム、スプレー、ドロップ、クリーム、シロップなど。

:::image type="content" source="../media/entities/medication-dosage.png" alt-text="薬剤投薬量属性の例。" lightbox="../media/entities/medication-dosage.png":::

**MEDICATION_ROUTE** - 薬剤の投与方法。 たとえば、経口、局所、吸入など。

:::image type="content" source="../media/entities/medication-form.png" alt-text="薬剤形態属性の例。" lightbox="../media/entities/medication-form.png"::: 

## <a name="social"></a>ソーシャル

### <a name="entities"></a>エンティティ

**FAMILY_RELATION** – 対象者の家族関係に関する言及。 たとえば、父、娘、兄弟、親など。

:::image type="content" source="../media/entities/family-relation.png" alt-text="家族関係エンティティの例。" lightbox="../media/entities/family-relation.png":::

## <a name="treatment"></a>処理方法

### <a name="entities"></a>エンティティ

**TREATMENT_NAME** – 治療手段。 たとえば、膝置換手術、骨髄移植、TAVI、ダイエットなど。

:::image type="content" source="../media/entities/treatment-entities-name.png" alt-text="治療名エンティティの例。" lightbox="../media/entities/treatment-entities-name.png":::


## <a name="supported-assertions"></a>サポートされているアサーション

アサーション修飾子は 3 つのカテゴリに分かれており、それぞれが異なる側面に焦点を当てています。
各カテゴリには、相互に排他的な値のセットが含まれています。 各エンティティには、カテゴリごとに 1 つの値のみが割り当てられます。 各カテゴリの最も一般的な値が既定値です。 サービスの出力応答には、既定値とは異なるアサーション修飾子だけが含まれます。

### <a name="certainty"></a>確実性  

概念の存在に関する情報と (存在または不在)、その存在に関するテキストがどの程度確実か (的確または可能性) に関する情報を提供します。

**Positive** (既定値): 概念が存在するか、発生しています。

**Negative**: 概念は現在存在していないか、発生していません。

:::image type="content" source="../media/entities/negative-entity.png" alt-text="負のエンティティの例。" lightbox="../media/entities/negative-entity.png":::

**Positive_Possible**: 概念は存在する可能性がありますが、不明確要素があります。

:::image type="content" source="../media/entities/positive-possible-entity.png" alt-text="正の可能性エンティティの例。" lightbox="../media/entities/positive-possible-entity.png" :::

**Negative_Possible**: 概念が存在する可能性はほとんどありませんが、ある程度の不明確要素があります。

:::image type="content" source="../media/entities/negative-possible-entity.png" alt-text="負の可能性エンティティの例。" lightbox="../media/entities/negative-possible-entity.png" :::

**Neutral_Possible**: 概念は、存在する可能性と存在しない可能性があり、どちらの側にも偏っていません。

:::image type="content" source="../media/entities/neutral-possible-entity.png" alt-text="中立の可能性エンティティの例。" lightbox="../media/entities/neutral-possible-entity.png":::

### <a name="conditionality"></a>条件付け

概念の存在が特定の条件に依存するかどうかに関する情報を提供します。 

**None** (既定値): 概念は事実であり、仮説でなく、特定の条件に依存しません。

**Hypothetical**: 概念は発達するか、将来発生する可能性があります。

:::image type="content" source="../media/entities/hypothetical-entity.png" alt-text="仮説エンティティの例。" lightbox="../media/entities/hypothetical-entity.png":::

**Conditional**: 概念は存在するか、特定の条件下でのみ発生します。

:::image type="content" source="../media/entities/conditional-entity.png" alt-text="条件付きエンティティの例。" lightbox="../media/entities/conditional-entity.png":::

### <a name="association"></a>関連付け

概念はテキストの対象または別の誰かと関連付けられているかどうかを示します。

**Subject** (既定値): 概念は、テキストの対象 (通常は患者) に関連付けられています。

**Someone_Else**: 概念は、テキストの対象ではない誰かに関連付けられています。

:::image type="content" source="../media/entities/association-entity.png" alt-text="関連付けエンティティの例。" lightbox="../media/entities/association-entity.png":::



## <a name="next-steps"></a>次のステップ

* [NER の概要](../../named-entity-recognition/overview.md)
