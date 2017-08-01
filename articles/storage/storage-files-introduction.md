---
title: "Azure File Storage の概要 | Microsoft Docs"
description: "業界標準を使用して Microsoft Cloud でネットワーク ファイル共有を作成および使用できるようにするサービスである Azure File Storage の概要。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 16fdd3aafef1a50554932a0e7843d347182c9b6a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Azure File Storage の概要
Azure File Storage は、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)と [Samba/共通インターネット ファイル システム (CIFS)](https://technet.microsoft.com/library/cc939973.aspx) を使用して、クラウド内のネットワーク ファイル共有を提供します。 Azure ファイル共有は、Windows、macOS、Linux のオンプレミス デプロイなどのクライアント、または Azure 仮想マシンによって、同時にマウントできます。 汎用ストレージ アカウントを使用すると、1 つのアカウントにある Azure File Storage やその他のサービス (BLOB、Azure 仮想マシン ディスク、キューなど) にアクセスできます。



## <a name="videos"></a>ビデオ
| Azure File Storage の概要 (27 分) | Azure File Storage のチュートリアル (5 分)  |
|-|-|
| [![Azure File Storage の概要に関する動画のスクリーンショット - クリックして再生](media/storage-file-storage/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Azure File Storage のチュートリアルのスクリーンショット - クリックして再生](media/storage-file-storage/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Azure File Storage が便利な理由
Azure File Storage を使用すると、オンプレミスまたはクラウドでホストされている Windows Server、Linux、または NAS ベースのファイル サーバーを、OS に依存しないクラウド ファイル共有に置き換えることができます。 これには、次のような利点があります。

* **共有アクセス**。 Azure ファイル共有では、業界標準の SMB プロトコルがサポートされています。そのため、アプリケーションの互換性を気にせずに、オンプレミスのファイル共有を Azure ファイル共有にシームレスに置き換えることができます。 複数のマシン、アプリケーション/インスタンスでファイル システムを共有できることは、共有性を必要とするアプリケーションにとって、Azure File Storage の大きな利点です。 
* **完全管理型**。 Azure ファイル共有は、ハードウェアまたは OS を管理することなく、作成できます。 つまり、重要なセキュリティ アップグレードの際にサーバー OS に修正プログラムを適用したり、故障したハード ディスクを交換したりする作業は必要ありません。
* **スクリプトとツール**。 PowerShell コマンドレットと Azure CLI を使用して、Azure アプリケーションの管理の一環として File Storage 共有を作成、マウント、管理することができます。Azure ファイル共有の作成と管理には、Azure Portal と Azure ストレージ エクスプローラーを使用できます。 
* **回復性**。 Azure File Storage は、当初から、常に使用できることを目的にして構築されています。 オンプレミスのファイル共有を Azure File Storage に置き換えることで、ローカルの停電またはネットワークの問題に対処するために夜間に起き出す必要がなくなります。 
* **慣れているプログラミング方法**。 Azure で実行されているアプリケーションは、ファイル [システム I/O API](https://msdn.microsoft.com/library/system.io.file.aspx) を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。 システム IO API の他に、[Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)または [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api) も使用できます。

Azure ファイル共有は、以下の作業に使用できます。

* **オンプレミスのファイル サーバーの置換**:  
    Azure File Storage を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイス上のファイル共有を完全に置き換えることができます。 Windows、macOS、Linux などの一般的なオペレーティング システムは、世界中のどこからでも、Azure ファイル共有を簡単にマウントできます。

* **アプリケーションの "リフトアンドシフト"**:  
    Azure File Storage を使用すると、アプリケーションの部分間でデータを共有するためにオンプレミスのファイル共有を使用するアプリケーションをクラウドに簡単に "リフトアンドシフト" することができます。 そうなるように、各 VM がファイル共有に接続すると、オンプレミスのファイル共有の場合と同様にファイルを読み書きできるようになります。

* **クラウド開発の簡略化**:  
    Azure File Storage は、新しいクラウド開発プロジェクトを簡略化するために、さまざまな方法で使用することができます。
    * **共有アプリケーションの設定**:  
        分散アプリケーションの一般的なパターンでは、多数の異なる VM からアクセスできる集中管理された場所に構成ファイルが配置されます。 このような構成ファイルを Azure ファイル共有に格納し、すべてのアプリケーション インスタンスで読み取ることができるようになっています。 また、REST インターフェイスを介してこれらの設定を管理することもできるため、世界中から構成ファイルにアクセスできます。

    * **診断の共有**:  
        Azure ファイル共有は、ログ、メトリック、クラッシュ ダンプのような診断ファイルを保存するために使用することもできます。 SMB と REST インターフェイスの両方を介してこれらを使用できるようにすることで、アプリケーションは各種分析ツールを構築または利用して、診断データを処理および分析できます。

    * **開発/テスト/デバッグ**:  
        クラウド内の VM で作業している開発者または管理者には、一連のツールまたはユーティリティが必要になることがよくあります。 各仮想マシンに必要なユーティリティをインストールおよび配信するのは、時間がかかる作業になる場合があります。 Azure File Storage を使用すると、開発者または管理者は好みのツールをファイル共有に格納し、任意の仮想マシンから簡単に接続できます。
        
## <a name="how-does-it-work"></a>それはどのように機能しますか?
Azure ファイル共有の管理は、オンプレミスのファイル共有の管理よりずっと簡単です。 次の図は、Azure File Storage の管理の構造を示しています。

![ファイル構造](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **ストレージ アカウント**: Azure Storage にアクセスするときは必ずストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、Azure Storage の拡張性とパフォーマンスのターゲットに関するページを参照してください。
* **共有**: File Storage 共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて親の共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ファイル共有の合計容量 5 TB まで増やすことができます。
* **ディレクトリ**: ディレクトリの階層 (オプション)。
* **ファイル**: 共有内のファイル。 ファイルのサイズの上限は 1 TB です。
* **URL 形式**: ファイルは、次の URL 形式を使用してアドレス指定できます。  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## <a name="next-steps"></a>次のステップ
* [Azure ファイル共有の作成](storage-file-how-to-create-file-share.md)
* [Windows での接続とマウント](storage-file-how-to-use-files-windows.md)
* [Linux での接続とマウント](storage-how-to-use-files-linux.md)
* [macOS での接続とマウント](storage-file-how-to-use-files-mac.md)
* [FAQ](storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ
* [Azure File Storage: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage 用のツールのサポート
* [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md)
* [Microsoft Azure Storage で AzCopy を使用する方法](storage-use-azcopy.md)
* [Azure Storage での Azure CLI の使用](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="blog-posts"></a>ブログ記事
* [Azure File Storage の一般提供開始](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage (Azure File Storage の内部)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure Files へのデータの移行](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

