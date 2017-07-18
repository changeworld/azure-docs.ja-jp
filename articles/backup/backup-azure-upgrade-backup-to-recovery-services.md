---
title: "Backup コンテナーを Recovery Services コンテナーにアップグレードする (プレビュー) | Microsoft Docs"
description: "Azure Backup コンテナーを Recovery Services コンテナーにアップグレードするためのコマンドとサポート情報です。"
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: required
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/22/2017
ms.author: markgal;arunak
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: b8cf27bd7e50d6edd2534ed2d9f6bb8a7079fbbf
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017


---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Backup コンテナーを Recovery Services コンテナーにアップグレードする

この記事では、Backup コンテナーを Recovery Services コンテナーにアップグレードする方法について説明します。 このアップグレード プロセスは、実行中のバックアップ ジョブに影響を与えず、バックアップ データが失われることもありません。 Backup コンテナーを Recovery Services コンテナーにアップグレードする主な理由は次のとおりです。
- Backup コンテナーのすべての機能は、Recovery Services で保持されます。
- Recovery Services コンテナーは Backup コンテナーよりも多くの機能があり (セキュリティの強化、統合された監視、高速復元、アイテム レベルの復元など)、改良され、簡素化されたポータルからバックアップ項目を管理します。
-  新機能は、Recovery Services コンテナーに対してのみリリースされます。

## <a name="how-to-upgrade"></a>アップグレードする方法

バックアップ コンテナーを Recovery Services コンテナーにアップグレードすることには大きな需要があります。 このプロセスを効率化するため、お客様を一括してアップグレード キューに登録しています。 アップグレード キューへのエントリにサインアップするには、該当するリンクを使用してください。
- [バックアップ コンテナーのアップグレード](https://www.surveymonkey.com/r/Y57BJQX)
- [Site Recovery コンテナーのアップグレード](https://www.surveymonkey.com/r/5HHPZQN)

## <a name="what-happens-after-i-sign-up"></a>サインアップ後の流れ
サブスクリプションがアップグレードのホワイトリストに登録されると、Microsoft からアップグレードに進むように連絡があります。 アップグレード プロセスに関する詳細については、Azure Backup の[ブログ](http://azure.microsoft.com/blog/upgrade-classic-backup-and-siterecovery-vault-to-arm-recovery-services-vault)をご覧ください。

