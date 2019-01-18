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
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300689"
---
次の制限は、Azure Backup に適用されます。

| **制限** | **既定値** |
| --- | --- |
| コンテナーに登録できるサーバー/マシン | Windows Server/Windows クライアント/System Center DPM: 50 <br/><br/> IaaS VM: 1,000  |
| コンテナー ストレージ内のデータ ソースのサイズ |最大 54,400 GB。 この制限は、IaaS VM のバックアップには適用されません |
| Azure サブスクリプションのバックアップ コンテナー |500 コンテナー (リージョンあたり) |
| 毎日のバックアップのスケジュール |Windows Server/クライアント: 3 回 (1 日あたり)<br/> System Center DPM: 2 回 (1 日あたり) <br/> IaaS VM: 1 回 (1 日あたり)  |
| Azure VM にアタッチするバックアップ用データ ディスク | 16 |
| Azure VM にアタッチする個々のバックアップ用データ ディスク| 4095 GB|
