---
title: Azure Stack で更新プログラムを適用する | Microsoft Docs
description: Azure Stack 統合システム用の Microsoft 更新プログラム パッケージをインポートしてインストールする方法を説明します。
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
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 9eca22f5a594bf3d61b1d68882c4853f4bfc499d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100753"
---
# <a name="apply-updates-in-azure-stack"></a>Azure Stack で更新を適用する

*適用対象: Azure Stack 統合システム*

管理者ポータルの **[更新]** タイルを使用して、Azure Stack 用の Microsoft または OEM 更新プログラム パッケージを適用できます。

統合システム バージョン 1807 以前を使用している場合は、更新プログラム パッケージをダウンロードし、パッケージ ファイルを Azure Stack にインポートした後、更新プログラム パッケージをインストールする必要があります。 手順については、「[パッケージをダウンロードして Azure Stack を更新する](#update-azure-stack-by-downloading-the-package)」をご覧ください

このアップグレードの説明は、Azure Stack 統合システムに適用されます。 Azure Stack 開発システムを使用している場合は、現在のバージョンのインストール パッケージをダウンロードする必要があります。 方法については、「[Azure Stack Development Kit のインストール](./asdk/asdk-install.md)」をご覧ください

## <a name="update-azure-stack"></a>Azure Stack の更新

### <a name="select-and-apply-an-update-package"></a>更新プログラム パッケージを選択して適用する

1. 管理ポータルを開きます。

2. **[ダッシュボード]** を選択します。 **[更新プログラム]** タイルを選択します。

    ![Azure Stack の更新プログラムが使用可能](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. お使いの Azure Stack の現在のバージョンを記録しておきます。 次の完全バージョンに更新することができます。 たとえば、Azure Stack 1811 を実行している場合、次のリリース バージョンは 1901 です。

    ![Azure Stack の更新プログラムを適用する](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. [更新プログラム] の一覧で、使用可能な次のバージョンを選択します。 バージョンの変更点を確認したい場合は、リリース ノート列の **[表示]** を選択して、バージョンのリリース ノート トピックを開くことができます。

5. [今すぐ更新] を選択します。 更新が開始されます。

### <a name="review-update-history"></a>更新履歴を確認する

1. 管理ポータルを開きます。

2. **[ダッシュボード]** を選択します。 **[更新プログラム]** タイルを選択します。

3. **[更新の履歴]** を選択します。

![Azure Stack の更新履歴](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>パッケージをダウンロードして Azure Stack を更新する

統合システム バージョン 1807 以前を使用している場合は、更新プログラム パッケージをダウンロードし、パッケージ ファイルを Azure Stack にインポートした後、更新プログラム パッケージをインストールする必要があります。

## <a name="download-the-update-package"></a>更新プログラム パッケージをダウンロードする

Azure Stack 用の Microsoft または OEM 更新プログラム パッケージを利用できるときに、Azure Stack からアクセスできる場所にパッケージをダウンロードし、パッケージの内容を確認します。 通常、更新プログラム パッケージは、次のファイルで構成されます。

- 自己解凍形式`<PackageName>.exe`ファイル。 このファイルには、更新用のペイロード (Windows Server の最新の累積更新プログラムなど) が含まれます。

- 対応する`<PackageName>.bin`ファイル。 これらのファイルは、*PackageName*.exe ファイルに関連付けられているペイロードの圧縮を提供します。

- `Metadata.xml`ファイル。 このファイルには、更新プログラムに関する重要な情報 (発行元、名前、前提条件、サイズ、サポート パス URL など) が含まれます。

> [!IMPORTANT]  
> Azure Stack 1901 更新プログラム パッケージの適用後、Azure Stack の更新プログラム パッケージのパッケージ形式は、.exe、.bin、.xml の形式から .zip および .xml の形式に変わります。 スタンプが接続された Azure Stack オペレーターは影響を受けません。 切断されている Azure Stack オペレーターは、以下の説明にあるプロセスと同じプロセスを使用して、.xml ファイルと .zip ファイルをインポートできます。

## <a name="import-and-install-updates"></a>更新プログラムをインポートしてインストールする

次の手順は、管理者ポータルで更新プログラム パッケージをインポートしてインストールする方法を示しています。

> [!IMPORTANT]  
> メンテナンス操作についてユーザーに通知することと、通常のメンテナンス期間はできるだけ勤務時間外にスケジュールすることを強くお勧めします。 メンテナンス操作は、ユーザーのワークロードとポータル操作の両方に影響を及ぼす可能性があります。

1. 管理者ポータルで、**[すべてのサービス]** を選択します。 次に、**[DATA + STORAGE]\(データ + ストレージ\)** カテゴリで、**[ストレージ アカウント]** を選択します  (または、フィルター ボックスで「**ストレージ アカウント**」の入力を開始して選択します)。

    ![ポータルでストレージ アカウントを探す場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. フィルター ボックスに「**更新**」と入力し、**updateadminaccount** ストレージ アカウントを選択します。

3. ストレージ アカウントの詳細で、**[サービス]** の **[BLOB]** を選択します。
 
    ![ストレージ アカウントの BLOB を選択する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. **[Blob service]** で、**[+ コンテナー]** を選択してコンテナーを作成します。 名前 (例: *Update-1811*) を入力し、**[OK]** を選択します。
 
     ![ストレージ アカウントにコンテナーを追加する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. コンテナーが作成されたら、コンテナー名をクリックした後、**[アップロード]** をクリックしてパッケージ ファイルをコンテナーにアップロードします。
 
    ![パッケージ ファイルをアップロードする方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. **[BLOB のアップロード]** で、フォルダー アイコンをクリックし、更新プログラム パッケージの .exe ファイルを参照し、ファイル エクスプローラー ウィンドウで **[開く]** をクリックします。
  
7. **[BLOB のアップロード]** で、**[アップロード]** をクリックします。
  
    ![各パッケージ ファイルをアップロードする場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. *PackageName*.bin ファイルと Metadata.xml ファイルに対して、手順 6 と 7 を繰り返します。 Supplemental Notice.txt ファイルが含まれている場合、そのファイルはインポートしないでください。
9. 終了したら、通知を確認できます (ポータルの右上隅にあるベル アイコン)。 アップロードの完了が通知に示されている必要があります。
10. ダッシュボードの [更新] タイルに戻ります。 更新プログラムが使用可能であることがタイルに示されている必要があります。 新しく追加された更新プログラム パッケージを確認するには、タイルをクリックします。
11. 更新プログラムをインストールするには、**[準備完了]** とマークされているパッケージを選択し、パッケージを右クリックして **[今すぐ更新]** を選択するか、上部にある **[今すぐ更新]** アクションをクリックします。
12. 更新プログラム パッケージのインストールをクリックしたら、**[Update run details]\(更新実行の詳細\)** 領域でその状態を確認できます。 ここから **[Download full logs]\(満杯になったログのダウンロード\)** をクリックして、ログ ファイルをダウンロードすることもできます。
13. 更新プログラムが完了すると、更新された Azure Stack のバージョンが [更新] タイルに表示されます。

更新プログラムが Azure Stack にインストールされた後、手動でストレージ アカウントから更新プログラムを削除することができます。 Azure Stack は古い更新プログラム パッケージを定期的にチェックし、ストレージから削除します。 Azure Stack で古いパッケージが削除されるまでに 2 週間ほどかかる場合があります。

## <a name="next-steps"></a>次の手順

- [Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)
- [Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)
