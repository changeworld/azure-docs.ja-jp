---
title: Microsoft Defender for Cloud のセキュリティポリシー、イニシアチブ、推奨事項について
description: Microsoft Defender for Cloud のセキュリティポリシー、イニシアチブ、推奨事項について説明します。
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00d73987acd0e5137913872138d6f78ac18e9bdf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083975"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>セキュリティ ポリシー、イニシアチブ、および推奨事項とは

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud は、セキュリティイニシアチブをサブスクリプションに適用します。 これらのイニシアチブには、1 つ以上のセキュリティ ポリシーが含まれています。 各ポリシーが、セキュリティ対策を強化するための推奨事項となります。 このページでは、これらについて詳しく説明します。


## <a name="what-is-a-security-policy"></a>セキュリティ ポリシーとは何ですか。

Azure Policy で作成される Azure ポリシー定義は、制御する特定のセキュリティ条件に関するルールです。 組み込みの定義には、展開を許可するリソースの種類の制御や、すべてのリソースでのタグ使用の強制などが含まれます。 独自のカスタム ポリシー定義を作成することもできます。

これらのポリシー定義 (組み込み定義またはカスタム定義) を実装するには、それらを割り当てる必要があります。 こうしたポリシーを割り当てるには、Azure portal、PowerShell、または Azure CLI を使用します。 Azure Policy では、ポリシーを無効または有効にすることができます。

Azure Policy には、さまざまな種類のポリシーがあります。 クラウドの Defender は、主に、特定の条件と構成を確認し、コンプライアンスを報告する "監査" ポリシーを使用します。 セキュリティ設定を適用するために使用できる "強制" ポリシーもあります。

## <a name="what-is-a-security-initiative"></a>セキュリティ イニシアチブとは

Azure Policy イニシアチブは、特定の目標や目的を実現するためにグループ化された Azure Policy の定義またはルールのコレクションです。 Azure イニシアチブは、一連のポリシーを論理的にグループ化して単一の項目として扱うことで、ポリシーの管理を簡略化します。

セキュリティ イニシアチブは、ワークロードの必要な構成を定義し、会社や規制当局のセキュリティ要件に確実に準拠できるようにします。

セキュリティポリシーと同様に、クラウドイニシアチブの Defender は Azure Policy でも作成されます。 [Azure Policy](../governance/policy/overview.md) を使用すると、ポリシーを管理し、イニシアチブを構築し、複数のサブスクリプションまたは管理グループ全体に対してイニシアチブを割り当てることができます。

Microsoft Defender for Cloud のすべてのサブスクリプションに自動的に割り当てられる既定のイニシアチブは、Azure のセキュリティベンチマークです。 このベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。 詳細については、[Azure セキュリティ ベンチマーク](/security/benchmark/azure/introduction)に関する記事を参照してください。

Defender for Cloud は、セキュリティイニシアチブとポリシーを操作するための次のオプションを提供します。

- **組み込みの既定のイニシアチブを表示および編集** する-クラウドに対して defender を有効にすると、"Azure セキュリティベンチマーク" という名前のイニシアチブがクラウド登録サブスクリプションのすべての defender に自動的に割り当てられます。 このイニシアチブをカスタマイズするには、ポリシーのパラメーターを編集して、イニシアチブ内の個々のポリシーを有効または無効にします。 [組み込みのセキュリティ ポリシー](./policy-reference.md)の一覧を参照して、すぐに使用できるオプションを確認してください。

- **独自のカスタムイニシアチブを追加** する-サブスクリプションに適用されるセキュリティイニシアチブをカスタマイズする場合は、Defender for Cloud で実行できます。 作成したポリシーにマシンが従っていない場合は、推奨事項が提供されます。 カスタム ポリシーを作成して割り当てる手順については、[カスタム セキュリティ イニシアチブの使用](custom-security-policies.md)に関する記事をご覧ください。

