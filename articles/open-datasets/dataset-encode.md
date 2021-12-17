---
title: ENCODE
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で ENCODE データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 0dc10b580d1a7c6fed8559f15a11e7c54737692d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285148"
---
# <a name="encode-encyclopedia-of-dna-elements"></a>ENCODE: DNA 要素の百科事典

[Encyclopedia of DNA Elements (ENCODE) コンソーシアム](https://www.encodeproject.org/help/project-overview/)は、米国国立ヒトゲノム研究所 (NHGRI) から資金提供を受け、国際的な共同研究を行っている研究グループです。 ENCODE の目的は、タンパク質や RNA レベルで作用する要素や、遺伝子が活性化する細胞や状況を制御する調整要素など、ヒトゲノムの機能的要素の包括的なパーツ リストを構築することです。

ENCODE の研究者は、さまざまなアッセイや手法を用いて機能的要素を特定しています。 遺伝子要素の発見とアノテーションは主に、多様な RNA ソースのシーケンシング、比較ゲノミクス、統合的バイオ インフォマティクス手法、人間によるキュレーションによって達成されます。 一般的には、DNA 過敏症アッセイ、DNA メチル化アッセイ、DNA および RNA と相互作用するタンパク質 (すなわち、ヒストン修飾、転写因子、クロマチン制御因子、RNA 結合タンパク質など) の免疫沈降法 (IP)、およびシーケンシングを使用して、調整要素を調べます。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは https://www.encodeproject.org/ のデータ ストアのミラーです。

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには約 756 TB のデータが含まれており、毎月第 1 週に更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: 'https://datasetencode.blob.core.windows.net/dataset '

米国中西部: 'https://datasetencode-secondary.blob.core.windows.net/dataset '

[SAS Token](../storage/common/storage-sas-overview.md): ?sv=2019-10-10&si=prod&sr=c&sig=9qSQZo4ggrCNpybBExU8SypuUZV33igI11xw0P7rB3c%3D

## <a name="use-terms"></a>使用条件

外部データ ユーザーは、型やサイズに関係なく、制限なしに任意の ENCODE データに基づいて結果を自由にダウンロード、分析、および公開できます。また、ENCODE データ作成者の猶予期間は、個々のメンバーまたはコンソーシアムの一員のいずれとしても含まれません。 未公開の ENCODE データを使用している研究者は、データ作成者に連絡して公開の可能性について話し合うことをお勧めします。 コンソーシアムでは、独自の分析作業の結果を独立した出版物として発表していきます。

ENCODE では、出版物やプレゼンテーションで ENCODE データセット (公開、未公開を問わず) を使用する研究者が、[https://www.encodeproject.org/help/citing-encode/](https://www.encodeproject.org/help/citing-encode/) で報告されている以下のすべての方法で ENCODE コンソーシアムを引用することを求めています。

## <a name="contact"></a>Contact

ご質問、懸念事項、ご意見がございましたら、電子メール (encode-help@lists.stanford.edu) にてヘルプ デスクにお問い合わせください。

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。