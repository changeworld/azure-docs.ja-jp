---
title: "StorSimple デバイス マネージャーを使用したパスワードの変更 | Microsoft Docs"
description: "StorSimple Manager サービスを使用して、StorSimple Snapshot Manager のパスワードとデバイス管理者のパスワードを変更する方法について説明します。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>StorSimple Manager サービスを使用した StorSimple のパスワードの変更
> [!NOTE]
> StorSimple のクラシック ポータルは廃止される予定です。 ご使用の StorSimple デバイス マネージャーは、廃止スケジュールに従い、自動的に新しい Azure Portal に移行されます。 この移行に関しては、メールとポータル通知でお知らせします。 このドキュメントも間もなく廃止されます。 新しい Azure Portal 向けに改訂された記事については、「[Use the StorSimple Manager service to change your StorSimple passwords (StorSimple Manager サービスを使用した StorSimple パスワードの変更)](storsimple-8000-change-passwords.md)」をご覧ください。 この移行についてご質問があれば、[Azure Portal への移行に関する FAQ](storsimple-8000-move-azure-portal-faq.md) のページを参照してください。

## <a name="overview"></a>概要
Azure クラシック ポータルの **[構成]** ページには、StorSimple Manager サービスの管理対象の StorSimple デバイスで再構成することができるすべてのデバイス パラメーターが含まれます。 このチュートリアルでは、 **[構成]** ページを使用して、デバイス管理者のパスワードまたは StorSimple Snapshot Manager のパスワードを変更する方法について説明します。

## <a name="change-the-device-administrator-password"></a>デバイス管理者のパスワードの変更
Windows PowerShell インターフェイスを使用して StorSimple デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。 サービスに初めて StorSimple デバイスを登録した場合、このインターフェイスの初期パスワードは *Password1*です。 データのセキュリティ上、登録プロセスの最後にこのパスワードを変更することが必須となっています。 このパスワードを変更せずに登録プロセスを終了することはできません。 詳細については、「 [手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、Azure クラシック ポータルから変更できます。 デバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-device-administrator-password"></a>デバイス管理者のパスワードを変更するには
1. クラシック ポータルで、デバイスの **[デバイス]**  >  **[構成]** をクリックします。
2. **[デバイス管理者パスワード]** セクションまでスクロールします。 管理者のパスワードは、8 ～ 15 文字で指定してください。 大文字、小文字、数字、特殊文字を 3 つ以上組み合わせたパスワードを使用する必要があります。
3. パスワードを確認入力します。
4. ページの下部にある **[保存]** をクリックします。

これでデバイス管理者のパスワードが更新されました。 この変更されたパスワードを使用して、Windows PowerShell インターフェイスにアクセスすることができます。

## <a name="change-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードの変更
StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードとデバイスの IP アドレスを入力するように求められます。 このパスワードは、Windows PowerShell のインターフェイスを使用して最初に構成されます。 詳細については、「 [手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、クラシック ポータルから変更できます。 StorSimple Snapshot Manager のパスワードを変更するには、以下の手順を実行します。

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager のパスワードを変更するには
1. クラシック ポータルで、デバイスの **[デバイス]**  >  **[構成]** をクリックします。
2. **[StorSimple Snapshot Manager]** セクションまでスクロールします。 14 または 15 文字のパスワードを入力します。 大文字、小文字、数字、特殊文字を 3 つ以上組み合わせたパスワードを使用してください。
3. パスワードを確認入力します。
4. ページの下部にある **[保存]** をクリックします。

StorSimple Snapshot Manager のパスワードが更新されました。

## <a name="next-steps"></a>次のステップ
* [StorSimple のセキュリティの詳細](storsimple-security.md)
* [デバイスの構成を変更する方法の詳細](storsimple-modify-device-config.md)
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理の詳細](storsimple-manager-service-administration.md)

