---
title: Azure Stack での Azure App Service サーバー ロールの容量計画 | Microsoft Docs
description: Azure Stack での Azure App Service サーバー ロールの容量計画
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: f54481fe59df21b500ee860d1e9a202ed32bdd87
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097150"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Azure Stack での Azure App Service サーバー ロールの容量計画

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

稼働準備済みの Azure App Service on Azure Stack のデプロイを設定するには、システムでサポートする容量について計画する必要があります。  

この記事では、実稼働環境のデプロイに使用すべき最小限のコンピューティング インスタンス数とコンピューティング SKU に関するガイダンスを示します。

これらのガイドラインを使用して、App Service の容量戦略を計画できます。 Azure Stack の今後のバージョンでは、App Service の高可用性オプションが提供される予定です。

| App Service サーバー ロール | 推奨の最小インスタンス数 | 推奨のコンピューティング SKU|
| --- | --- | --- |
| コントローラー | 2 | A1 |
| フロントエンド | 2 | A1 |
| 管理 | 2 | A3 |
| 発行元 | 2 | A1 |
| Web Worker - 共有 | 2 | A1 |
| Web Worker - 専用 | 層ごとに 2 個 | A1 |

## <a name="controller-role"></a>コントローラー ロール

**推奨の最小構成**: 2 つの A1 Standard のインスタンス

Azure App Service コントローラーは通常、CPU、メモリ、ネットワーク リソースをそれほど消費しません。 ただし、高可用性のためには、2 つのコントローラーが必要です。 コントローラー 2 つは、許容される最大コント ローラー数でもあります。 2 番目の Web サイト コントローラーは、デプロイ中にインストーラーから直接作成できます。

## <a name="front-end-role"></a>フロントエンド ロール

**推奨の最小構成**: 2 つの A1 Standard のインスタンス

フロントエンドは、Web ワーカーの可用性に応じて、Web Worker に要求をルーティングします。 高可用性のためには複数のフロントエンドが必要で、3 つ以上保有できます。 容量計画では、各コアで 1 秒あたり約 100 の要求を処理できるように考慮してください。

## <a name="management-role"></a>管理ロール

**推奨の最小構成**: 2 つの A3 Standard のインスタンス

Azure App Service 管理ロールは、App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを担当します。 通常、運用環境では、管理サーバー ロールに約 4 GB の RAM のみが必要となります。 ただし、多くの管理タスク (Web サイトの作成など) が実行される場合は、CPU レベルが高くなる可能性があります。 高可用性のためには、このロールに複数のサーバーが割り当てられている必要があり、サーバーごとに少なくとも 2 つのコアが必要です。

## <a name="publisher-role"></a>パブリッシャー ロール

**推奨の最小構成**: 2 つの A1 Standard のインスタンス

多くのユーザーが同時に公開する場合は、パブリッシャー ロールの CPU 使用率が高くなる可能性があります。 高可用性のためには、複数のパブリッシャー ロールを使用できるようにします。  パブリッシャーは、FTP または FTPS のトラフィックのみを処理します。

## <a name="web-worker-role"></a>Web Worker ロール

**推奨の最小構成**: 2 つの A1 Standard のインスタンス

高可用性のためには、少なくとも 4 つの Web Worker ロールが必要です。共有 Web サイト モード用に 2 つと、提供する予定の各専用ワーカー用に 2 つです。 共有および専用のコンピューティング モードでは、さまざまなレベルのサービスがテナントに提供されます。 次のような顧客が多い場合は、より多くの Web Worker が必要になる可能性があります。

- 専用コンピューティング モードの worker 層 (リソースを大量に使用) を使用している。
- 共有コンピューティング モードで稼働している。

ユーザーは、専用コンピューティング モード SKU 用の App Service プランを作成すると、その App Service プランで指定した数の Web Worker を使用できなくなります。

ユーザーに従量課金プラン モデルで Azure Functions を提供するには、共有 Web Worker をデプロイする必要があります。

使用する共有 Web Worker ロールの数を決定する場合、次の考慮事項を検討します。

- **メモリ**: メモリは Web Worker ロールの最も重要なリソースです。 メモリが不足していると、仮想メモリがディスクからスワップされるときの Web サイトのパフォーマンスに影響します。 各サーバーで、オペレーティング システム用に約 1.2 GB の RAM が必要となります。 このしきい値を超える RAM を Web サイトの実行で使用できます。
- **アクティブな Web サイトの割合**: 通常は、Azure App Service on Azure Stack デプロイ内のアプリケーションの約 5% がアクティブです。 ただし、特定の時点でのアクティブなアプリケーションの割合は、それよりも高い場合も低い場合もあります。 アクティブなアプリケーションの割合が 5% の場合、Azure App Service on Azure Stack のデプロイに配置するアプリケーションの最大数は、次の数より少なくする必要があります。
  - アクティブな Web サイトの数の 20 倍 (5 x 20 = 100)。
- **平均メモリ占有領域**: 運用環境で計測されるアプリケーションの平均メモリ占有領域は約 70 MB です。 この占有領域を使用して、すべての Web Worker ロールのコンピューターまたは VM 全体で割り当てられるメモリは、次のように計算できます。

    *プロビジョニング済みアプリケーションの数 * 70 MB * 5% - (Web Worker ロールの数 * 1044 MB)*

   たとえば、10 個の Web Worker ロールを実行している環境に 5,000 個のアプリケーションがある場合、各 Web Worker ロール VM に 7,060 MB の RAM が必要です。

   5,000 * 70 * 0.05 – (10 * 1044) = 7060 (= 約 7 GB)

   さらに worker インスタンスを追加する方法については、[worker ロールの追加](azure-stack-app-service-add-worker-roles.md)に関する記事をご覧ください。

## <a name="file-server-role"></a>ファイル サーバー ロール

ファイル サーバー ロールでは、開発とテスト用にスタンドアロン ファイル サーバーを使用して、たとえば Azure Stack Development Kit で Azure App Service をデプロイするときなどに、<https://aka.ms/appsvconmasdkfstemplate> のテンプレートを使用できます。 運用環境では、事前構成済みの Windows ファイル サーバーか、事前構成済みの Windows 以外のファイル サーバーを使用する必要があります。

運用環境では、ファイル サーバー ロールが集中的なディスク I/O を行います。 ファイル サーバー ロールがユーザー Web サイトのコンテンツとアプリケーション ファイルすべてを保存するため、このロール用に次のいずれかを事前構成しておく必要があります。

- Windows ファイル サーバー
- Windows ファイル サーバー クラスター
- Windows 以外のファイル サーバー
- Windows 以外のファイル サーバー クラスター
- NAS (ネットワーク接続ストレージ) デバイス

詳細については、[ファイル サーバーのプロビジョニング](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[Azure Stack 上の App Service を開始する前に](azure-stack-app-service-before-you-get-started.md)
