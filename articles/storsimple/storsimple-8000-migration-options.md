---
title: StorSimple 5000-7000 シリーズのデータを移行するためのオプションを評価する | Microsoft Docs
description: StorSimple 5000-7000 シリーズのデータを移行するためのオプションの概要を示します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2018
ms.author: alkohli
ms.openlocfilehash: a1cdf3235674ae651c30fa4f13622b80212abc7d
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099692"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>StorSimple 5000-7000 シリーズのデータを移行するためのオプション 
2019年 7 月に StorSimple 5000-7000 シリーズの[サポートは終了](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)します。 StorSimple 5000-7000 シリーズを実行しているお客様は、他の Azure ファースト パーティのハイブリッド サービスにアップグレードできます。 この記事では、データの移行に使用できる Azure ハイブリッド オプションについて説明します。 

## <a name="migration-options"></a>移行オプション

StorSimple 5000-7000 シリーズには、主に次の 2 つのオプションが用意されています。

- **StorSimple 8000 シリーズへのアップグレード**: StorSimple 8000 シリーズにアップグレードして、StorSimple プラットフォームを引き続き使用します。  このアップグレード パスでは、お客様は 5000-7000 シリーズ デバイスを 8000 シリーズで置き換える必要があります。 データは、移行ツールを使用して、5000-7000 シリーズ デバイスから移行されます。 移行が正常に完了すると、StorSimple 8000 シリーズ デバイスにより、Azure Blob Storage にデータが引き続き格納されます。 

    StorSimple 8000 シリーズを使用してデータを移行する方法の詳細については、「[StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する](storsimple-8000-migrate-from-5000-7000.md)」を参照してください。

- **Azure File Sync に移行**: この新しい移行オプションを使用すると、お客様が自分の組織のファイル共有を Azure Files に格納できます。 その後、これらのファイル共有は、オンプレミス アクセスのために Azure File Sync (AFS) を使用して一元化されます。 AFS は Windows Server ホストにデプロイできます。 以降、実際のデータ移行は、ホスト コピーとして、または移行ツールを使用して実行されます。

    Azure File Sync にデータを移行する方法の詳細については、[StorSimple 5000-7000 シリーズから Azure File Sync へのデータの移行](https://aka.ms/StorSimpleMigrationAFS)に関するページをご覧ください。

## <a name="migration---frequently-asked-questions"></a>移行 - よく寄せられる質問

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. StorSimple 5000 および 7000 シリーズのデバイスのサービスはいつ終了しますか。 

A. StorSimple 5000-7000 シリーズは 2019 年 7 月に[サービスが終了](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series)になります。 2019 年 7 月のサービス終了後、これらのデバイスのソフトウェアとハードウェア両方について、Microsoft ではサポートを提供できなくなります。 お使いのデバイスのデータを移行する計画を今すぐ立て始めることを強くお勧めします。

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Azure に格納したデータはどうなりますか。  

A. 新しいサービスにデータを移行すれば、そのデータは Azure で引き続き使用できます。 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q.  StorSimple デバイスにローカルに格納したデータはどうなりますか。 

A. ローカル デバイスのデータを新しいサービスにコピーするには、移行ドキュメントの説明に従ってください。

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>StorSimple 5000/7000 シリーズ アプライアンスをそのまま使い続けたい場合は、どうなりますか。 

A. サービスは動作し続ける可能性がありますが、Microsoft では、ハードウェアとソフトウェアのサポートを提供できなくなります。 ビジネス継続性を実現するめに移行することを強くお勧めします。

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. StorSimple 5000-7000 シリーズのデバイスからのデータ移行オプションには、どのようなものがありますか。 

A. StorSimple 5000-7000 シリーズのユーザーは、それぞれのシナリオに応じて、次の移行オプションを使用できます。 

 - **8000 シリーズへのアップグレード**: このオプションは、StorSimple プラットフォームを使用し続ける場合に使用します。 
 - **Azure File Sync に移行**: このオプションは、Azure ネイティブ形式に切り替える場合に使用します。 Azure File Sync を使用すると、ファイル共有を一元管理できます。 

ここで紹介していない移行オプションについては、Microsoft サポートにお問い合わせください。

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. 他のストレージ ソリューションへの移行はサポートされていますか。

A. はい。 他のストレージ ソリューションへの移行には、データのホスト コピーを使用できます。

### <a name="q-is-migration-supported-by-microsoft"></a>Q. 移行は Microsoft によってサポートされていますか。 

A. 5000 または 7000 シリーズからの移行は完全にサポートされています。 実際、Microsoft では、移行を開始する前に、サポートに問い合わせることをお勧めしています。 現在、移行操作には Microsoft サポートの支援が必要です。 StorSimple 5000-7000 シリーズのデバイスからデータを移行する場合は、[サポート チケットを開いてください](storsimple-8000-contact-microsoft-support.md)。

### <a name="q-how-does-the-cost-compare-for-the-two-listed-migrations-to-azure-hybrid-services"></a>Q. Azure ハイブリッド サービスへの移行で示されている 2 つのオプションのコストはどのように異なりますか。 

A. 移行のコストは、選択するオプションによって異なります。 移行自体は無料ですが、StorSimple 8000 シリーズにアップグレードする場合は、ハードウェア デバイスのコストがかかります。 同様に、Azure File Sync を使用する場合は、サービスに対するサブスクリプション料金が適用されます。 いずれの場合も、お客様はストレージ コストも継続的に支払う必要があります。 見積もりについては、[サービスごとに Microsoft の料金計算ツール](https://azure.microsoft.com/pricing/#product-picker)をご利用ください。  

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  移行が完了するまでの時間はどのくらいですか。

A. データの移行にかかる時間は、データ量と選択したアップグレード オプションによって異なります。 

## <a name="next-steps"></a>次の手順
 - [StorSimple 5000-7000 シリーズ デバイスのデータを 8000 シリーズ デバイスに移行する](storsimple-8000-migrate-from-5000-7000.md)。
 - [StorSimple 5000-7000 シリーズ デバイスのデータを Azure File Sync に移行する](storsimple-5000-7000-afs-migration.md)
