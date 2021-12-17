---
title: パートナー センターのコマーシャル マーケットプレース申請 API の概要
description: コマーシャル マーケットプレース申請 API の概要について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056592"
---
# <a name="commercial-marketplace-submission-api-overview"></a>コマーシャル マーケットプレース申請 API の概要

API を使用してプログラムによるクエリを実行し、プランの申請を作成して、公開します。 API は、お使いのアカウントで多くのプランを管理していて、これらのプランの申請プロセスを自動化および最適化したい場合に役立ちます。

## <a name="types-of-apis"></a>API の種類

利用できる申請 API には、次の 2 つのセットがあります。

- **パートナー センター申請 API** - パートナー センターを通じて公開するために、コンシューマー製品と商用製品全体で機能する API の共通セット。 この API のセットには、新しい機能が継続的に追加されます。 この API と統合する方法の詳細については、「[パートナー センター申請 API のオンボード](submission-api-onboard.md)」を参照してください。 
- **レガシ Cloud パートナー ポータル API** - 非推奨の Cloud パートナー ポータルから引き継がれた API。パートナー センターに統合され、引き続き機能します。 この API のセットはメンテナンス モードのみです。パートナー センターで導入された新機能はサポートされていない可能性があり、パートナー センターに移行する前に既に統合されていた既存の製品にのみ使用する必要があります。 Cloud パートナー ポータル API を引き続き使用する方法の詳細については、「[Cloud パートナー ポータルの API リファレンス](cloud-partner-portal-api-overview.md)」を参照してください。

各プランの種類でサポートされる申請 API については、次の表を参照してください。

| プランの種類 | レガシ Cloud パートナー ポータル API のサポート | パートナー センター申請 API のサポート |
| --- | :---: | :---: |
| Azure Application |  | &#x2714; |
| Azure Container | &#x2714; |  |
| Azure Virtual Machine | &#x2714; |  |
| コンサルティング サービス | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| IoT Edge モジュール | &#x2714; |  |
| マネージド サービス | &#x2714; |  |
| Power BI アプリ | &#x2714; |  |
| サービスとしてのソフトウェア |  | &#x2714; |
|

Microsoft 365 Office アドイン、Microsoft 365 SharePoint ソリューション、Microsoft 365 Teams アプリ、Power BI ビジュアルでは、申請 API はサポートされていません。

## <a name="next-steps"></a>次のステップ

- 必要に応じて、プランの種類に適した API リンクにアクセスします
