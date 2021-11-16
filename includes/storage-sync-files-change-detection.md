---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 70fa104ef285ecffe11940d04deb451a9cdf39ee
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866728"
---
Azure Portal または SMB を使用して Azure ファイル共有に加えられた変更は、サーバー エンドポイントに対する変更とは異なり、検出とレプリケーションが即座に行われることはありません。 Azure Files にはまだ変更通知/ジャーナルがないため、ファイルが変更されたときに自動的に同期セッションを開始する方法はありません。 Windows Server では、Azure File Sync は [Windows USN ジャーナル](/windows/win32/fileio/change-journals)を使用して、ファイルが変更されたときに同期セッションを自動的に開始します。

Azure ファイル共有に対する変更を検出するために、Azure File Sync には、*変更検出ジョブ* と呼ばれるスケジュールされたジョブがあります。 変更検出ジョブは、ファイル共有内のすべてのファイルを列挙した後、ファイルの同期バージョンと比較します。 変更検出ジョブによってファイルが変更されていると判断された場合に、Azure File Sync は同期セッションを開始します。 変更検出ジョブは 24 時間ごとに実行されます。 変更検出ジョブは Azure ファイル共有内のすべてのファイルを列挙することで機能するため、変更の検出は、大きな名前空間のほうが小さな名前空間よりも時間がかかります。 大規模な名前空間の場合、変更されたファイルを判断するのに 24 時間ごとに 1 回より長くなる場合があります。

Azure ファイル共有で変更されたファイルを直ちに同期したければ、**Invoke-AzStorageSyncChangeDetection** PowerShell コマンドレットを使用すると、Azure ファイル共有における変更の検出を手動で開始できます。 このコマンドレットは、なんらかの自動プロセスによって Azure ファイル共有に変更が加えられたり、管理者によって (ファイルやディレクトリを共有に移動するなどの) 変更が実行されたりするシナリオを意図したものです。 エンド ユーザーの変更に関しては、Azure File Sync エージェントを IaaS VM にインストールし、エンド ユーザーに IaaS VM 経由でファイル共有にアクセスしてもらうことをお勧めします。 そのようにすることで、すべての変更がすぐに他のエージェントと同期され、Invoke-AzStorageSyncChangeDetection コマンドレットを使う必要はありません。 詳細については、[Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) のドキュメントを参照してください。

>[!NOTE]
>**Invoke-AzStorageSyncChangeDetection** PowerShell コマンドレットは、最大 10,000 個の項目のみを検出できます。 その他の制限事項については、[Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) のドキュメントを参照してください。

>[!NOTE]
>REST を使用して Azure ファイル共有に加えられた変更は、SMB の最終更新時刻を更新するものではなく、同期による変更とは見なされません。

Windows Server 上のボリュームに対する USN のような変更検出を Azure ファイル共有に追加するための調査を行っています。 今後この機能の開発を優先的に進めるために、[Azure コミュニティ フィードバック](https://feedback.azure.com/d365community/idea/26f8aa9d-3725-ec11-b6e6-000d3a4f0f84)で投票をお願いします。
