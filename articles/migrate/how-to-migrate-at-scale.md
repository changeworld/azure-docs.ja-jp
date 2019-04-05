---
title: Azure への多数の VM の移行を自動化する | Microsoft Docs
description: Azure Site Recovery を使用して多数の VM を移行するための、スクリプトの使用方法について説明します
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 02/07/2019
ms.author: snehaa
ms.openlocfilehash: 74dabc49dd3d0e38f43dc758204c35ea1c0efd99
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438484"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Azure Site Recovery を使用して VM の移行をスケーリングする

この記事では、Azure Site Recovery を使用して多数の VM を移行するための、スクリプトを使ったプロセスについて説明します。 これらのスクリプトは、GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples) リポジトリからダウンロードできます。 スクリプトは、VMware、AWS、GCP の VM と物理サーバーを Azure に移行するために使用できます。 また、VM を物理サーバーとして移行する場合は、これらのスクリプトを使用して Hyper-V VM を移行することもできます。 スクリプトでは、[こちら](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)に記載されている Azure Site Recovery PowerShell が使用されます。

## <a name="current-limitations"></a>現在の制限事項:
- スクリプトでは現在、非マネージド ディスクへの移行のみがサポートされています
- Standard ディスクへの移行のみがサポートされています
- ターゲット VM のプライマリ NIC に対してのみ、静的 IP アドレスの指定がサポートされています
- スクリプトでは、Azure ハイブリッド特典に関連する入力は受け付けられません。ポータルで、レプリケートされた VM のプロパティを手動で更新する必要があります

## <a name="how-does-it-work"></a>それはどのように機能しますか?

### <a name="prerequisites"></a>前提条件
始める前に、次の手順を実行する必要があります。
- Azure サブスクリプション内に Site Recovery コンテナーが作成されていることを確認します
- 構成サーバーとプロセス サーバーがソース環境にインストールされていることと、コンテナーから環境を検出できることを確認します
- レプリケーション ポリシーが作成されていて、構成サーバーに関連付けられていることを確認します
- 構成サーバーに VM 管理者アカウントを追加したことを確認します (オンプレミスの VM をレプリケートするために使用されます)
- Azure 内にターゲット成果物が作成されていることを確認します
    - ターゲット リソース グループ
    - ターゲット ストレージ アカウント (およびそのリソース グループ)
    - フェールオーバー用のターゲット仮想ネットワーク (およびそのリソース グループ)
    - ターゲット サブネット
    - テスト フェールオーバー用のターゲット仮想ネットワーク (およびそのリソース グループ)
    - 可用性セット (必要な場合)
    - ターゲット ネットワーク セキュリティ グループとそのリソース グループ
- ターゲット VM のプロパティが決定していることを確認します
    - ターゲット VM 名
    - Azure 内のターゲット VM サイズ (Azure Migrate の評価を使用して決定できます)
    - VM 内のプライマリ NIC のプライベート IP アドレス
- GitHub の [Azure PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples) リポジトリからスクリプトをダウンロードします

### <a name="csv-input-file"></a>CSV 入力ファイル
すべての前提条件を満たしたら、移行する各ソース マシンのデータを含んだ CSV ファイルを作成する必要があります。 入力 CSV には、入力の詳細を含んだヘッダー行と、移行する必要がある各コンピューターの詳細を含んだ行を含める必要があります。 すべてのスクリプトは、同じ CSV ファイルを使用するように設計されています。 scripts フォルダーには、参照用のサンプル CSV テンプレートがあります。

### <a name="script-execution"></a>スクリプトの実行
CSV の準備ができたら、次の手順を実行してオンプレミス VM の移行を実行できます。

**手順番号** | **スクリプト名** | **説明**
--- | --- | ---
1 | asr_startmigration.ps1 | csv にリストされているすべての VM のレプリケーションを有効にします。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます
2 | asr_replicationstatus.ps1 | レプリケーションの状態をチェックします。このスクリプトを実行すると、各 VM の状態を含んだ csv が作成されます
3 | asr_updateproperties.ps1 | VM がレプリケート/保護されたら、このスクリプトを使用して VM のターゲット プロパティ (コンピューティングとネットワークのプロパティ) を更新します
4 | asr_propertiescheck.ps1 | プロパティが適切に更新されているかどうかを確認します
5 | asr_testmigration.ps1 |  csv にリストされている VM のテスト フェールオーバーを開始します。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます
6 | asr_cleanuptestmigration.ps1 | テスト フェールオーバーされた VM を手動で検証したら、このスクリプトを使用してテスト フェールオーバー VM をクリーンアップします
7 | asr_migration.ps1 | csv にリストされている VM の計画外フェールオーバーを実行します。このスクリプトを実行すると、各 VM のジョブ詳細を含んだ CSV 出力が作成されます。 このスクリプトでは、アプリケーションの一貫性を保つため、フェールオーバーをトリガーする前にオンプレミス VM をシャットダウンすることはしません。スクリプトを実行する前に、VM を手動でシャットダウンすることをお勧めします。
8 | asr_completemigration.ps1 | VM 上でコミット操作を実行し、ASR エンティティを削除します
9 | asr_postmigration.ps1 | NIC のポスト フェールオーバーにネットワーク セキュリティ グループを割り当てる場合は、このスクリプトを使用してその操作を実行できます。 このスクリプトを実行すると、ターゲット VM 内の 1 つの NIC に NSG が割り当てられます。

## <a name="next-steps"></a>次の手順

Azure Site Recovery を使用してサーバーを Azure に移行する方法について[詳しく知る](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)
