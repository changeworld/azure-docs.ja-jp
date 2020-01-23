---
title: Azure Key Vault 用 Advanced Threat Protection を設定する | Microsoft Docs
description: この記事では、Azure Security Center で Azure Key Vault 用 Advanced Threat Protection を設定する方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2375d8ee92d9c04c287b7fca793fcdc236e1e8f4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720040"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 用 Advanced Threat Protection を設定する (プレビュー)

Azure Key Vault 用 Advanced Threat Protection によって、セキュリティ インテリジェンスにレイヤーが追加されます。 このツールを使うと、Key Vault アカウントに対して害を及ぼす可能性のあるアクセスの試行または悪用を検出できます。 Azure Security Center でネイティブの Advanced Threat Protection を使用することにより、セキュリティの専門家でなくとも脅威に対処できます。また、追加のセキュリティ監視システムを習得する必要もありません。

Security Center で異常なアクティビティが検出されると、アラートが表示されます。 また、サブスクリプション管理者に、不審なアクティビティの詳細と、特定された脅威を調査して修復する方法に関する推奨事項をメールで送信することもできます。

> [!NOTE]
> 現時点では、Azure Key Vault 用 Advanced Threat Protection は、北米リージョンでのみご利用いただけます。

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Azure Security Center から Advanced Threat Protection を設定する

既定では、Security Center の Standard レベルをサブスクライブすると、すべての Key Vault アカウントで Advanced Threat Protection が有効になります。 詳細については、[価格](security-center-pricing.md)に関するページをご覧ください。

特定のサブスクリプションの保護を有効または無効にするには、次の手順に従います。

1. Security Center の左ペインから、 **[価格と設定]** を選択します。
1. 脅威保護を有効または無効にするストレージ アカウントのサブスクリプションを選択します。
1. **[価格レベル]** を選択します。
1. **[リソースの種類ごとに価格レベルを選択]** グループから、 **[Key Vaults]** 行を見つけて、 **[有効]** または **[無効]** を選択します。

    [![Azure Security Center で Key Vault の Advanced Threat Protection を有効または無効にする](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Key Vault 用 Advanced Threat Protection を有効または無効にする方法について説明しました。 

その他の関連資料については、次の記事を参照してください。

- [Security Center での Azure サービス レイヤーの脅威検出](security-center-alerts-service-layer.md):この記事では、Azure Key Vault の Advanced Threat Protection に関連したアラートについて説明します。
