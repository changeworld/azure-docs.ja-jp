---
title: Power BI アプリケーション プラン - Azure Marketplace | Microsoft Docs
description: Power BI アプリケーションを Microsoft AppSource Marketplace に発行する方法について説明します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: a8474f6dd276c592fce27c8bd90706fe5f126519
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666193"
---
# <a name="power-bi-application-offer"></a>Power BI アプリケーション プラン

|                  |                                |
|------------------|--------------------------------|
| このセクションでは、Power BI アプリケーションを Microsoft [AppSource Marketplace](https://appsource.microsoft.com/) に発行する方法について説明します。 Power BI アプリケーションでは、Power BI コンテンツがパッケージ化され、データフローを使用して一般的なデータ ストレージ内のデータにレポートとダッシュ ボードが接続されます。 さらにアプリを、AppSource を介して他のテナントにデプロイすることができます。 | ![Power BI アイコン](./media/powerbi-icon.png) |


このセクションは、次の主要部分に分かれています。

-   [前提条件](./cpp-prerequisites.md) - Power BI アプリ プランを作成または発行するための技術要件およびビジネス要件を示します。
-   [Power BI アプリ プランの作成](./cpp-create-offer.md) - [Cloud パートナー ポータル](https://cloudpartner.azure.com)を使用して Power BI アプリ プラン エントリを新規に作成するために必要な手順を示します。
-   [Power BI アプリ プランの発行](./cpp-publish-offer.md) - AppSource への発行のために新しいプランを送信する方法と、既存のプランを更新する方法について説明します。


## <a name="publishing-steps"></a>発行手順

次の図は、Power BI アプリ プランを発行するための手順の概要を示したものです。

![Power BI アプリの発行手順](media/publishing-steps.png)

Power BI アプリの発行プロセス

1. Power BI でサービス アプリケーションを作成します (この結果、パッケージ インストール URL が作成されます)。  この URL はプラン用の技術資産を表します。  詳細については、「[Power BI とは](https://go.microsoft.com/fwlink/?linkid=2028636)」を参照してください。
2. 次のようなビジネス資産を収集および作成します。
    - マーケティング資料
    - 法的ドキュメントおよびサポート ドキュメント
3.  プランを作成する - Cloud パートナー ポータルを使用することにより、オファーの説明、マーケティング資料、法務文書、サポート情報、資産の仕様など、オファーに関する詳細情報を構成します。
4.  プランを認定して発行する - この送信によって発行プロセスが開始されます。
    - AppSource オンボーディング チームによってアプリケーションのテスト、検証、および認定が行われます。 
    - 認定されたら、そのアプリケーションをテスト環境で確認してからリリースする必要があります。

これらの手順がすべて実行されると、アプリは AppSource 上で "提供開始" となります。

## <a name="next-steps"></a>次の手順

上記の手順を開始する前に、Power BI アプリを AppSource に発行するための[要件](./cpp-prerequisites.md)を満たす必要があります。