- **規制遵守基準をイニシアチブとして追加** する-Defender for Cloud の法令順守ダッシュボードには、環境内のすべての評価の状態が、特定の標準または規制 (Azure CI、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020) のコンテキストで表示されます。 詳細については、[規制コンプライアンスの向上](regulatory-compliance-dashboard.md)に関する記事をご覧ください。

## <a name="what-is-a-security-recommendation"></a>セキュリティに関する推奨事項とは

Defender は、ポリシーを使用して、リソースのコンプライアンスステータスを定期的に分析して、潜在的なセキュリティの誤りや弱点を特定します。 その後、これらの問題を修正する方法に関する推奨事項が提供されます。 推奨事項は、関連するポリシーに対してリソースを評価し、定義された要件を満たしていないリソースを特定した結果です。

Defender for Cloud は、選択したイニシアチブに基づいて、セキュリティに関する推奨事項を作成します。 イニシアチブからのポリシーがリソースと比較され、準拠していないものが見つかった場合は、Defender for Cloud で推奨設定として表示されます。

推奨事項とは、リソースをセキュリティで保護および強化するために実行する操作です。 各推奨事項には、次の情報が記載されています。

- 問題の簡単な説明
- 推奨事項を実装するために実行する修復手順
- 影響を受けるリソース

実際には、次のように動作します。

1. Azure のセキュリティベンチマークは、要件を含む ***イニシアチブ*** です。

    たとえば、Azure Storage アカウントはネットワークアクセスを制限して、攻撃対象領域を減らす必要があります。

1. このイニシアチブには複数の ***ポリシー*** が含まれており、それぞれに特定のリソースの種類の要件があります。 これらのポリシーによって、イニシアチブの要件が適用されます。 

    この例を続行するには、"Storage アカウントは、仮想ネットワークルールを使用してネットワークアクセスを制限する必要があります" というポリシーを適用します。

1. Microsoft Defender for Cloud は、接続されているサブスクリプションを継続的に評価します。 ポリシーを満たしていないリソースが見つかった場合は、その状況を修正し、セキュリティ要件を満たしていないリソースのセキュリティを強化するための ***推奨事項*** が表示されます。

    たとえば、保護されたサブスクリプションのいずれかの Azure Storage アカウントが仮想ネットワークルールで保護されていない場合は、それらのリソースを強化するための推奨事項が表示されます。 

(1) イニシアチブには、(3) 環境固有の推奨事項を生成する (2) ポリシーが含まれています。

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>推奨事項とポリシーの関係を表示する

前述のように、Defender for Cloud の組み込みの推奨事項は、Azure のセキュリティベンチマークに基づいています。 ほとんどすべての推奨事項には、ベンチマークの要件から派生した基になるポリシーがあります。

推奨事項の詳細を確認するときに、基になるポリシーを確認できると有益な場合が多くあります。 ポリシーでサポートされているすべての推奨事項について、推奨事項の詳細ページの **[ポリシー定義の表示]** リンクを使用して、関連するポリシーの Azure Policy エントリに直接アクセスします。

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="推奨事項をサポートする特定のポリシーが記載されている Azure Policy ページへのリンク。":::

ポリシー定義を表示し、評価ロジックを確認するには、このリンクを使用します。 

[セキュリティの推奨事項に関するリファレンス ガイド](recommendations-reference.md)に記載されている推奨事項の一覧を確認している場合も、ポリシー定義のページへのリンクが表示されます。

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Microsoft Defender for Cloud の推奨事項リファレンスページから、特定のポリシーの [Azure Policy] ページに直接アクセスします。":::


## <a name="next-steps"></a>次のステップ

このページでは、ポリシー、イニシアチブ、および推奨事項の基本的な概念と関係に関する概要を説明しています。 関連情報については、以下をご覧ください。

- [カスタム イニシアチブを作成する](custom-security-policies.md)
- [セキュリティ ポリシーを無効にして推奨事項を無効にする](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Azure Policy でセキュリティ ポリシーを編集する方法を確認する](../governance/policy/tutorials/create-and-manage.md)
