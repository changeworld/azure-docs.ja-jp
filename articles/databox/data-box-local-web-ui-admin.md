---
title: Microsoft Azure Data Box ローカルWeb UI 管理 | データ内の Microsoft Docs
description: ローカル Web UI を使用して Data Box デバイスを管理する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: c0793cd4adc5359aaa2282eb2505c257315c056f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793395"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>ローカル Web UI を使用して Data Box を管理する

この記事では、Data Box で実行できる一部の構成と管理タスクについて説明します。 Data Box の管理は、Azure portal UI またはデバイスのローカル Web UI から行うことができます。 この記事では、ローカル Web UI を使用して実行できるタスクについて説明します。

Data Box のローカル Web UI は、デバイスの初期構成に使用されます。 ローカル Web UI を使用して、Data Box のシャットダウンまたは再起動、診断テストの実行、ソフトウェアの更新、コピー ログの表示、および Microsoft サポートのログ パッケージの生成を行うこともできます。

この記事には次のチュートリアルが含まれています。

- サポート パッケージの生成
- デバイスのシャットダウンと再起動
- デバイスの使用可能な容量の表示
- チェックサム検証のスキップ 

## <a name="generate-support-package"></a>サポート パッケージの生成

デバイスの問題が発生した場合は、システム ログからサポート パッケージを作成できます。 Microsoft サポートでは、このパッケージを使用して問題のトラブルシューティングを行います。 サポート パッケージを作成するには、次の手順を実行します。

1. ローカル Web UI で、**[サポートにお問い合わせ]** に移動し、**[サポート パッケージの作成]** をクリックします。

    ![サポート パッケージの作成 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. サポート パッケージが収集されます。 この操作は、数分かかります。

    ![サポート パッケージの作成 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. サポート パッケージの作成が完了したら、**[サポート パッケージのダウンロード]** をクリックします。 

    ![サポート パッケージの作成 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. ダウンロードする場所を参照して選択します。 フォルダを開いて内容を表示します。

    ![サポート パッケージの作成 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>デバイスのシャットダウンと再起動

ローカル Web UI を使用して、Data Box をシャットダウンまたは再起動できます。 再起動する前に、ホストの共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 これにより、データ破損の可能性を最小限に抑えられます。 デバイスをシャットダウンするときに、データのコピーが進行中でないことを確認してください。

Data Box をシャットダウンするには、次の手順を実行します。

1. ローカル Web UI で、**[シャット ダウンまたは再起動]** に移動します。
2. **[シャットダウン]** をクリックします。

    ![Data Box のシャット ダウン 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 確認を求められたら、**[OK]** をクリックして続行します。

    ![Data Box のシャット ダウン 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

デバイスがシャットダウンしたら、フロントパネルの電源ボタンを使用して、デバイスをオンにします。

Data Box を再起動するには、次の手順を実行します。

1. ローカル Web UI で、**[シャット ダウンまたは再起動]** に移動します。
2. **[Restart (再開)]** をクリックします。

    ![Data Box の再起動 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 確認を求められたら、**[OK]** をクリックして続行します。

   デバイスは、シャットダウンしてから再起動します。

## <a name="view-available-capacity-of-the-device"></a>デバイスの使用可能な容量の表示

デバイス ダッシュボードを使用して、デバイスの使用可能な容量と使用済み容量を表示できます。 

1. ローカル Web UI で、**[ダッシュボードの表示]** に移動します。
2. **[接続とコピー]** の下に、デバイスの空き容量と使用済み容量が表示されます。

    ![使用可能な容量の表示](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>チェックサム検証のスキップ

発送の準備をするときに、既定ではチェックサムがデータ用に生成されます。 特定のまれなケースにおいて、データ型 (小さいファイル サイズ) によっては、パフォーマンスが非常に遅くなることがあります。 このような場合は、チェックサムをスキップできます。 

パフォーマンスに重大な影響がない限り、チェックサムを無効にしないことを強くお勧めします。

1. デバイスのローカル Web UI の右上隅にある [設定] に移動します。

    ![チェックサムを無効にする](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **[無効]** をクリックしてチェックサム検証を無効にします
3. **[Apply]** をクリックします。

## <a name="next-steps"></a>次の手順

- [Azure portal から Data Box を管理する](data-box-portal-admin.md)方法について説明します。

