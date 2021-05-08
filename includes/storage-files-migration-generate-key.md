---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075593"
---
サービス データ暗号化キーを使用して、StorSimple Manager サービスから StorSimple デバイスに送信される、顧客の機密データ (ストレージ アカウントの資格情報など) を暗号化します。 IT 組織にストレージ デバイスに関するキー ローテーション ポリシーがある場合は、これらのキーを定期的に変更する必要があります。 キーの変更プロセスは、StorSimple Manager サービスが 1 つのデバイスを管理しているか、または複数のデバイスを管理しているかによって多少異なります。 詳細については、「[StorSimple のセキュリティとデータの保護](../articles/storsimple/storsimple-8000-security.md)」を参照してください。

サービス データ暗号化キーの変更は、次の 3 つの手順からなるプロセスです。

1. Azure Resource Manager 用の Windows PowerShell スクリプトを使用して、サービス データ暗号化キーを変更するデバイスを承認します。
2. StorSimple 用 Windows PowerShell を使用して、サービス データ暗号化キーの変更を開始します。
3. StorSimple デバイスを複数使用している場合は、他のすべてのデバイスでサービス データ暗号化キーを更新します。

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>手順 1:Windows PowerShell スクリプトを使用してサービス データ暗号化キーを変更するデバイスを承認する
通常、デバイスの管理者は、サービス データ暗号化キーを変更する場合、デバイスの承認をサービス管理者に依頼します。 その後、サービス管理者は、キーの変更をデバイスに承認します。

この手順は、Azure Resource Manager ベースのスクリプトを使用して実行します。 サービス管理者は、承認するのに適したデバイスを選択できます。 選択したデバイスは、サービス データ暗号化キー変更プロセスを開始できるようになります。 

スクリプトの使用の詳細については、「[Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)」を参照してください

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>サービス データ暗号化キーの変更を許可できるデバイス
デバイスは、サービス データ暗号化キーの変更開始の承認に先立ち、次の条件を満たす必要があります。

* デバイスがサービス データ暗号化キーの変更の承認の対象となるようにオンラインである必要があります。
* キーの変更が開始されなかった場合は、30 分経過後にもう一度同じデバイスを承認できます。
* 別のデバイスを承認するには、既に承認済みのデバイスによってキーの変更が開始されていないことが条件となります。 新しいデバイスが承認された後に、以前のデバイスが変更を開始することはできません。
* サービス データ暗号化キーのロールオーバーの実行中に、デバイスを承認することはできません。
* サービスに登録されているデバイスの中に、暗号化をロールオーバーしたデバイスと、ロールオーバしていないデバイスがある場合、デバイスを承認できます。 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>手順 2:StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する
この手順は、承認済みの StorSimple デバイスの StorSimple 用 Windows PowerShell インターフェイスで実行されます。

> [!NOTE]
> キーのロールオーバーが完了するまで、StorSimple Manager サービスの Azure Portal では操作を行うことができません。


デバイスのシリアル コンソールを使用して Windows PowerShell インターフェイスに接続している場合は、次の手順を実行します。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>サービス データ暗号化キーの変更を開始するには
1. オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに、次のコマンドを入力します。
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. コマンドレットが正常に完了すると、新しいサービス データ暗号化キーが表示されます。 このキーをコピーし、このプロセスの手順 3. で使用するために保存します。 このキーは、StorSimple Manager サービスに登録されている残りのすべてのデバイスの更新に使用されます。
   
   > [!NOTE]
   > このプロセスは、StorSimple デバイスを承認してから 4 時間以内に開始する必要があります。
   > 
   > 
   
   4 時間を経過すると、この新しいキーはサービスに送信され、サービスに登録されているすべてのデバイスにプッシュされます。 そのようになると、サービスのダッシュボードにアラートが表示されます。 このサービスにより、登録済みのデバイス上でのすべての操作が無効になります。その後、デバイスの管理者は、他のデバイスのサービス データ暗号化キーを更新する必要があります。 ただし、I/O (データをクラウドに送信するホスト) は中断されません。
   
   サービスに登録されているデバイスが 1 台の場合は、ロールオーバー プロセスが完了し、次の手順をスキップできます。 サービスに登録されているデバイスが複数ある場合は、手順 3. に進みます。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>手順 3:他の StorSimple デバイス上のサービス データ暗号化キーを更新する
StorSimple Manager サービスに登録されているデバイスが複数ある場合は、StorSimple デバイスの Windows PowerShell インターフェイスで次の手順を実行する必要があります。 手順 2. で取得したキーを使用して、StorSimple Manager サービスに登録されている残りのすべての StorSimple デバイスを更新する必要があります。

デバイスのサービス データ暗号化を更新するには、次の手順を実行します。

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>物理デバイス上のサービス データ暗号化キーを更新するには
1. StorSimple 用 Windows PowerShell を使用して、コンソールに接続します。 オプション 1 を選択して、フル アクセスでログオンします。
2. コマンド プロンプトに「`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`」と入力します。
3. 「[手順 2. StorSimple 用 Windows PowerShell を使用してサービス データ暗号化キーの変更を開始する](#to-initiate-the-service-data-encryption-key-change)」で取得したサービス データ暗号化キーを指定します。

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>すべての 8010/8020 クラウド アプライアンス上のサービス データ暗号化キーを更新するには
1. [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell スクリプトをダウンロードして設定します。 
2. PowerShell を開き、コマンド プロンプトに次のように入力します。`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

このスクリプトは、デバイス マネージャーにあるすべての 8010/8020 クラウド アプライアンスで、サービス データ暗号化キーが設定されていることを確認します。