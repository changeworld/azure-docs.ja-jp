---
title: Azure で StorSimple デバイス マネージャー サービスをデプロイする | Microsoft Docs
description: サービスの作成、削除、および移行と、サービス登録キーの管理のために必要な手順について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076566"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>StorSimple 8000 シリーズ デバイスの StorSimple デバイス マネージャー サービスをデプロイする

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 サービスの作成後は、StorSimple デバイス マネージャー サービスに接続されているすべてのデバイスを、このサービスを使用して 1 か所から集中的に管理できるため、管理上の負担を最小限にできます。

このチュートリアルでは、サービスの作成、削除、および移行と、サービス登録キーの管理のために必要な手順について説明します。 この記事に記載されている内容は、StorSimple 8000 デバイスにのみ適用されます。 StorSimple Virtual Array の詳細については、「[StorSimple Virtual Array の StorSimple デバイス マネージャー サービスをデプロイする](storsimple-virtual-array-manage-service.md)」を参照してください。

> [!NOTE]
> -  Azure portal では、Update 5.0 以降を実行しているデバイスがサポートされます。 デバイスが最新の状態でない場合は、すぐに Update 5 をインストールしてください。 詳細については、「[Update 5 のインストール](storsimple-8000-install-update-5.md)」をご覧ください。 
> - StorSimple Cloud Appliance (8010/8020) を使っている場合は、クラウド アプライアンスを更新できません。 最新バージョンのソフトウェアを使って、Update 5.0 で新しいクラウド アプライアンスを作成してから、作成した新しいクラウド アプライアンスにフェールオーバーします。 
> - Update 4.0 以前を実行しているすべてのデバイスでは、管理機能が制限されます。 

## <a name="create-a-service"></a>サービスの作成
StorSimple デバイス マネージャー サービスを作成するには、以下が必要です。

* エンタープライズ契約によるサブスクリプション
* アクティブな Microsoft Azure ストレージ アカウント
* アクセス管理で使用する課金情報

使用できるのはエンタープライズ契約によるサブスクリプションのみです。 サービスの作成時に既定のストレージ アカウントを生成することもできます。

単一のサービスで複数のデバイスを管理できます。 ただし、1 つのデバイスを複数のサービスに割り当てることはできません。 大企業は、そのようなサービス インスタンスを複数持つことで、さまざまなサブスクリプション、組織、デプロイの場所に対応することができます。 

> [!NOTE]
> StorSimple 8000 シリーズ デバイスと StorSimple Virtual Array の管理には、StorSimple デバイス マネージャー サービスの個別のインスタンスが必要です。

サービスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


それぞれの StorSimple デバイス マネージャー サービスに対して、以下の属性が存在します。

* **[名前]** – StorSimple Manager デバイス マネージャー サービスの作成時に割り当てられた名前。 **サービス名は、サービスの作成後に変更することはできません。これは、Azure Portal で名前を変更できない他のエンティティ (デバイス、ボリューム、ボリューム コンテナー、バックアップ ポリシーなど) も同様です。**
* **[状態]** – サービスの状態。**アクティブ**、**作成中**、または **オンライン** のいずれか。
* **[場所]** – StorSimple デバイスがデプロイされる地理的な場所。
* **[サブスクリプション]** – サービスに関連付けられている課金サブスクリプション。

## <a name="delete-a-service"></a>サービスの削除

サービスを削除する際は、接続されているデバイスでそのサービスが使用されていないことを先に確認します。 サービスが使用されている場合は、該当するデバイスを非アクティブにします。 非アクティブ化操作によってデバイスとサービス間の接続は切断されますが、クラウド内のデバイスのデータは保持されます。

> [!IMPORTANT]
> いったんサービスを削除すると、その操作を元に戻すことはできません。 このサービスを使用していたデバイスは、別のサービスで使用する前に工場出荷時の既定値にリセットする必要があります。 このとき、デバイス上のローカル データや構成は失われます。

サービスを削除するには次の手順を実行します。

### <a name="to-delete-a-service"></a>サービスを削除するには

