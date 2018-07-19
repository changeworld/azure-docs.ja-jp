---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755741"
---
次の制限は、Azure Backup に適用されます。

| 制限の種類 | 既定の制限 |
| --- | --- |
| 各資格情報コンテナーに登録できるサーバーやマシンの数 |Windows Server/Client/SCDPM で 50  <br/> IaaS VM で 1,000 |
| Azure 資格情報コンテナー ストレージに格納されるデータのデータ ソースのサイズ |最大 54400 GB<sup>1</sup> |
| 各 Azure サブスクリプションで作成できるバックアップ資格情報コンテナー数 |500 の Recovery Services コンテナー (リージョンあたり) |
| 1 日にバックアップをスケジュールできる回数 |Windows Server/Client で 1 日 3 回 <br/> SCDPM で 1 日 2 回 <br/> IaaS VM で 1 日 1 回 |
| バックアップ用に Azure の仮想マシンに接続できるデータ ディスクの数 |16 |
| バックアップ用に Azure の仮想マシンに接続される個々のデータ ディスクのサイズ| 4095 GB <sup>2</sup>|

* <sup>1</sup> 54400 GB の制限は、IaaS VM のバックアップには適用されません。
 

