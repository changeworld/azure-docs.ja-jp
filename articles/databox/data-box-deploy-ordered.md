---
title: Azure Data Box の注文方法のチュートリアル | Microsoft Docs
description: Azure Data Box の展開の前提条件と注文方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: bc21ba73ef7e8f5879af2b15787449315f36a3f8
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745314"
---
# <a name="tutorial-order-azure-data-box"></a>チュートリアル:Azure Data Box を注文する

Azure Data Box は、迅速かつ簡単な信頼性の高い方法でオンプレミス データを Azure にインポートできるハイブリッド ソリューションです。 お客様は、Microsoft 提供の 80 TB (使用可能容量) ストレージ デバイスにデータを転送した後、そのデバイスを Microsoft に戻します。 その後、このデータは Azure にアップロードされます。

このチュートリアルでは、Azure Data Box を注文する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * Data Box を展開するための前提条件
> * Data Box を注文する
> * 注文を追跡する
> * 注文をキャンセルする

## <a name="prerequisites"></a>前提条件

デバイスを展開する前に、Data Box サービスとデバイスに関する次の構成の前提条件を完了してください。

### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

* データセンター ネットワークに接続されているホスト コンピューターがあること。 Data Box は、このコンピューターからデータをコピーします。 ホスト コンピューターでは、「[Azure Data Box system requirements](data-box-system-requirements.md)」(Azure Data Box のシステム要件) に記載されている、サポートされるオペレーティング システムが実行されている必要があります。
* お客様のデータセンターには、高速ネットワークが必要です。 少なくとも 1 本の 10 GbE 接続を利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。

## <a name="order-data-box"></a>Data Box を注文する