1. 削除するサービスを検索します。 **[リソース]** アイコンをクリックし、検索する適切な用語を入力します。 検索結果で、削除するサービスをクリックします。

    ![削除するサービスを検索する](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. この操作で、StorSimple デバイス マネージャー サービスのブレードに移動します。 **[削除]** をクリックします。

    ![サービスの削除](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. 確認通知が表示されたら、 **はい** をクリックします。 サービスが削除されるまで数分かかる場合があります。

    ![削除の確定](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>サービス登録キーを取得する

正常にサービスを作成した後、そのサービスに StorSimple デバイスを登録します。 初めて StorSimple デバイスを登録する場合は、サービス登録キーが必要です。 既存の StorSimple サービスに追加のデバイスを登録するには、登録キーとサービス データ暗号化キー (最初のデバイスの登録中に生成される) の両方が必要です。 サービス データ暗号化キーの詳細については、「 [StorSimple セキュリティ](storsimple-8000-security.md)」をご覧ください。 StorSimple デバイス マネージャーのブレードで **[キー]** にアクセスすることで、登録キーを取得できます。

次の手順を実行して、サービス登録キーを取得します。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

サービス登録キーは、安全な場所に保管してください。 このキーは、サービスに追加のデバイスを登録するときにサービス データ暗号化キーと共に必要です。 サービス登録キーを取得したら、StorSimple 用 Windows PowerShell インターフェイスを介してデバイスを構成する必要があります。

この登録キーを使用する方法については、「[手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

## <a name="regenerate-the-service-registration-key"></a>サービス登録キーを再生成する
キー ローテーションを実行する必要があるとき、またはサービス管理者の一覧が変更されたときは、サービス登録キーを再生成する必要があります。 キーを再生成した場合、新しいキーはその後のデバイスの登録に対してのみ使用されます。 既に登録されているデバイスは、再生成の影響を受けません

次の手順を実行して、サービス登録キーを再生成します。

### <a name="to-regenerate-the-service-registration-key"></a>サービス登録キーを再生成するには
1. **[StorSimple デバイス マネージャー]** ブレードで、 **[管理]&gt;** **[キー]** の順に移動します。
    
    ![[キー] ブレードに移動する](./media/storsimple-8000-manage-service/regenregkey2.png)

2. **[キー]** ブレードで、 **[再生成]** をクリックします。

    ![[再生成] をクリック](./media/storsimple-8000-manage-service/regenregkey3.png)
3. **[サービス登録キーの再生成]** ブレードで、キーを再生成するときに必要な操作を確認します。 これ以降にこのサービスに登録されるデバイスはすべて、新しい登録キーを使用します。 **[再生成]** をクリックして確定します。 登録が完了すると、その旨が通知されます。

    ![再生成を確認する](./media/storsimple-8000-manage-service/regenregkey4.png)

4. 新しいサービス登録キーが表示されます。

5. このキーをコピーし、このサービスに新しいデバイスを登録するときのために保存します。



## <a name="change-the-service-data-encryption-key"></a>サービス データ暗号化キーの変更
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Update 5.0 より前のバージョンを実行しているデバイスでサポートされる操作
Azure Portal では、Update 5.0 以降を実行している StorSimple デバイスのみがサポートされます。 古いバージョンを実行しているデバイスのサポートは限定的です。 Azure Portal に移行した後は、次の表を使用して、Update 5.0 以前のバージョンを実行しているデバイス上でどの操作がサポートされているかを把握してください。

| Operation                                                                                                                       | サポートされています      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| デバイスの登録                                                                                                               | はい            |
| 全般、ネットワーク、セキュリティなどのデバイス設定の指定                                                                | はい            |
| 更新プログラムのスキャン、ダウンロード、およびインストール                                                                                             | はい            |
| デバイスの非アクティブ化                                                                                                               | はい            |
| デバイスの削除                                                                                                                   | はい            |
| ボリューム コンテナーの作成、変更、および削除                                                                                   | いいえ             |
| ボリュームの作成、変更、および削除                                                                                             | いいえ             |
| バックアップ ポリシーの作成、変更、および削除                                                                                      | いいえ             |
| 手動バックアップの取得                                                                                                            | いいえ             |
| スケジュールされたバックアップの作成                                                                                                         | 適用なし |
| バックアップセットからの復元                                                                                                        | いいえ             |
| Update 3.0 以降を実行しているデバイスへの複製 <br> ソース デバイスは Update 3.0 より前のバージョンを実行しています。                                | はい            |
| Update 3.0 より前のバージョンを実行しているデバイスへの複製                                                                          | いいえ             |
| ソース デバイスとしてのフェールオーバー <br> (Update 3.0 より前のバージョンを実行しているデバイスから、Update 3.0 以降を実行しているデバイスに)                                                               | はい            |
| ターゲット デバイスとしてのフェールオーバー <br> (Update 3.0 より前のソフトウェア バージョンを実行しているデバイスに)                                                                                   | いいえ             |
| アラートのクリア                                                                                                                  | はい            |
| バックアップ ポリシー、バックアップ カタログ、ボリューム、ボリューム コンテナー、監視チャート、ジョブ、およびクラシック ポータルで作成されたアラートの表示 | はい            |
| デバイス コントローラーのオンとオフ                                                                                              | はい            |


## <a name="next-steps"></a>次のステップ
* [StorSimple デプロイのプロセスの詳細](storsimple-8000-deployment-walkthrough-u2.md)
* [StorSimple ストレージ アカウントの管理の詳細](storsimple-8000-manage-storage-accounts.md)
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理方法](storsimple-8000-manager-service-administration.md)
