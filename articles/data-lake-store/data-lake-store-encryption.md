---
title: "Azure Data Lake Store での暗号化 | Microsoft Docs"
description: "Azure Data Lake Store における暗号化とキーの交換のしくみについて説明します"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: ja-jp
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store でのデータの暗号化

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Azure Data Lake Store での暗号化の概要

Azure Data Lake Store (ADLS) での暗号化を使用すると、データを保護し、企業のセキュリティ ポリシーを実装できるほか、規制上のコンプライアンス要件を満たすことができます。 この記事では設計の概要を示し、Data Lake Store でデータ暗号化が実装されるしくみの技術的な側面について説明します。

ADLS では、"既定でオン" になる、保存データの "透過的" な暗号化がサポートされています。 これらの用語の意味をここで少し詳しく説明します。

* 既定でオン: 新しい Azure Data Lake Store アカウントを作成する際に、既定の設定で暗号化が有効になります。 その後、Data Lake Store に格納されているデータは永続メディアに格納される前に必ず暗号化されます。 これはすべてのデータに対して実行されます。また、この動作をアカウント作成後に変更することはできません。
* 透過的: データを保存する前の暗号化とデータを取得する前の復号化は ADLS によって自動的に行われます。 暗号化の構成と管理は、管理者によって Data Lake Store レベルで行われます。 データ アクセス API に対する変更は行われません。そのため、暗号化のために Data Lake Store とやり取りするアプリケーションとサービスに変更を加える必要はありません。

Data Lake Store では転送中のデータ (つまり移動中のデータ) も常に暗号化されます。 データは永続メディアに格納される前に暗号化されるうえ、転送中または移動中も HTTPS (Secure Sockets Layer 経由の HTTP) を使用して常にセキュリティ保護されます。 HTTPS は、Data Lake Store REST インターフェイスでサポートされる唯一のプロトコルです。

