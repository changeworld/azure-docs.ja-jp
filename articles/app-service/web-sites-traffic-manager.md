---
title: "Azure トラフィック マネージャーによる Azure の Web アプリのトラフィックの制御"
description: "この記事では、Azure の Web アプリに関連して、Azure Traffic Manager の概要情報を提供します。"
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 93645aa5765d533b45fe2266f061ad61c0bf45d7
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Azure トラフィック マネージャーによる Azure の Web アプリのトラフィックの制御
> [!NOTE]
> この記事では、Azure Web Apps に関連して、Microsoft Azure Traffic Manager の概要情報を提供します。 "Azure トラフィック マネージャー自体の詳細については、この記事の最後にある関連情報を参照してください。
> 
> 

## <a name="introduction"></a>はじめに
Azure トラフィック マネージャーを使用すると、Web クライアントからの要求を Azure App Service の Web アプリに振り分ける方法を制御できます。 Web アプリのエンドポイントが Azure トラフィック マネージャーのプロファイルに追加されると、Azure トラフィック マネージャーは Web アプリの状態 (実行中、停止、または削除済み) を追跡して、トラフィックを受信する必要のあるエンドポイントを決定できるようになります。

## <a name="routing-methods"></a>ルーティング方法
Azure Traffic Manager は 3 つのルーティング方法を使用します。 これらの方法について、Azure の Web アプリに関連して、次の一覧で説明します。

* **[優先順位](#priority):** すべてのトラフィックにプライマリ Web アプリケーションを使用し、プライマリまたはバックアップ Web アプリケーションが使用できなくなった場合に備えてバックアップを用意します。
* **[重み付け](#weighted):** 一連の Web アプリに、均等にまたは定義した重みに従ってトラフィックを分散させます。
* **[パフォーマンス](#performance):** 異なる地理的位置に Web アプリケーションがある場合は、最短のネットワーク待機時間という観点から "最も近い" Web アプリケーションを使用します。
* **[地域](#geographic):** ユーザーの DNS クエリが送信された地理的位置に基づいて、ユーザーを特定の Web アプリケーションにルーティングします。 

詳細については、「[Traffic Manager のルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)」を参照してください。

## <a name="web-apps-and-traffic-manager-profiles"></a>Web アプリとトラフィック マネージャーのプロファイル
Web アプリのトラフィック制御を構成するには、Azure トラフィック マネージャーでプロファイルを作成し、前に説明している 3 つの負荷分散方法のいずれかをプロファイルで指定します。その後、トラフィックを制御するエンドポイント (この場合は Web アプリ) をプロファイルに追加します。 Web アプリの状態 (実行中、停止、または削除済み) は定期的にプロファイルに反映されて、その状態に応じて Azure トラフィック マネージャーはトラフィックを振り分けることができます。

Azure トラフィック マネージャーを Azure で使用する場合は、次の点に留意してください。

* 同じリージョンでの Web アプリのみの展開の場合、Web アプリはそのモードには関係なく、フェールオーバーとラウンド ロビンの機能を既に備えています。
* 同じリージョンでの Web アプリのデプロイで、Azure の別のクラウド サービスと連携させる場合、両方の種類のエンドポイントを組み合わせたハイブリッドのシナリオが可能です。
* リージョンごとに 1 つのみの Web アプリ エンドポイントをプロファイルで指定することもできます。 1 つのリージョンのエンドポイントとして Web アプリを選択すると、そのリージョン内の残りの Web アプリはそのプロファイルで選択できなくなります。
* Azure トラフィック マネージャーのプロファイルで指定した Web アプリ エンドポイントは、プロファイルで Web アプリの構成ページの **[ドメイン名]** セクションに表示されますが、そこでは構成できません。
* Web アプリをプロファイルに追加した後、Web アプリのポータル ページのダッシュボードの **[サイトの URL]** には、Web アプリのカスタム ドメインを設定していればその URL が表示されます。 それ以外の場合は、Traffic Manager のプロファイルの URL (`contoso.trafficmgr.com` など) が表示されます。 Web アプリの直接のドメイン名と Traffic Manager の URL の両方が、Web アプリの構成ページの **[ドメイン名]** セクションに表示されます。
* カスタム ドメイン名は予期したとおりに機能しますが、それらのドメイン名を Web アプリに追加するだけでなく、Traffic Manager の URL を参照するように DNS マップを構成する必要もあります。 Azure Web アプリのカスタム ドメイン名の設定については、「[Azure Website のカスタム ドメインの構成](app-service-web-tutorial-custom-domain.md)」を参照してください。
* 標準またはプレミアム モードの Web アプリのみを Azure Traffic Manager のプロファイルに追加できます。

## <a name="next-steps"></a>次のステップ
Azure トラフィック マネージャーの概念と技術的概要については、「 [Traffic Manager Overview (トラフィック マネージャーの概要)](../traffic-manager/traffic-manager-overview.md)」を参照してください。

Web Apps での Traffic Manager の使用の詳細については、ブログ記事「[Using Azure Traffic Manager with Azure Web Sites (Azure Web サイトでの Azure Traffic Manager の使用)](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)」および「[Azure Traffic Manager can now integrate with Azure Web Sites (Azure Traffic Manager と Azure Web サイトの統合が可能になりました)](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)」を参照してください。

