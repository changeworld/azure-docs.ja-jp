---
title: Azure Key Vault の脅威の防止
description: この記事では、Azure Security Center で Azure Key Vault 用 Advanced Threat Protection を設定する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914808"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 用 Threat Protection (プレビュー)

Azure Key Vault 用 Advanced Threat Protection によって、セキュリティ インテリジェンスにレイヤーが追加されます。 このツールを使うと、Key Vault アカウントに対して害を及ぼす可能性のあるアクセスの試行または悪用を検出できます。 Azure Security Center でネイティブの Advanced Threat Protection を使用することにより、セキュリティの専門家でなくとも脅威に対処できます。また、追加のセキュリティ監視システムを習得する必要もありません。

Security Center で異常なアクティビティが検出されると、アラートが表示されます。 また、サブスクリプション管理者に、不審なアクティビティの詳細と、特定された脅威を調査して修復する方法に関する推奨事項をメールで送信することもできます。

## <a name="configuring-threat-protection-from-security-center"></a>Security Center から Threat Protection を構成する

既定では、Security Center の Standard 価格レベルにサブスクライブすると、すべての Key Vault アカウントで Advanced Threat Protection が有効になります。 詳細については、[価格](security-center-pricing.md)に関するページをご覧ください。

特定のサブスクリプションの保護を有効または無効にするには、次のようにします。

1. Security Center の左ペインから、 **[価格と設定]** を選択します。

1. 脅威保護を有効または無効にするストレージ アカウントのサブスクリプションを選択します。

1. **[価格レベル]** を選択します。

1. **[リソースの種類ごとに価格レベルを選択]** グループから、 **[Key Vaults]** 行を見つけて、 **[有効]** または **[無効]** を選択します。

    [![Azure Security Center で Key Vault の Advanced Threat Protection を有効または無効にする](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Key Vault 用 Advanced Threat Protection を有効または無効にする方法について説明しました。 

関連資料については、次の記事をご覧ください。

- [Azure Security Center での脅威の防止](threat-protection.md) -- この記事では、Azure Security Center のセキュリティ アラートのソースについて説明します。
- [Key Vault セキュリティ アラート](alerts-reference.md#alerts-azurekv)--すべての Azure Security Center アラートの参照テーブルの Key Vault セクション