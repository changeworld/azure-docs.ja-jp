---
title: Azure Security Center でのセキュリティ インシデントの管理 | Microsoft Docs
description: このドキュメントは、Azure Security Center を使用してセキュリティ インシデントを処理するために役立ちます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 1a6dbaeac5355d50edb93a7f215d7f8e88231e98
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615966"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Security Center でのセキュリティ インシデントの管理

セキュリティ アラートのトリアージと調査は、熟練のセキュリティ アナリストでさえ長時間を要することのある作業であり、多くのユーザーにとっては、どこから始めればよいのかもわかりません。 Security Center では、[分析](security-center-detection-capabilities.md)を利用して異なる[セキュリティ アラート](security-center-managing-and-responding-alerts.md)間の情報を結び付け、攻撃キャンペーンとそれに関連するすべてのアラートを単一のビューに表示します。これにより、攻撃者がどのような操作を実行し、どのリソースが影響を受けたのかを迅速に把握することができます。

このトピックでは Security Center のインシデントの概要とアラートを修復する方法について説明します。

## <a name="what-is-a-security-incident"></a>セキュリティ インシデントとは

Security Center でのセキュリティ インシデントとは、 [キル チェーン](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) のパターンに一致するリソースに関するすべての警告を集約したものです。 インシデントは [[セキュリティ アラート]](security-center-managing-and-responding-alerts.md) の一覧に表示されます。 インシデントをクリックすると関連アラートが表示されるので、発生後との詳細を入手できます。

## <a name="managing-security-incidents"></a>セキュリティ インシデントの管理

1. Security Center ダッシュボードで **[セキュリティ アラート]** タイルをクリックします。 インシデントとアラートが一覧表示されます。 セキュリティ インシデントの説明には、他の警告とは異なるアイコンが付いている点に注意してください。

    ![セキュリティ インシデントの表示](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 詳細を表示するには、インシデントをクリックします。 **[セキュリティインシデントが検出されました]** ブレードに詳細が表示されます。 **[一般情報]** セクションには、セキュリティ アラートを始動させたものに関する分析情報があります。 ターゲット リソース、発信元 IP アドレス (該当する場合)。アラートが現在もアクティブかどうか、推奨修復方法などの情報が表示されます。  

    ![Azure Security Center でのセキュリティ インシデントへの対応](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 各アラートの詳細を取得するには、アラートをクリックします。 Security Center から提案される修復方法は、セキュリティ アラートによって異なります。

   > [!NOTE]
   > 同じアラートが 1 つのインシデントに含まれることもあれば、スタンドアロン アラートとして表示されることもあります。

    ![[アラートの詳細]](./media/security-center-incident/security-center-incident-alert.png)

1. 各アラートに対して与えられている修復手順に従います。

アラートの詳細については、[セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関するページを参照してください。

次のトピックでは、リソースの種類に応じてさまざまなアラートを説明します。

* [IaaS Windows マシンのアラート](threat-protection.md#windows-machines)
* [IaaS Linux マシンのアラート](threat-protection.md#linux-machines)
* [Azure App Service のアラート](threat-protection.md#app-services)
* [Azure コンテナーのアラート](threat-protection.md#azure-containers)
* [SQL Database および SQL Data Warehouse のアラート](threat-protection.md#data-sql)
* [Azure Storage のアラート](threat-protection.md#azure-storage)
* [Cosmos DB のアラート](threat-protection.md#cosmos-db)

次のトピックでは、Security Center が、Azure にデプロイされたリソースに追加の保護レイヤーを適用するために、Azure インフラストラクチャとの統合から収集したさまざまなテレメトリを活用する方法について説明します。

* [Azure 管理レイヤー (Azure Resource Manager) のアラート (プレビュー)](threat-protection.md#management-layer)
* [Azure Key Vault のアラート (プレビュー)](threat-protection.md#azure-keyvault)
* [Azure ネットワーク レイヤーのアラート](threat-protection.md#network-layer)
* [その他のサービスからのアラート](threat-protection.md#alerts-other)

## <a name="see-also"></a>関連項目
このドキュメントでは、Security Center でのセキュリティ インシデント機能の使用方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center のセキュリティ アラート](security-center-alerts-overview.md)。
* [セキュリティ アラートの管理](security-center-managing-and-responding-alerts.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
