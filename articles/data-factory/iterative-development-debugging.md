---
title: Azure Data Factory での反復開発とデバッグ | Microsoft Docs
description: Azure Portal で Data Factory パイプラインの開発とデバッグを反復して実行する方法について説明します。
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 04/04/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: adb5ffd65d4b2b591abba5d4b3481b7ca08de579
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory での反復開発とデバッグ

Azure Data Factory では、Data Factory パイプラインの開発とデバッグを反復して実行できます。

## <a name="iterative-debugging-features"></a>反復デバッグ機能
パイプラインを作成し、パイプライン キャンバスの**デバッグ**機能を使用して、1 行もコードを記述せずにテストを実行できます。

![パイプライン キャンバスのデバッグ機能](media/iterative-development-debugging/iterative-development-image1.png)

テストの実行結果は、パイプライン キャンバスの**出力**ウィンドウに表示されます。

![パイプライン キャンバスの出力ウィンドウ](media/iterative-development-debugging/iterative-development-image2.png)

テストの実行が成功したら、パイプラインにさらにアクティビティを追加し、反復的な方法でデバッグを続行します。 テストの実行中に、実行を **[キャンセル]** することもできます。

![テストの実行をキャンセルする](media/iterative-development-debugging/iterative-development-image3.png)

テストの実行を行うときは、**[デバッグ]** を選択する前に、Data Factory に変更を発行する必要はありません。 これは、Data Factory ワークフローを更新する前に、変更が期待どおりに動作することを確認する場合に便利です。

## <a name="more-info-about-debugging"></a>デバッグの詳細

1. **デバッグ**機能を使用して開始するテストの実行は、**[モニター]** タブの一覧では利用できません。**[モニター]** タブの **[Trigger now]\(今すぐトリガー\)**、**[スケジュール]**、または **[タンブリング ウィンドウ]** トリガーでトリガーされた実行のみを確認できます。パイプライン キャンバスの**出力**ウィンドウで、**デバッグ**機能を使用して開始された最後のテストの実行を確認できます。

2. **[デバッグ]** を選択すると、パイプラインが実際に実行されます。 そのため、たとえばパイプラインにコピー アクティビティが含まれていれば、テストの実行では、データがコピー元からコピー先にコピーされます。 その結果、デバッグ時のコピー アクティビティとその他のアクティビティでは、テスト フォルダーを使用することをお勧めします。 パイプラインのデバッグが終わったら、通常の操作で使用する実際のフォルダーに切り替えます。

## <a name="setting-breakpoints-for-debugging"></a>デバッグ用のブレークポイントの設定

Data Factory では、パイプライン キャンバスの特定のアクティビティに到達するまでデバッグすることもできます。 そこまでテストするアクティビティにブレークポイントを設定し、**[デバッグ]** を選択します。 Data Factory は、パイプライン キャンバスでブレークポイント アクティビティに達するまで、テストが実行されることを保証します。 この*特定の場所までデバッグする*機能は、パイプライン全体ではなく、パイプライン内のアクティビティのサブセットのみをテストする場合に便利です。

![パイプライン キャンバス上のブレークポイント](media/iterative-development-debugging/iterative-development-image4.png)

## <a name="next-steps"></a>次の手順
[Azure Data Factory における継続的インテグレーションとデプロイ](continuous-integration-deployment.md)
