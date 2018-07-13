---
title: サポートされるデータ ソース - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: サポートされるデータ ソース
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376365"
---
# <a name="data-sources"></a>データ ソース 
QnA Maker は、半構造化された一般的なコンテンツ形式 (FAQ、製品マニュアルなど) から、質問とその回答のペアを自動的に抽出することができます。 また、構造化されたファイルのコンテンツをナレッジ ベースに追加することもできます。

## <a name="plain-faq-pages"></a>基本的な FAQ ページ
これは最も一般的なタイプの FAQ ページです。質問と回答が同じページにあり、質問のすぐ下に回答が記載されます。 

![基本的な FAQ ページ](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>セクション リンクを含んだ FAQ ページ 
このタイプの FAQ ページでは、質問が 1 か所に集約され、同じページ上の別のセクションにある回答にリンクされます。

 ![セクション リンク FAQ ページ](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>異なるページへのリンクを含んだ FAQ ページ 
このタイプの FAQ ページは、セクション リンクを含んだ FAQ ページに似ています。異なるのは、そのリンクが異なるページにリダイレクトされるという点です。 QnA Maker は、リンクされているすべてのページをクロールして、対応する回答を抽出します。

 ![ディープ リンク FAQ ページ](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>製品マニュアル

一般にマニュアルは、製品に付属するガイダンス資料です。 ユーザーにとっては、製品のセットアップ、使用、保守、トラブルシューティングにあたっての貴重な情報源となります。 QnA Maker は、マニュアルを処理する際、見出しと小見出しを質問として抽出し、質問に続くコンテンツを回答として抽出します。 [こちら](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)の例を参照してください。

> [!NOTE]
> 抽出の対象として最も適しているのは、目次やインデックス ページがあり、階層化された見出しから成る明確な構造を持ったマニュアルです。


## <a name="structured-data-format-through-file-upload"></a>ファイルのアップロードでの構造化データ形式

QnA Maker には、列が書式設定されている構造化ファイル (.tsv、.xlsx など) をナレッジ ベースの作成中にアップロードすることもできます。 また、ナレッジ ベースの **[設定]** タブからファイルをアップロードすることもできます。

| 質問  | リダイレクト先  | Metadata                |
|-----------|---------|-------------------------|
| 質問 1 | 回答 1 | `Key1:Value1\|Key2:Value2` |
| 質問 2 | 回答 2 |      `Key:Value`           |
これより後の列は、ソース ファイルに含まれていても無視されます。

## <a name="structured-data-format-through-import"></a>インポートでの構造化データ形式
ナレッジ ベースをインポートすると、既にあるナレッジ ベースの内容が置き換えられます。 インポートでは、データ ソース情報を含んだ .tsv 形式の構造化ファイルが必要となります。 この情報によって、QnA Maker は質問とその回答のペアをグループ化し、その帰属先となるデータ ソースを特定することができます。

| 質問  | リダイレクト先  | ソース| Metadata                |
|-----------|---------|----|---------------------|
| 質問 1 | 回答 1 | URL 1|`Key1:Value1\|Key2:Value2` |
| 質問 2 | 回答 2 | 編集|    `Key:Value`       |

## <a name="editorial"></a>編集
ナレッジ ベースの内容とする既存のコンテンツがない場合は、QnA Maker のナレッジ ベースに編集という形でそれらを追加することもできます。 ナレッジ ベースの更新方法については、[こちら](../How-To/edit-knowledge-base.md)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker サービスの設定](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)