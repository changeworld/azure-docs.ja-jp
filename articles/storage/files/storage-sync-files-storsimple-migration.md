---
title: StorSimple から Azure File Sync への移行
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple は提供を終了した製品であり、Azure File Sync がこのソリューションの移行先となります。 移行の概念を確認し、移行に関するカスタマイズされた支援が必要な場合は、AzureFiles@microsoft.com にご連絡ください。
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895078"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple の Azure File Sync への移行

StorSimple は、提供が終了された Microsoft 製品です。 この製品とそのクラウド サービスの延長サポートは、2022 年末まで継続されます。
すぐに StorSimple からの移行計画を開始することが重要です。

StorSimple アプライアンスの移行先となる、既定の戦略的な長期 Azure サービスは Azure File Sync です。これは、StorSimple を超える優れた機能のスーパーセットを備えた、一般提供されている Azure サービスです。

## <a name="full-cloud-side-migration-with-limited-downtime"></a>ダウンタイムを抑えた完全なクラウド側での移行
この記事では、移行の開始方法の概念について説明します。
StorSimple から Azure File Sync に移行する必要があるお客様は、お客様自身でその作業を進める必要がない点を知っておいてください。

> [!IMPORTANT]
> Microsoft はお客様の移行を支援できるよう取り組んでいます。 カスタマイズした移行計画について、および移行中のサポートについては、AzureFiles@microsoft .com にメールをお送りください。

## <a name="migration-approach"></a>移行のアプローチ
Azure File Sync への移行はクラウド側で開始され、オンプレミスへの影響は最小限に抑えられ、ダウンタイムも抑制されます。
以下の概念は、StorSimple 8000 シリーズのアプライアンスを対象としています。
StorSimple 7000 シリーズから移行する必要がある場合、最初の手順として、Microsoft の対応する 8000 シリーズの代替デバイスへの無料アップグレードを行うことが求められます。
AzureFiles@microsoft.com にご連絡いただくと、適切な数の代替デバイスを準備できるよう Microsoft が支援します。

### <a name="general-approach"></a>一般的なアプローチ
![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "オンプレミスの StorSimple アプライアンスに置き換わる新しいオンプレミスの Windows Server への、一時的な仮想アプライアンスと Windows Server を介するクラウド側での移行について説明しています。")

1. オンプレミスの StorSimple アプライアンスのボリューム複製を一時的な StorSimple 仮想アプライアンスにマウントします。
2. iSCSI 経由で仮想アプライアンスを一時的な Azure VM に接続します。
3. Azure File Sync を一時的な Windows Server VM に インストールします。サーバーで同期を構成する前に、この移行のための特定のレジストリ キーを設定することも必要です。
    * StorSimple ボリュームの共有数に応じて、同じ数の Azure ファイル共有をデプロイしてください。 (Azure ファイル共有はストレージ アカウントごとに 1 つずつデプロイすることをお勧めします。)
    * Windows Server のクラウド VM 上の個々の共有と Azure ファイル共有の間の同期を構成します。 (1 対 1 のマッピング)
    * 必要に応じて、クラウドを使った階層化を有効にし、ローカル サーバーをオンプレミスのパフォーマンス キャッシュとして追加します。 この手順が必要になるのは、オンプレミスの StorSimple を、Windows Server と Azure File Sync のクラウドを使った階層化を利用した、機能豊富なローカル キャッシュに置き換える場合です。 オンプレミスの Windows Server は物理マシンまたはクラスターの場合、または仮想マシンの場合があります。 データセットのサイズと同じサイズのストレージを用意する必要はありません。 必要となるのは、最も頻繁にアクセスされるファイルをローカルにキャッシュするために十分なストレージだけです。

## <a name="minimizing-downtime"></a>ダウンタイムの最小化
上記の手順 3 の後でも、StorSimple オンプレミス アプライアンスはユーザーとアプリケーションによって引き続き使用されています。 そのため、初期のボリューム複製から同期されたファイル セットは、同期が完了した時点では多少古くなります。
ダウンタイムを最小限に抑える方法は、ボリューム複製プロセスからの同期を繰り返し、その繰り返しのたびに同期の完了が早まるようにすることです。これは、ボリューム複製間の変更が徐々に少なくなることによって可能になります。
このプロセスは、ダウンタイムとして許容できると判断した時間内にボリューム複製からの同期が終了できるようになるまで繰り返すことができます。
これが該当するようになったら、StorSimple アプライアンスに変更を加えないようにユーザーとアプリケーションをブロックします。 ダウンタイムが開始します。
別のボリューム複製を、接続されているサーバーと同期します。
ユーザーとアプリケーションに対し、新しい Azure File Sync 対応の Windows Server へのアクセスを確立します。
以前の StorSimple アプライアンスから新しい Windows Server への切り替えをアプリやユーザーに対して透過的に行うように、DFS 名前空間をデプロイまたは調整することを検討してください。
移行が完了しました。

## <a name="migration-goal"></a>移行の目標
移行が完了したら、一時的な StorSimple 仮想アプライアンスと Azure VM をプロビジョニング解除できます。

さらに、ユーザーとアプリケーションが既に Windows Server にアクセスするようになっているため、StorSimple オンプレミス アプライアンスもプロビジョニング解除できます。
ここで残されるものを次の図に示します。 Azure File Sync の標準デプロイには、多数の Azure ファイル共有と、Azure File Sync を介してそれらに接続された Windows Server が含まれます。1 つのサーバーで、複数の異なるローカル フォルダを別個のファイル共有に同時に接続できることに注意してください。
また、データをブランチ オフィスにキャッシュする必要がある場合は、1 つの Azure ファイル共有を多数の異なるサーバーに同期できます。 また、オンプレミスのストレージ スペースをより効率的に使用するために、クラウドを使った階層化ポリシーを最適化できるかどうかも確認してください。

![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "移行完了後の目標を示す図。これは、ユーザーとアプリケーションがクラウド内または Windows Server 上のファイルにアクセスしている、オンプレミスの Windows Server と同期された多数のファイル共有を表しています。")

## <a name="next-steps"></a>次の手順
Azure Files と Azure File Sync についてよく理解してください。移行を成功させるには、Azure File Sync の用語とデプロイ パターンを理解することが重要です。 この概要の記事にあるすべての手順に関する詳細情報が用意されています。 移行の計画や実行の際にカスタマイズした支援が必要な場合は、Microsoft にご連絡ください。

> [!IMPORTANT]
> Microsoft はお客様の移行を支援できるよう取り組んでいます。 カスタマイズした移行計画について、および移行中のサポートについては、AzureFiles@microsoft .com にメールをお送りください。

## <a name="additional-resources"></a>その他のリソース
ターゲット サービスとしての Azure File Sync には、Azure File Sync を初めて使用する場合に読むことをお勧めする 2 つの基本的なドキュメントが用意されています。
* [Azure File Sync - 概要](storage-sync-files-planning.md)
* [Azure File Sync - デプロイ ガイド](storage-sync-files-deployment-guide.md)

Azure Files は Azure のストレージ サービスであり、ファイル共有をサービスとして提供します。 VM または関連する VM ストレージに対するお支払いや保守は不要です。
* [Azure Files - 概要](storage-files-introduction.md)
* [Azure Files - Azure ファイル共有のデプロイ方法](storage-how-to-create-file-share.md)