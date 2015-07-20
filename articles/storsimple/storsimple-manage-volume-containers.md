<properties 
   pageTitle="StorSimple ボリューム コンテナーの管理"
   description="StorSimple Manager サービスの [ボリューム コンテナー] ページを使用して、ボリューム コンテナーを追加、変更、または削除する方法について説明します。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/13/2015"
   ms.author="v-sharos" />

# StorSimple ボリューム コンテナーの管理

## 概要

このチュートリアルでは、StorSimple Manager サービスを使用して、StorSimple ボリューム コンテナーを作成、管理する方法について説明します。

Microsoft Azure StorSimple デバイスのボリューム コンテナーには、ストレージ アカウント、暗号化、および帯域幅の使用量の設定を共有する、1 つ以上のボリュームが含まれています。1 つのデバイスに、すべてのボリュームに対応する複数のボリューム コンテナーを含めることができます。

また、ボリューム コンテナーには次の属性があります。

- **ボリューム** – ボリューム コンテナー内に含まれる、仮想プロビジョニングされた StorSimple ボリューム。ボリューム コンテナーには、最大で 256 台の仮想プロビジョニングされた StorSimple ボリュームを含めることができます。

- **暗号化** – ボリューム コンテナーごとに定義できる暗号化キー。このキーは、StorSimple デバイスからクラウドに送信されるデータの暗号化に使用します。ミリタリーグレードの AES-256 ビット キーは、ユーザー入力のキーで使用します。データをセキュリティで保護するために、クラウド ストレージの暗号化を常に有効にすることをお勧めします。

- **ストレージ アカウント** – クラウド ストレージのサービス プロバイダーにリンクされるストレージ アカウント。ボリューム コンテナーに存在するすべてのボリュームで、このストレージ アカウントが共有されます。ストレージ アカウントを既存の一覧から選択することもできますが、ボリューム コンテナーを作成した時点で新しいアカウントを作成し、後でそのアカウントのアクセス資格情報を指定することもできます。

- **クラウドの帯域幅** – デバイスのデータがクラウドに送信されるときに、デバイスが使用する帯域幅。このコンテナーを定義するときに 1 ～ 1,000 Mbps の値を指定すると、帯域幅の制御を適用できます。デバイスで使用できる帯域幅をすべて使用する場合は、このフィールドを [無制限] に設定します。帯域幅テンプレートを作成、適用し、スケジュールに基づいて帯域幅を割り当てることもできます。

![[ボリューム コンテナー] ページ](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

次の手順は、StorSimple の **[ボリューム コンテナー]** ページを使用して、次の一般的な操作を完了する方法について説明するための手順です。

- ボリューム コンテナーを追加する 
- ボリューム コンテナーを変更する 
- ボリューム コンテナーを削除する 

## ボリューム コンテナーを追加する

ボリューム コンテナーを作成するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]


## ボリューム コンテナーを変更する

ボリューム コンテナーを変更するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]


## ボリューム コンテナーを削除する

ボリューム コンテナーには複数のボリュームが含まれています。ボリューム コンテナーを削除する前に、ボリューム コンテナーに含まれるすべてのボリュームを削除する必要があります。ボリューム コンテナーを削除するには、次の手順を実行します。

[AZURE.INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## 次のステップ

詳細については、「[StorSimple ボリュームの管理](storsimple-manage-volumes.md)」を参照してください。
 

<!---HONumber=July15_HO2-->