---
title: Azure Firewall の FQDN タグの概要
description: Azure Firewall の FQDN タグについて説明します
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6dc7d20d31d9399355b2b3de90ea90f2f3e07af5
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224649"
---
# <a name="fqdn-tags-overview"></a>FQDN タグの概要

FQDN タグは、よく知られている Microsoft サービスに関連付けられた完全修飾ドメイン名 (FQDN) のグループを表します。 アプリケーション ルールで FQDN タグを使用すると、必要な送信ネットワーク トラフィックがファイアウォールを通過するのを許可できます。

たとえば、手動で Windows Update ネットワーク トラフィックがファイアウォールを通過できるようにするには、Microsoft のドキュメントに従って複数のアプリケーション ルールを作成する必要があります。 FQDN タグを使用すると、**Windows Updates** タグを含むアプリケーション ルールを作成できるため、Microsoft Windows Update エンドポイントへのネットワーク トラフィックがファイアウォールを通過できるようになります。

独自の FQDN タグを作成することも、タグに含める FQDN を指定することもできません。 Microsoft では、FQDN タグに含まれる FQDN を管理し、FQDN の変更に合わせてタグを更新しています。 

<!--- screenshot of application rule with a FQDN tag.-->

次の表に、現在使用できる FQDN タグを示します。 これらのタグは Microsoft によって管理されており、定期的に追加される予定です。

|FQDN タグ  |説明  |
|---------|---------|
|Windows Update     |「[ソフトウェアの更新用にファイアウォールを構成する方法](https://technet.microsoft.com/library/bb693717.aspx)」で説明されているように、Microsoft Update への発信アクセスを許可します。|
|Windows 診断|すべての [Windows 診断のエンドポイント](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)への発信アクセスを許可します。|
|Microsoft Active Protection Service (MAPS)|[MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/) への発信アクセスを許可します。|
|App Service Environment (ASE)|ASE プラットフォーム トラフィックへの発信アクセスを許可します。 このタグの対象には、ASE によって作成されたお客様固有のストレージ エンドポイントおよび SQL エンドポイントは含まれません。 これらは、[サービス エンドポイント](../virtual-network/tutorial-restrict-network-access-to-resources.md)を使用して有効にするか、手動で追加する必要があります。|
|Azure Backup|Azure Backup サービスへの発信アクセスを許可します。

> [!NOTE]
> アプリケーション ルールで FQDN タグを選択する場合は、[プロトコル:ポート] フィールドを **[https]** に設定する必要があります。

## <a name="next-steps"></a>次の手順

Azure Firewall のデプロイ方法については、「[チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)」を参照してください。