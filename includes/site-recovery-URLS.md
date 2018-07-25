---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765540"
---
| Name | 商用の URL | 政府機関の URL | 説明 |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | AAD を使ったアクセス制御と ID 管理に使用 |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | レプリケーション データの転送と調整に使用 |
| レプリケーション | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | レプリケーション管理操作と調整に使用 |
| Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用 |
| テレメトリ (オプション) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | テレメトリに使用 |

``time.nist.gov`` と ``time.windows.com`` は、すべてのデプロイにおけるシステム時刻とグローバル時刻間の時刻同期の確認に使用されます。


