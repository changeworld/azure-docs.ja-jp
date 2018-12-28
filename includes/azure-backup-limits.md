---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109095"
---
次の制限は、Azure Backup に適用されます。

| **制限** | **既定値** |
| --- | --- |
| コンテナーに登録できるサーバー/マシン | Windows Server/Windows クライアント/System Center DPM: 50 <br/><br/> IaaS VM: 1,000  |
| コンテナー ストレージ内のデータ ソースのサイズ |最大 54,400 GB。 この制限は、IaaS VM のバックアップには適用されません |
| Azure サブスクリプションのバックアップ コンテナー |500 コンテナー (リージョンあたり) |
| 毎日のバックアップのスケジュール |Windows Server/クライアント: 3 回 (1 日あたり)<br/> System Center DPM: 2 回 (1 日あたり) <br/> IaaS VM: 1 回 (1 日あたり)  |
| Azure VM にアタッチするバックアップ用データ ディスク | 32 |
| Azure VM にアタッチする個々のバックアップ用データ ディスク| 4095 GB|



