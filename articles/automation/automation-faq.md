---
title: Azure Automation に関する FAQ
description: この記事では、Azure Automation についてよく寄せられる質問に対する回答を提供します。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a245c858c15a8a33b6347c86124a7b45f0c4290
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450896"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation についてよく寄せられる質問

この Microsoft FAQ は、Azure Automation についてよく寄せられる質問の一覧です。 その機能について他にご質問がある場合は、ディスカッション フォーラムにアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>Update Management を使用すると、予期しない OS レベルのアップグレードを防ぐことができますか?

はい。 詳細については、「[更新プログラムの除外](./update-management/manage-updates-for-vm.md#exclude-updates)」を参照してください。

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>Update Management で Linux マシンに重要な更新プログラムまたはセキュリティ更新プログラムが適用されないのはなぜですか?

更新プログラムを Linux マシンにデプロイする場合、更新プログラムの分類を選択できます。 このオプションにより、更新プログラムがフィルター処理され、指定した条件を満たすものに絞られます。 詳細については、「[Linux の更新プログラムの分類](./update-management/manage-updates-for-vm.md#linux-update-classifications)」を参照してください。

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>Update Management を使用して更新プログラムを Azure テナント全体にデプロイできますか?

はい。 手順については、「[Azure テナント全体に更新プログラムをデプロイする](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants)」を参照してください。

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Automation ではどの Python 3 バージョンがサポートされていますか?

クラウド ジョブの場合、Python 3.8 がサポートされています。 コードが異なるバージョン間で互換性がある場合は、3.x バージョンのスクリプトとパッケージが動作する可能性があります。

Windows Hybrid Runbook Worker 上のハイブリッド ジョブの場合、使用したい任意の 3.x バージョンをインストールすることを選択できます。 Linux Hybrid Runbook Worker 上のハイブリッド ジョブの場合、DSC OMSConfig と Linux Hybrid Worker を実行するために、そのマシンにインストールされている Python 3 バージョンに依存します。 バージョン 3.6 をインストールすることをお勧めします。ただし、Python 3 のバージョン間でメソッド シグネチャまたはコントラクトに破壊的変更がない場合には、異なるバージョンでも機能します。

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 と Python 3 の Runbook は同じ Automation アカウントで実行できますか?

はい。Python 2 と Python 3 の Runbook を同じ Automation アカウントで使用することに制限はありません。  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>既存の Python 2 Runbook およびパッケージの Python 3 への移行に関して、どのような予定がありますか?

Azure Automation では、Python 2 Runbook およびパッケージを Python 3 に移行する予定はありません。 この移行は、お客様ご自身で実行する必要があります。 既存および新しい Python 2 Runbook とパッケージは、引き続き機能します。

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>Python 3 環境では、既定でどのようなパッケージがサポートされますか?

Azure パッケージ 4.0.0 です。 詳細については、「[Python 3 パッケージの管理](python-3-packages.md)」を参照してください。

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Python 2 パッケージを参照する Python 3 Runbook (またはその逆) を実行した場合、どうなりますか?

Python 2 と Python 3 の実行環境は異なります。 Python 2 Runbook を実行している間は Python 2 パッケージのみをインポートでき、Python 3 についても同様です。

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2 と Python 3 の Runbook およびパッケージを区別するにはどうすればよいですか?

Python 3 は新しい Runbook の定義であり、これによって Python 2 と Python 3 の Runbook を区別できます。 同様に、別のパッケージの種類が Python 3 パッケージに導入されています。

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Python2 と Python3 の両方がインストールされている場合、Hybrid Runbook Worker は実行する Python のバージョンをどのようにして認識しますか?

Windows Runbook Worker では、Python 2 Runbook を実行するときに、`PYTHON_2_PATH` 環境変数が最初に検索され、それが有効な実行可能ファイルを参照しているかどうかが検証されます。 たとえば、インストール フォルダーが `C:\Python2` の場合、`C:\Python2\python.exe` が有効なパスであるかどうかがチェックされます。 見つからない場合は、`PATH` 環境変数が検索され、同様のチェックが実行されます。

Python 3 の場合、`PYTHON_3_PATH` 環境変数が最初に検索された後、`PATH` 環境変数にフォールバックされます。

「[複数の Python バージョン](automation-runbook-types.md#multiple-python-versions)」を参照してください。

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Hybrid Runbook Worker では Python インタープリターをどのようにして見つけますか?

Python モジュールの検索は、前述のように環境変数によって制御されます。

## <a name="is-python-3-supported-in-source-control"></a>ソース管理で Python 3 はサポートされていますか?

いいえ。 ソース管理は、Python 3 では現在サポートされていません。 既定では、Python Runbook は Python 2 Runbook として同期されます。

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Runbook 作成者は、Azure サンドボックスで使用可能な Python パッケージをどのようにして確認できますか?

「[サンドボックスで使用可能なパッケージを特定する](python-3-packages.md#identify-available-packages-in-sandbox)」を参照してください。

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>複数のモジュールがある場合、Runbook 作成者は、使用するパッケージ モジュールのバージョンをどのようにして設定すればよいですか?

「[Python 3 パッケージの管理](python-3-packages.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

こちらでご質問の回答が見つからない場合は、次のソースでさらに質問と回答を参照できます。

- [Azure Automation](/answers/topics/azure-automation.html)
- [フィードバック フォーラム](https://feedback.azure.com/d365community/forum/721a322e-bd25-ec11-b6e6-000d3a4f0f1c)
