---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060582"
---
**名前** | **商用の URL**  | **政府機関の URL** | **説明** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | AAD を使ったアクセス制御と ID 管理に使用 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | レプリケーション データの転送と調整に使用 
レプリケーション | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | レプリケーション管理操作と調整に使用 
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用 
テレメトリ (オプション) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | テレメトリに使用 
時刻同期 | ``time.windows.com`` | ``time.nist.gov`` | すべてのデプロイでシステム時刻とグローバル時刻の間の時刻同期を確認するために使用されます。


