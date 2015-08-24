<properties 
   pageTitle="StorSimple Manager サービスを使用した StorSimple のパスワードの変更 | Microsoft Azure" 
   description="StorSimple Manager サービスを使用して、StorSimple Snapshot Manager のパスワードとデバイス管理者のパスワードを変更する方法について説明します。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/11/2015"
   ms.author="v-sharos@microsoft.com"/>

# StorSimple Manager サービスを使用した StorSimple のパスワードの変更

## 概要 

管理ポータルの **[構成]** ページには、StorSimple Manager サービスの管理対象の StorSimple デバイスで再構成することができるすべてのデバイス パラメーターが含まれます。このチュートリアルでは、**[構成]** ページを使用して、StorSimple Snapshot Manager のパスワードまたはデバイス管理者のパスワードを変更する方法について説明します。

## StorSimple Snapshot Manager のパスワードの変更

StorSimple Snapshot Manager ソフトウェアは Windows ホスト上に常駐し、管理者が、ローカル スナップショットとクラウド スナップショットの形式で StorSimple デバイスのバックアップを管理することを可能にします。

StorSimple Snapshot Manager でデバイスを構成するとき、ストレージ デバイスを認証するためのパスワードとデバイスの IP アドレスを入力するように求められます。このパスワードは、Windows PowerShell のインターフェイスを使用して最初に構成されます。詳細については、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)」の「[手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、管理ポータルから変更できます。StorSimple Snapshot Manager のパスワードを変更するには、以下の手順を実行します。

#### StorSimple Snapshot Manager のパスワードを変更するには

1. ポータルで、デバイスの **[デバイス]**、**[構成]** の順にクリックします。

2. **[StorSimple Snapshot Manager]** セクションまでスクロールします。14 または 15 文字のパスワードを入力します。大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用してください。

3. パスワードを確認入力します。

4. ページの下部にある **[保存]** をクリックします。

StorSimple Snapshot Manager のパスワードが更新されました。
 
## デバイス管理者のパスワードの変更

Windows PowerShell インターフェイスを使用して StorSimple デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。サービスに初めて StorSimple デバイスを登録した場合、このインターフェイスの初期パスワードは *Password1* です。データのセキュリティ上、登録プロセスの最後にこのパスワードを変更することが必須となっています。このパスワードを変更せずに登録プロセスを終了することはできません。詳細については、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough.md)」の「[手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)」を参照してください。

登録時に Windows PowerShell インターフェイスを使用して最初に設定したパスワードは、管理ポータルから変更できます。デバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### デバイス管理者のパスワードを変更するには

1. ポータルで、デバイスの **[デバイス]**、**[構成]** の順にクリックします。

2. **[デバイス管理者パスワード]** セクションまでスクロールします。管理者のパスワードは、8 ～ 15 文字で指定してください。大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用する必要があります。

3. パスワードを確認入力します。

4. ページの下部にある **[保存]** をクリックします。

これでデバイス管理者のパスワードが更新されました。この変更されたパスワードを使用して、Windows PowerShell インターフェイスにアクセスすることができます。

## 次のステップ

[StorSimple のセキュリティの詳細を確認する](storsimple-security.md)。

[デバイスの構成を変更する方法について確認する](storsimple-modify-device-config.md)。

<!---HONumber=August15_HO7-->