---
title: Azure Security Center における Azure ストレージ アカウントの暗号化 | Microsoft Docs
description: このドキュメントでは、「**Enable encryption for Azure Storage Account**」 (Azure Storage アカウント暗号化の有効化) という Azure Security Center の推奨事項を実装する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: cadf375b541900600be82e76673637ff01a8ad96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696071"
---
# <a name="encryption-for-azure-storage-account-in-azure-security-center"></a>Azure Security Center における Azure Storage アカウントの暗号化

保存データ向け Azure ストレージの暗号化は、すべての Azure ストレージ アカウントに対して既定で有効です。 Azure Storage は、Azure Storage に書き込まれるとデータを暗号化し、取得される前に復号化します。 詳細については、「[保存データ向け Azure ストレージの暗号化](../storage/common/storage-service-encryption.md)」をご覧ください。

セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md)」 - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 - セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 - 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 - このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) - Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
