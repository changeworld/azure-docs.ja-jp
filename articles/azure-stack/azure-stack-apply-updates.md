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
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: wfayed
ms.openlocfilehash: 2a835e7cd9d4c45c1c39c3c135705cb4dff0e6fb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842188"
---
# <a name="apply-updates-in-azure-stack"></a>Azure Stack で更新を適用する

*適用対象: Azure Stack 統合システム*

管理者ポータルの **[更新]** タイルを使用して、Azure Stack 用の Microsoft または OEM 更新プログラム パッケージを適用できます。 更新プログラム パッケージをダウンロードし、パッケージ ファイルを Azure Stack にインポートした後、更新プログラム パッケージをインストールする必要があります。

## <a name="download-the-update-package"></a>更新プログラム パッケージをダウンロードする

Azure Stack 用の Microsoft または OEM 更新プログラム パッケージを利用できるときに、Azure Stack からアクセスできる場所にパッケージをダウンロードし、パッケージの内容を確認します。 通常、更新プログラム パッケージは、次のファイルで構成されます。

- 自己解凍形式`<PackageName>.exe`ファイル。 このファイルには、更新用のペイロード (Windows Server の最新の累積更新プログラムなど) が含まれます。

- 対応する`<PackageName>.bin`ファイル。 これらのファイルは、*PackageName*.exe ファイルに関連付けられているペイロードの圧縮を提供します。

- `Metadata.xml`ファイル。 このファイルには、更新プログラムに関する重要な情報 (発行元、名前、前提条件、サイズ、サポート パス URL など) が含まれます。

## <a name="import-and-install-updates"></a>更新プログラムをインポートしてインストールする

次の手順は、管理者ポータルで更新プログラム パッケージをインポートしてインストールする方法を示しています。

> [!IMPORTANT]  
> メンテナンス操作についてユーザーに通知することと、通常のメンテナンス期間はできるだけ勤務時間外にスケジュールすることを強くお勧めします。 メンテナンス操作は、ユーザーのワークロードとポータル操作の両方に影響を及ぼす可能性があります。

1. 管理者ポータルで、**[すべてのサービス]** を選択します。 次に、**[DATA + STORAGE]\(データ + ストレージ\)** カテゴリで、**[ストレージ アカウント]** を選択します  (または、フィルター ボックスで「**ストレージ アカウント**」の入力を開始して選択します)。

    ![ポータルでストレージ アカウントを探す場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. フィルター ボックスに「**更新**」と入力し、**updateadminaccount** ストレージ アカウントを選択します。

    ![Updateadminaccount を検索する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. ストレージ アカウントの詳細で、**[サービス]** の **[BLOB]** を選択します。
 
    ![ストレージ アカウントの BLOB を選択する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. **[Blob service]** で、**[+ コンテナー]** を選択してコンテナーを作成します。 名前 (例: *Update-1709*) を入力し、**[OK]** を選択します。
 
     ![ストレージ アカウントにコンテナーを追加する方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. コンテナーが作成されたら、コンテナー名をクリックした後、**[アップロード]** をクリックしてパッケージ ファイルをコンテナーにアップロードします。
 
    ![パッケージ ファイルをアップロードする方法を示している画面](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. **[BLOB のアップロード]** で、フォルダー アイコンをクリックし、更新プログラム パッケージの .exe ファイルを参照し、ファイル エクスプローラー ウィンドウで **[開く]** をクリックします。
  
7. **[BLOB のアップロード]** で、**[アップロード]** をクリックします。 
  
    ![各パッケージ ファイルをアップロードする場所を示している画面](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. *PackageName*.bin ファイルと Metadata.xml ファイルに対して、手順 6 と 7 を繰り返します。 Supplemental Notice.txt ファイルが含まれている場合、そのファイルはインポートしないでください。
9. 終了したら、通知を確認できます (ポータルの右上隅にあるベル アイコン)。 アップロードの完了が通知に示されている必要があります。 
10. ダッシュボードの [更新] タイルに戻ります。 更新プログラムが使用可能であることがタイルに示されている必要があります。 新しく追加された更新プログラム パッケージを確認するには、タイルをクリックします。
11. 更新プログラムをインストールするには、**[準備完了]** とマークされているパッケージを選択し、パッケージを右クリックして **[今すぐ更新]** を選択するか、上部にある **[今すぐ更新]** アクションをクリックします。
12. 更新プログラム パッケージのインストールをクリックしたら、**[Update run details]\(更新実行の詳細\)** 領域でその状態を確認できます。 ここから **[Download full logs]\(満杯になったログのダウンロード\)** をクリックして、ログ ファイルをダウンロードすることもできます。
13. 更新プログラムが完了すると、更新された Azure Stack のバージョンが [更新] タイルに表示されます。

更新プログラムが Azure Stack にインストールされた後、手動でストレージ アカウントから更新プログラムを削除することができます。 Azure Stack は古い更新プログラム パッケージを定期的にチェックし、ストレージから削除します。 Azure Stack で古いパッケージが削除されるまでに 2 週間ほどかかる場合があります。

## <a name="next-steps"></a>次の手順

- [Azure Stack での更新プログラムの管理概要](azure-stack-updates.md)
- [Azure Stack サービス ポリシー](azure-stack-servicing-policy.md)
