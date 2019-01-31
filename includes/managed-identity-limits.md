---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7cdd2ce44cfa24b2b6bad2bb45260299bc8eda5f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252372"
---
| Category | 制限 |
| --- | --- |
| ユーザー割り当て済みマネージド ID | <ul><li>ユーザー割り当て済みマネージド ID を作成するときは、英数字 (0-9、a-z、A-Z) とハイフン (-) のみがサポートされます。 さらに、VM/VMSS への割り当てが適切に動作するためには、名前の長さは 24 文字以下にする必要があります。</li><li>マネージド ID 仮想マシン拡張機能を使用している場合にサポートされるユーザー割り当て済みマネージド ID の制限値は 32 個です。  マネージド ID 仮想マシン拡張機能を使用していない場合にサポートされるユーザー割り当て ID の制限値は 512 個です。</li>|