![図 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Azure Data Lake Store による暗号化の設定

Azure Data Lake Store の暗号化はアカウントの作成中に設定され、常に既定で有効になります。 お客様は、キーの管理を自分で行うか Azure Data Lake Store に任せるか (既定) を選択できます。

Azure Data Lake Store による暗号化を設定する方法については、[概要](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)に関するページを参照してください。

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Azure Data Lake Store 内部における暗号化のしくみ

### <a name="master-encryption-keys"></a>マスター暗号化キー

Azure Data Lake Store では、マスター暗号化キー (MEK) を管理するモードが 2 つあります。 マスター暗号化キーの使用方法については、後ほど詳細に説明します。 ここでは、マスター暗号化キーがトップレベル キーであると考えてください。 Data Lake Store に格納されているデータを復号化するには、マスター暗号化キーにアクセスする必要があります。

マスター暗号化キーを管理するモードには次の 2 つがあります。

1.    サービス管理キー
2.    顧客管理キー

どちらのモードでも、マスター暗号化キーは Azure Key Vault に格納されてセキュリティ保護されます。 Azure Key Vault は、安全性の高い完全管理型の Azure サービスであり、暗号化キーの保護に使用できます。 Azure Key Vault の詳細については、[こちら](https://azure.microsoft.com/services/key-vault)を参照してください。

次の図では、MEK を管理する 2 つのモードで使用できる機能を簡単に比較しています。

|  | サービス管理キー | 顧客管理キー |
| --- | --- | --- |
|データをどのように格納するか|格納する前に常に暗号化|格納する前に常に暗号化|
|マスター暗号化キーをどこに格納するか|Azure Key Vault|Azure Key Vault|
|Azure Key Vault の外部に格納される暗号化キーがあるか|なし|いいえ|
|Azure Key Vault から MEK を取得できるか|いいえ。 一度キー コンテナーに格納されたら、暗号化と復号化に使用することしかできません。|いいえ。 一度キー コンテナーに格納されたら、暗号化と復号化に使用することしかできません。|
|Azure Key Vault と MEK の所有者はだれか|Azure Data Lake Store サービス。|お客様が Azure Key Vault を所有します。これは、お客様自身の Azure サブスクリプションに属します。 キー コンテナー内の MEK はソフトウェアまたはハードウェア (HSM) で管理できます。|
|Azure Data Lake Store サービスによる MEK へのアクセスを取り消すことができるか|いいえ|はい。 Azure Key Vault のアクセス制御リストを管理できるほか、Azure Data Lake Store サービスのサービス ID に対するアクセス制御エントリを削除できます。|
|MEK を完全に削除できるか|いいえ|はい。 お客様が Azure Key Vault から MEK を削除すると、Azure Data Lake Store サービスを含めだれも ADLS アカウント内のデータを復号化できなくなります。 <br><br> Azure Key Vault から削除する前に MEK を明示的にバックアップしておけば、その MEK を復元し、データを回復することができます。 しかし、Azure Key Vault から削除する前に MEK をバックアップしていない場合、後で ADLS アカウント内のデータを復号化することはできなくなります。|


(MEK およびそれが格納されるキー コンテナーの管理者が異なるという) トップレベルの違いを除けば、残りの設計はどちらのモードでも同じです。

マスター暗号化キーのモードの選択に関して、注意すべき重要な点がいくつかあります。

1.    ADLS アカウントをプロビジョニングする際に、顧客管理キーと ADLS 管理キーのどちらを使用するかを選択できます。
2.    一度 ADLS アカウントをプロビジョニングすると、モードを変更することはできません。

### <a name="encryption-and-decryption-of-data"></a>データの暗号化と復号化

Azure Data Lake のデータ暗号化の設計で使用されるキーの種類は 3 つあります。 次の表には、各キーの役割をまとめてあります。

| キー                   | 省略形 | 関連付けられる対象 | 保存先                             | 型       | メモ                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| マスター暗号化キー | MEK          | ADLS アカウント | Azure Key Vault                              | 非対称 | ADLS またはお客様による管理が可能です                                                              |
| データ暗号化キー   | DEK          | ADLS アカウント | 永続的なストレージ – ADLS サービスによる管理 | 対称  | DEK は MEK によって暗号化され、暗号化された DEK はサービスによって永続メディアに格納されます |
| ブロック暗号化キー  | BEK          | データのブロック | なし                                         | 対称  | BEK は DEK から派生したデータ ブロックです                                                      |

次の図にこれらの概念を示します。

![図 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>ファイルが復号化される際の疑似アルゴリズム:
1.    ADLS アカウントの DEK がキャッシュされていて使用できる状態であることを確認します。
    * 準備が整っていない場合、永続的なストレージから暗号化された DEK を読み取り、それを Azure Key Vault に送信して復号化します。 復号化された DEK をメモリにキャッシュすると、使用できる状態になります。
2.    ファイル内のすべてのデータ ブロックに関して次の処理を実行します。
    * 永続的なストレージから暗号化されたデータ ブロックを読み取ります。
    * DEK と暗号化されたデータ ブロックから BEK を生成します。
    * BEK を使用してデータを復号化します。
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>データ ブロックが暗号化される際の疑似アルゴリズム:
1.    ADLS アカウントの DEK がキャッシュされていて使用できる状態であることを確認します。
    * 準備が整っていない場合、永続的なストレージから暗号化された DEK を読み取り、それを Azure Key Vault に送信して復号化します。 復号化された DEK をメモリにキャッシュすると、使用できる状態になります。
2.    DEK からデータ ブロックの一意の BEK を生成します。
3.    AES 256 暗号化を使用して BEK でデータ ブロックを暗号化します。
4.    暗号化されたデータ ブロックを永続的なストレージに格納します。

> [!NOTE] 
> 平文のデータ暗号化キー (DEK) は、パフォーマンス上の理由から短い期間メモリに一時的にキャッシュされ、その期間が過ぎるとすぐに消去されます。 このデータ暗号化キーは常に、マスター暗号化キー (MEK) によって暗号化された状態で永続メディアに格納されます。

## <a name="key-rotation"></a>キーの交換

Azure Data Lake Store では、顧客管理キーを使用している場合にマスター暗号化キー (MEK) を交換できます。 顧客管理キーを使用して ADLS アカウントを設定する方法については、[概要](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)に関するページを参照してください。

### <a name="pre-requisites"></a>前提条件

Azure Data Lake アカウントの設定時に、独自のキーを使用することを選択しておきます。 このオプションをアカウントの作成後に変更することはできません。 暗号化で既定のオプションを使用する場合、データは常に Azure Data Lake によって管理されたキーを使用して暗号化されます。このオプションでは Azure Data Lake によってキーが管理されるため、キーを交換することができません。 次の手順では、顧客管理キーを使用していると仮定しています (キー コンテナーから独自のキーを選択)。

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Azure Data Lake Store でキー (MEK) を交換する方法

1. 新しい [Azure Portal](https://portal.azure.com/) にサインオンします。
2. Azure Data Lake Store アカウントに関連付けられたキーが格納されているキー コンテナーに移動して、キーを選択します。

    ![構成する](./media/data-lake-store-encryption/keyvault.png)

3.    Azure Data Lake Store アカウントに関連付けられたキーを選択して、このキーの新しいバージョンを作成します。
  
   現時点の Azure Data Lake では、新しいバージョンのキーへの交換しかサポートされておらず、異なるキーへの交換はサポートされていません。

   ![新しいバージョン](./media/data-lake-store-encryption/keynewversion.png)

4.    Azure Data Lake ストレージ アカウントに移動して [暗号化] を選択します。

    ![新しいバージョン](./media/data-lake-store-encryption/select-encryption.png)

5.    新しいバージョンのキーが使用できることを示す通知と、この新しいバージョンにキーを交換するボタンが表示されます。 [回転キー] をクリックして、新しいバージョンにキーを更新します。

    ![完了](./media/data-lake-store-encryption/rotatekey.png)

6. この処理は 2 分足らずで完了します。キーの交換が原因でダウンタイムは発生しないものと思われます。 処理が完了したら、新しいバージョンのキーが使用中の状態になります。

