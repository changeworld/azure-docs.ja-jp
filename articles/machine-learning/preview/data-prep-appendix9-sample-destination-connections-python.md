---
title: "Azure Machine Learning データ準備で可能な変換先/出力先の例 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備でのカスタム データ変換先/出力先の例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 4cad3343461a6c7eda78566b3d2552b1e3591960
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-destination-connections-python"></a>変換先接続のサンプル (Python) 
この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事をご覧ください。


## <a name="write-to-excel"></a>Excel への書き込み 


Excel への書き込みには、追加のライブラリが必要です。 機能拡張の概要には、新しいライブラリの追加について記載されています。 `openpyxl` は、追加する必要があるライブラリです。

Excel に書き込む前に、他のいくつかの変更が必要になる場合があります。 データ準備で使用されるデータ型には、変換先の一部の形式でサポートされていないものがあります。 たとえば、"Error" オブジェクトが存在する場合、これらは正しく Excel にシリアル化されません。 したがって、Excel への書き込みを試みる前に、すべての列からエラーを削除する [Replace Error Values] \(エラー値の置換\) 変換を行う必要があります。

以前の作業がすべて終了している場合、次の行により、Excel ドキュメントの単一のシートにデータ テーブルが書き込まれます。 データフローの書き出し (スクリプト) の変換を追加します。 その後、演算のセクションに次のコードを入力します。


### <a name="on-windows"></a>Windows の場合 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>macOS/OS X の場合 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
