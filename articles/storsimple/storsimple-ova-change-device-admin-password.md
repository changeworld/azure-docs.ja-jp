<properties 
   pageTitle="StorSimple 仮想デバイスの管理者パスワードを変更する |Microsoft Azure"
   description="Azure クラシック ポータルまたは StorSimple Virtual Array の Web UI を使用して、デバイス管理者のパスワードを変更する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/02/2016"
   ms.author="alkohli" />

# StorSimple Virtual Array デバイス管理者パスワードを変更する

## 概要

Windows PowerShell インターフェイスを使用して StorSimple 仮想デバイスにアクセスする際、デバイス管理者のパスワードの入力が求められます。StorSimple デバイスを初めてプロビジョニングして起動したときの既定のパスワードは *Password1* です。データのセキュリティ上、既定のパスワードは初回サインイン時にリセットされ、パスワードを変更するよう求められます。

デバイスを運用環境にデプロイした後は、ローカル Web UI または Azure クラシック ポータルを使用して、いつでもデバイス管理者のパスワードを変更できます。それぞれの手順については、この記事で説明します。

## Azure クラシック ポータルを使用してパスワードを変更する

Azure クラシック ポータルを使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### Azure クラシック ポータルを使用してデバイス管理者のパスワードを変更するには

1. ポータルで、デバイスの **[デバイス]**、**[構成]** の順にクリックします。

2. **[デバイス管理者パスワード]** セクションまでスクロールします。管理者のパスワードは、8 ～ 15 文字で指定してください。大文字、小文字、数字、および特殊文字を組み合わせたパスワードを使用する必要があります。

3. パスワードを確認入力します。

4. ページの下部にある **[保存]** をクリックします。

これでデバイス管理者のパスワードが更新されました。この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。

## StorSimple Virtual Array の Web UI を使用してパスワードを変更する

ローカル Web UI を使用してデバイス管理者のパスワードを変更するには、以下の手順を実行します。

#### ローカル Web UI を使用してデバイス管理者のパスワードを変更するには

1. ローカル Web UI で、デバイスの **[メンテナンス]**、**[パスワードの変更]** の順にクリックします。

    ![password1 の変更](./media/storsimple-ova-change-device-admin-password/image40.png)

2. **現在のパスワード**を入力します。

3. **新しいパスワード**を入力します。パスワードは 8 文字以上で、大文字、小文字、数字、および特殊文字の 4 種類の文字のうち 3 種類の文字を含める必要があります。

    直近 24 個のパスワードは使用できません。

3. 確認のためにパスワードを再入力します。

    ![password2 の変更](./media/storsimple-ova-change-device-admin-password/image41.png)

4. ページの下部にある **[適用]** をクリックします。新しいパスワードが適用されます。パスワードの変更が失敗した場合は、次のエラーが表示されます。

    ![パスワード エラー](./media/storsimple-ova-change-device-admin-password/image42.png)

    パスワードが正常に更新されると、通知が表示されます。この変更されたパスワードを使用して、デバイスにローカルにアクセスすることができます。

## 次のステップ

[StorSimple Virtual Array 管理](storsimple-ova-web-ui-admin.md)の詳細を確認します。

<!---HONumber=AcomDC_0309_2016-->