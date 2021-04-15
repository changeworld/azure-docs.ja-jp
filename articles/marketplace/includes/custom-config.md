---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92283391"
---
追加の構成が必要な場合は、起動時に実行されるスケジュールされたタスクを使用して、デプロイされた後の VM に最終的な変更を加えます。 また、以下の点についても検討してください。

- 一度だけ実行されるタスクの場合、タスクは正常に完了した後に自身を削除する必要があります。
- 構成は、C と D 以外のドライブに依存しないようにする必要があります。これは、存在が必ず保証されるドライブがこれら 2 つのみのためです (ドライブ C はオペレーティング システム ディスク、ドライブ D は一時ローカル ディスクです)。

拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](../../virtual-machines/extensions/features-linux.md)」を参照してください。