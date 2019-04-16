---
title: Azure Stack での更新プログラムの管理概要 | Microsoft Docs
description: Azure Stack 統合システムの更新プログラムの管理について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281575"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Azure Stack での更新プログラムの管理概要

*適用対象:Azure Stack 統合システム*

Azure Stack 統合システム用の Microsoft 更新プログラム パッケージは、通常、毎月第 4 火曜日にリリースされます。 更新通知が確実に組織に届くようにする具体的な通知プロセスについては、ご利用の OEM (Original Equipment Manufacturer) にお問い合わせください。 また、サポートが有効なリリースの情報については、このドキュメント ライブラリの「**概要**」 > 「**リリース ノート**」を参照してください。 

Microsoft ソフトウェア更新プログラムの各リリースは、1 つの更新プログラム パッケージとしてバンドルされます。 Azure Stack のオペレーターは、これらの更新プログラム パッケージのインポート、インストール、そのインストール状況の監視を、管理者ポータルから行うことができます。 

また、OEM ベンダーがドライバーやファームウェアの更新プログラムなど、更新プログラムをリリースします。 これらの更新プログラムは、ベンダーから個別のパッケージとして提供されますが、インポート、インストール、および管理方法は、Microsoft の更新プログラム パッケージと同じです。

お使いのシステムのサポートを維持するには、Azure Stack を継続的にアップデートして常に特定のバージョン レベルを保つようにする必要があります。 必ず「[Azure Stack servicing policy (Azure Stack サービス ポリシー)](azure-stack-servicing-policy.md)」を確認してください。

> [!NOTE]
> Azure Stack Development Kit に Azure Stack 更新プログラム パッケージを適用することはできません。 更新プログラム パッケージは統合システム用に設計されています。 詳細については、[ASDK の再デプロイ](https://docs.microsoft.com/azure/azure-stack/asdk)に関する記事を参照してください。

## <a name="the-update-resource-provider"></a>更新リソースプロバイダー

Azure Stack には、Microsoft ソフトウェア更新プログラムの適用を調整する更新リソース プロバイダーが含まれています。 このプロバイダーによって、すべての物理ホスト、Service Fabric アプリケーションとランタイム、すべてのインフラストラクチャ仮想マシンとその関連サービスに更新プログラムが適用されていることが確認されます。

更新プログラムをインストールすると、更新プロセスが Azure Stack 内のさまざまなサブシステム (例: 物理ホスト、インフラストラクチャ仮想マシン) をターゲットとしていく状況の概要を確認できます。

## <a name="plan-for-updates"></a>更新の計画

メンテナンス操作についてユーザーに通知することと、通常のメンテナンス期間はできるだけ勤務時間外にスケジュールすることを強くお勧めします。 メンテナンス操作は、テナントのワークロードとポータル操作の両方に影響を及ぼす可能性があります。

- この更新プログラムのインストールを開始する前に、次のパラメーターを指定して [Test-AzureStack](azure-stack-diagnostic-test.md) を実行して Azure Stack の状態を確認し、見つかったすべての操作上の問題 (すべての警告とエラーを含む) を解決します。 また、アクティブなアラートを確認し、アクションが必要なアラートを解決します。  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>[更新] タイルによる更新プログラムの管理

管理者ポータルから更新プログラムを管理します。 Azure Stack オペレーターは、ダッシュボード内の [更新] タイルを使用して以下のことができます。

- 現在のバージョンなどの重要な情報を表示します。
- 更新プログラムをインストールし、進行状況を監視します。
- 以前にインストールされた更新プログラムの更新履歴を確認します。
- クラウドの現在の OEM パッケージ バージョンを表示
 
## <a name="determine-the-current-version"></a>現在のバージョンの判断

現在のバージョンの Azure Stack は [更新] タイルで表示できます。 タイルを開くには:

1. Azure Stack 管理者ポータルを開きます。
2. **[ダッシュボード]** を選択します。 **[更新]** タイルに現在のバージョンが表示されます。 

    ![既定のダッシュボードの [更新] タイル](./media/azure-stack-updates/image1.png)

    たとえば、スクリーンではバージョンは 1.1903.0.35 です。

## <a name="install-updates-and-monitor-progress"></a>更新プログラムのインストールと進行状況の監視


1. Azure Stack 管理者ポータルを開きます。
2. **[ダッシュボード]** を選択します。 [更新プログラム] タイルを選択します。
3. **[今すぐ更新]** を選択します。

    ![Azure Stack 更新実行の詳細](media/azure-stack-updates/azure-stack-update-button.png)

4.  Azure Stack のさまざまなサブシステムを介して更新プロセスが反復処理されるときに詳細な状態を表示できます。 サブシステムの例として、物理ホスト、Service Fabric、インフラストラクチャ仮想マシン、管理者とユーザーの両方のポータルを提供するサービスなどがあります。 更新プロセスを通じて、更新リソース プロバイダーにより更新 (成功した手順数、進行中の数など) に関するその他の詳細が報告されます。

5. 更新実行の詳細ブレードから **[詳細ログのダウンロード]** を選択して、詳細ログをダウンロードします。

    ![Azure Stack 更新実行の詳細](media/azure-stack-updates/update-run-details.png)

6. 完了したら、更新リソース プロバイダーにより**完了した**ことの確認が提供され、更新プロセスが完了したことと、それにかかった時間が通知されます。 そこから、フィルターを使用して、すべての更新、利用可能な更新、またはインストールされた更新に関する情報を表示できます。

    ![Azure Stack 更新実行の詳細の成功](media/azure-stack-updates/update-success.png)

   更新が失敗した場合、[更新プログラム] タイルにより**要注意**が報告されます。 **[詳細ログのダウンロード]** を使用して、更新プログラムが失敗した可能性がある詳細な状態を取得します。 Azure Stack のログ収集により、診断とトラブルシューティングが容易になります。

## <a name="next-steps"></a>次の手順

- [Azure Stack サービス ポリシー](azure-stack-servicing-policy.md) 
- [Azure Stack でのリージョン管理](azure-stack-region-management.md)
