---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381087"
---
次の制限は、Azure Backup に適用されます。

| 制限の種類 | 既定の制限 |
| --- | --- |
| 各資格情報コンテナーに登録できるサーバーやマシンの数 |Windows Server/Client/SCDPM で 50  <br/> IaaS VM で 1,000 |
| Azure 資格情報コンテナー ストレージに格納されるデータのデータ ソースのサイズ |最大 54400 GB<sup>1</sup> |
| 各 Azure サブスクリプションで作成できるバックアップ資格情報コンテナー数 |500 の Recovery Services コンテナー (リージョンあたり) |
| 1 日にバックアップをスケジュールできる回数 |Windows Server/Client で 1 日 3 回 <br/> SCDPM で 1 日 2 回 <br/> IaaS VM で 1 日 1 回 |
| バックアップ用に Azure の仮想マシンに接続できるデータ ディスクの数 |16 |
| バックアップ用に Azure の仮想マシンに接続される個々のデータ ディスクのサイズ| 4095 GB|

* <sup>1</sup> 54400 GB の制限は、IaaS VM のバックアップには適用されません。
 

