---
title: Azure portal での Azure Firewall Premium
description: Azure portal での Azure Firewall Premium について説明します。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: cfb9f76751bbf5966b4aaa29e382a0946bf7cfd6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440432"
---
# <a name="azure-firewall-premium-in-the-azure-portal"></a>Azure portal での Azure Firewall Premium


 Azure Firewall Premium は、機密度が高く、規制された環境に必要な機能を備えた次世代のファイアウォールです。 このパックには、次の機能があります。

- **TLS 検査** - アウトバウンド トラフィックを復号化し、データを処理し、その後にデータを暗号化して宛先に送信します。
- **IDPS** - ネットワーク侵入検出と防止システム (IDPS) を使用すると、ネットワーク アクティビティを監視して悪意のあるアクティビティがないか確認し、このアクティビティに関する情報をログに記録し、それを報告して、任意でそのブロックを試みることができます。
- **URL フィルタリング** - Azure Firewall の FQDN フィルタリング機能を拡張して URL 全体が考慮対象になるようにします。 たとえば、`www.contoso.com` の代わりに `www.contoso.com/a/c` となります。
- **Web カテゴリ** - 管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトといった Web サイト カテゴリへのユーザーのアクセスを許可または禁止できます。

詳細については、「[Azure Firewall Premium の機能](premium-features.md)」を参照してください。

## <a name="deploy-the-firewall"></a>ファイアウォールをデプロイする

Azure Firewall Premium のデプロイは、標準の Azure Firewall のデプロイに似ています。

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="ポータルのデプロイ":::

**[ファイアウォール層]** には **[Premium]** を選択し、 **[ファイアウォール ポリシー]** には既存の Premium ポリシーを選択するか、新しいポリシーを作成します。

## <a name="configure-the-premium-policy"></a>Premium ポリシーを構成する

Premium ファイアウォール ポリシーの構成は、標準のファイアウォール ポリシーの構成に似ています。 Premium ポリシーを使用すると、Premium 機能を構成できます。

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Premium ポリシーのデプロイ":::

### <a name="rule-configuration"></a>ルールの構成

Premium ポリシーでアプリケーション ルールを構成するときに、追加の Premium 機能を構成することができます。

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium ルール":::

## <a name="next-steps"></a>次のステップ

実行中の Azure Firewall Premium 機能を確認するには、「[Azure Firewall Premium をデプロイして構成する](premium-deploy.md)」をご覧ください。