デバイスを注文するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用して、次の URL にサインインします。[https://portal.azure.com](https://portal.azure.com)
2. **[+ リソースの作成]** をクリックし、*Azure Data Box* を検索します。 **[Azure Data Box]** をクリックします。

   [![Azure Data Box 1 を検索する](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. **Create** をクリックしてください。

4. ご利用のリージョンで Data Box サービスが利用可能かどうかを確認します。 次の情報を入力または選択し、 **[適用]** をクリックします。

    |設定  |値  |
    |---------|---------|
    |サブスクリプション     | Data Box サービス用の EA、CSP、または Azure スポンサー サブスクリプションを選択します。 <br> サブスクリプションは、課金アカウントにリンクされます。       |
    |転送の種類     | **[Azure へインポート]** を選択します。        |
    |ソースの国または地域    |    データが現在存在する国/地域を選択します。         |
    |宛先 Azure リージョン     |     データを転送する Azure リージョンを選択します。        |

5. **[Data Box]** を選択します。 1 つの注文での使用可能な最大容量は 80 TB です。 データ サイズがこれより大きい場合は、複数の注文を作成できます。

      [![Data Box の選択 - オプション 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. **[注文]** で**注文の詳細**を指定します。 次の情報を入力または選択し、 **[次へ]** をクリックします。

    |設定  |値  |
    |---------|---------|
    |名前     |  注文を追跡するためのフレンドリ名を指定します。 <br> 名前の長さは 3 ～ 24 文字で、文字、数字、ハイフンを使うことができます。 <br> 名前の最初と最後は、文字か数字とする必要があります。      |
    |Resource group     |    既存のグループを使用するか、新しいグループを作成します。 <br> リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。         |
    |宛先 Azure リージョン     | ストレージ アカウントのリージョンを選択します。 <br> 詳細については、[利用可能なリージョン](data-box-overview.md#region-availability)に関する記事をご覧ください。        |
    |保存先     | ストレージ アカウント、マネージド ディスク、またはその両方から選択します。 <br> 指定した Azure リージョンに基づいて、既存のストレージ アカウントのフィルター処理された一覧から 1 つまたは複数のストレージ アカウントを選択します。 Data Box は、最大 10 個のストレージ アカウントにリンクできます。 <br> 新しい**汎用 v1 アカウント**、**汎用 v2 アカウント**、または **Blob Storage アカウント**を作成することもできます。 <br>仮想ネットワークに対するストレージ アカウントがサポートされます。 セキュリティで保護されたストレージ アカウントと Data Box サービスとを連携させるには、ストレージ アカウントのネットワーク ファイアウォール設定内で、信頼できるサービスを有効にします。 詳細については、[Azure Data Box を信頼できるサービスとして追加する](../storage/common/storage-network-security.md#exceptions)方法を参照してください。|

    保存先としてストレージ アカウントを使用している場合は、以下のようなスクリーンショットが表示されます。

    ![ストレージ アカウントの Data Box 注文](media/data-box-deploy-ordered/order-storage-account.png)

    Data Box を使用して、オンプレミスの VHD からマネージド ディスクを作成する場合は、以下の情報も指定する必要があります。

    |設定  |値  |
    |---------|---------|
    |リソース グループ     | オンプレミスの VHD からマネージド ディスクを作成する場合は、新しい リソース グループを作成します。 既存のリソース グループを使用できるのは、Data Box サービスでマネージド ディスクの Data Box 注文を作成するときに、そのリソース グループがすでに作成されていた場合に限られます。 <br> 複数のリソース グループを指定するときは、各リソース グループをセミコロンで区切ります。 サポートされるリソース グループは、最大 10 個です。|

    ![マネージド ディスクの Data Box 注文](media/data-box-deploy-ordered/order-managed-disks.png)

    マネージド ディスクに指定されたストレージ アカウントは、ステージング ストレージ アカウントとして使用されます。 VHD は、Data Box サービスにより、ページ BLOB としてステージング ストレージ アカウントにアップロードされた後、マネージド ディスクに変換され、リソース グループに移動されます。 詳細については、「[Azure へのデータのアップロードを確認する](data-box-deploy-picked-up.md#verify-data-upload-to-azure)」を参照してください。

7. **[配送先住所]** に、お客様の氏名、会社の名前と郵送先住所、および有効な電話番号を入力します。 **[住所の確認]** をクリックします。 配送先住所でサービスが利用可能かどうかが確認されます。 指定した配送先住所でサービスを利用できる場合は、その旨が通知されます。

   注文が正しく処理されると、[自己管理の出荷] が選択されている場合、電子メール通知が届きます。 自己管理の出荷の詳細については、[自己管理の出荷の使用](data-box-portal-customer-managed-shipping.md)に関するページをご覧ください。

8. 出荷の詳細が正しく検証されたら、 **[次へ]** をクリックします。

9. **[通知の詳細]** で、電子メール アドレスを指定します。 指定した電子メール アドレスに、注文の状態の更新に関する電子メール通知が送信されます。

    グループの管理者が辞めた場合も引き続き通知を受け取ることができるように、グループ メールを使用することをお勧めします。

10. 注文、連絡先、通知、プライバシー条項に関する情報の**概要**を確認します。 プライバシー条項への同意に対応するボックスをオンにします。

11. **[注文]** をクリックします。 注文が作成されるまで数分かかります。

## <a name="track-the-order"></a>注文を追跡する

注文後、Azure portal から注文の状態を追跡できます。 Data Box の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルでは、注文は、 **[注文済み]** 状態で表示されます。

デバイスが使用できない場合は、通知されます。 デバイスが使用可能な場合は、Microsoft が配送用のデバイスを特定し、配送の準備をします。 デバイスの準備中に、次のアクションが実行されます。

* デバイスに関連付けられているストレージ アカウントごとに SMB 共有が作成されます。
* 各共有で、ユーザー名やパスワードなどのアクセス資格情報が生成されます。
* デバイスのロック解除に役立つデバイス パスワードも生成されます。
* Data Box は、任意の時点でのデバイスへの不正アクセスを防ぐためにロックされます。

デバイスの準備が完了すると、ポータルでは、注文が **[処理済み]** 状態で表示されます。

![処理された Data Box の注文](media/data-box-overview/data-box-order-status-processed.png)

次に、Microsoft は、デバイスを準備して地域の運送業者を通じて発送します。 デバイスが出荷されると、お客様に追跡番号が送信されます。 ポータルには、**出荷済み**状態の注文が表示されます。

![発送された Data Box の注文](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>注文をキャンセルする

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。

注文は、注文した後、注文の状態が [処理済み] とマークされるまでの間の任意の時点でキャンセルできます。

キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * Data Box を展開するための前提条件
> * Data Box を注文する
> * 注文を追跡する
> * 注文をキャンセルする

次のチュートリアルに進んで、Data Box を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を設定する](./data-box-deploy-set-up.md)
