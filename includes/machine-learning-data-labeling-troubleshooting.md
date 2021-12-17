---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 8122a9c001ce56edefaa0215fc20fe183d2cd00c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367451"
---
これらの問題が発生した場合は、次のヒントを参考にしてください。

|問題  |解像度  |
|---------|---------|
|BLOB データストアに作成されたデータセットしか使用できない。     |  現在のリリースの既知の制限です。       |
|作成後、プロジェクトで "Initializing (初期化しています)" と長時間にわたり表示される。     | ページを手動で最新の情報に更新してください。 初期化は、1 秒あたり約 20 データポイントの速度で完了します。 自動更新が実行されない問題が確認されています。         |
|新しくラベル付けされた項目がデータ レビューで表示されない。     |   ラベル付けされたすべての項目を読み込むには、 **[最初]** ボタンを選択します。 **[First]\(1 番目\)** ボタンを選択すると、リストの先頭に戻りますが、ラベル付けされたデータはすべて読み込まれます。      |
|特定のラベラーに一連のタスクを割り当てることができない。     |   現在のリリースの既知の制限です。  |