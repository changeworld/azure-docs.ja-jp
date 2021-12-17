---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 04/14/2021
ms.openlocfilehash: a33a702128be0d8e90b3db635001dbfbdb279f2a
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575949"
---
|イメージの種類  |一般化されたイメージ  |専用イメージ  |
|---------|---------|---------|
|移行先     |どのシステムにもデプロイされる         | 特定のシステムが対象        |
|ブート後のセットアップ     | VM の初回ブート時にセットアップが必要。          | セットアップは必要ありません。 <br> VM はプラットフォームによってオンにされる。        |
|構成     |ホスト名、管理者ユーザー、その他の VM 固有の設定が必要。         |事前構成済み。         |
|どのような場合に使用するか     |同じイメージからの複数の新しい VM の作成。         |特定のマシンの移行または以前のバックアップからの VM の復元。         |
