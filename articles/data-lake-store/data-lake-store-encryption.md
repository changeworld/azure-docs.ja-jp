---
title: "Azure Data Lake Store の暗号化 |Microsoft ドキュメント"
description: "Azure Data Lake Store 内の暗号化とキーのローテーションのしくみを理解します。"
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
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store 内のデータの暗号化

Azure Data Lake Store の暗号化では、データを保護する、企業のセキュリティ ポリシーを実装および規制のコンプライアンス要件を満たすのに役立ちます。 この記事では、設計の概要について説明し、いくつかの実装の技術的な側面について説明します。

Data Lake Store は、残りの部分と転送中に両方のデータの暗号化をサポートします。 Data Lake Store をサポートしている、静止したデータの"の既定では、"透過的な暗号化します。 もう少し詳しくでこれらの用語の意味を次に示します。

* **既定では**: 新しい Data Lake Store アカウントを作成するときに既定の設定が暗号化を有効します。 その後、Data Lake Store に格納されているデータは永続的なメディアに保存する前に常に暗号化されます。 これは、すべてのデータの動作であり、アカウントの作成後に変更できません。
* **透過的な**: Data Lake Store は自動的に、永続化する前にデータを暗号化し、取得する前にデータを復号化します。 暗号化が構成され、管理者が Data Lake Store レベルで管理されています。 データ アクセス Api は変更されません。 したがって、暗号化のための Data Lake Store と対話するアプリケーションとサービスでの変更は必要ありません。

転送 (中のデータとも呼ばれます) のデータが Data Lake Store にも常に暗号化されます。 永続的なメディアに保存する前にデータを暗号化に加えて、データも常にセキュリティで保護転送中に HTTPS を使用しています。 HTTPS は、データ Lake Store REST インターフェイスがサポートされている唯一のプロトコルです。 次の図は、データが Data Lake Store で暗号化する方法を示しています。

![Data Lake Store のデータの暗号化のダイアグラム](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Data Lake Store と暗号化を設定します。

Data Lake Store の暗号化がアカウントの作成中にセットアップしは、既定では常に有効にします。 自分でキーを管理するか (これは、既定値) を管理する Data Lake Store を許可することができます。

詳細については、次を参照してください。[作業の開始](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)です。

## <a name="how-encryption-works-in-data-lake-store"></a>Data Lake Store で暗号化がどのように動作するか

次の情報は、マスター暗号化キーを管理する方法を説明し、3 つの Data Lake Store のデータの暗号化で使用できるキーの種類について説明します。

### <a name="master-encryption-keys"></a>マスター暗号化キー

Data Lake Store は、マスター暗号化キー (MEKs) の管理の 2 つのモードを提供します。 ここでは、マスター暗号化キーが最上位のキーであると仮定します。 Data Lake Store に格納されているデータの暗号化を解除するには、マスター暗号化キーへのアクセスが必要です。

マスター暗号化キーを管理するための 2 つのモードは次のとおりです。

*   キーの管理されたサービス
*   お客様が管理キー

両方のモードでは、マスター暗号化キーは、Azure Key Vault に格納することによってセキュリティ保護されます。 Key Vault は、暗号化キーを保護するために使用できる Azure で完全に管理された、安全性の高いサービスです。 詳細については、次を参照してください。 [Key Vault](https://azure.microsoft.com/services/key-vault)です。

次に、MEKs を管理するための 2 つのモードで提供される機能の簡単な比較を示します。

|  | キーの管理されたサービス | お客様が管理キー |
| --- | --- | --- |
|データの格納方法|常に格納される前に暗号化されます。|常に格納される前に暗号化されます。|
|マスター_キーの暗号化キーの格納場所|Key Vault|Key Vault|
|暗号化キーが Key Vault の外部でクリア テキストで格納されているか。 |いいえ|いいえ|
|Key Vault によって、MEK を取得できますか。|いいえ。 MEK が Key Vault に格納されている後に、暗号化と復号化にのみ使用できます。|いいえ。 MEK が Key Vault に格納されている後に、暗号化と復号化にのみ使用できます。|
|Key Vault インスタンスと、MEK 所有者は誰ですか。|Data Lake Store サービス|Azure サブスクリプションに属している、Key Vault インスタンスを所有しています。 ソフトウェアまたはハードウェアによっては、Key Vault に MEK を管理できます。|
|Data Lake Store サービスの MEK へのアクセスを取り消すことができますか。|いいえ|はい。 Key Vault にアクセス制御リストを管理し、Data Lake Store サービスのサービス id にアクセス制御エントリを削除できます。|
|永続的に、MEK を削除できますか。|いいえ|はい。 Key Vault からの MEK を削除する場合は、Data Lake Store サービスを含め、すべてのユーザーが Data Lake Store アカウント内のデータを解読できません。 <br><br> Key Vault から削除する前に MEK を明示的にバックアップしておく場合、MEK を復元することができますと、データを回復し、ことができます。 ただし、キー資格情報コンテナーから削除する前に MEK がバックアップされない場合、Data Lake Store アカウント内のデータは決して解読できません以降。|


別に、MEK と格納されている Key Vault インスタンスを管理する人の違い、設計の残りの部分は、同じ両方のモードです。

これは、マスター暗号化キーのモードを選択すると、次を注意してください。

*   顧客を使用する管理されているかキーまたはキーの管理されたサービス、Data Lake Store アカウントをプロビジョニングする際に選択できます。
*   Data Lake Store アカウントがプロビジョニングされた後、モードを変更できません。

### <a name="encryption-and-decryption-of-data"></a>データの暗号化と解読

データの暗号化の設計に使用されるキーの 3 つの種類があります。 次の表は、概要を示します。

| キー                   | 省略形 | 関連付けられています。 | 記憶域の場所                             | 型       | 注                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| マスター_キーの暗号化 | MEK          | Data Lake Store アカウント | Key Vault                              | 非対称 | これは、は、Data Lake Store またはで管理することができます。                                                              |
| データ暗号化キー   | DEK          | Data Lake Store アカウント | Data Lake Store サービスによって管理されている、永続的なストレージ | 対称  | DEK は、MEK して暗号化されます。 暗号化された DEK は、永続的なメディアに格納されているものです。 |
| ブロックの暗号化キー  | BEK          | データのブロック | None                                         | 対称  | BEK は DEK およびデータ ブロックから派生します。                                                      |

次の図は、これらの概念を示しています。

![データの暗号化キー](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>ファイルを復号化するときの擬似アルゴリズム:
1.  かどうか、Data Lake Store アカウントの DEK はキャッシュされ、使用可能な状態を確認してください。
    - 以外の場合は、永続的な記憶域から暗号化された DEK を読み取るし、復号化する Key Vault に送信します。、 メモリ内の復号化された、DEK をキャッシュします。 使用する準備ができました。
2.  ファイル内のデータのブロックがすべて。
    - 永続的なストレージからデータの暗号化されたブロックを読み取り。
    - DEK とデータの暗号化されたブロックから、BEK を生成します。
    - データの暗号化を解除するのに、BEK を使用します。


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>擬似アルゴリズムとデータのブロックは、暗号化することです。
1.  かどうか、Data Lake Store アカウントの DEK はキャッシュされ、使用可能な状態を確認してください。
    - 以外の場合は、永続的な記憶域から暗号化された DEK を読み取るし、復号化する Key Vault に送信します。、 メモリ内の復号化された、DEK をキャッシュします。 使用する準備ができました。
2.  DEK からデータのブロックの一意の BEK を生成します。
3.  AES 256 暗号化を使用して、BEK でデータ ブロックを暗号化します。
4.  永続的ストレージにデータの暗号化されたデータ ブロックを格納します。

> [!NOTE] 
> パフォーマンス上の理由から、クリア テキストで DEK は、短い時間、メモリ内キャッシュされがすぐに消去された後で。 永続的なメディアで、MEK によって暗号化された常に格納されます。

## <a name="key-rotation"></a>キー ローテーション

お客様が管理するキーを使用しているときに、MEK を回転することができます。 顧客管理キーを持つ Data Lake Store アカウントを設定する方法については、次を参照してください。[作業の開始](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)です。

### <a name="prerequisites"></a>前提条件

Data Lake Store アカウントをセットアップするときに、独自のキーを使用しています。 アカウントが作成された後、このオプションを変更できません。 次の手順では、お客様が管理するキーを使用している前提としています (つまり、選択した独自のキーから Key Vault)。

暗号化のため、既定のオプションを使用する場合、データが常に暗号化すること Data Lake Store によって管理されているキーを使用して、注意してください。 このオプションで Data Lake Store で管理されているように、キーを回転できることがありません。

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Data Lake Store に MEK を回転させる方法

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. Data Lake Store アカウントに関連付けられた、キーを格納する Key Vault インスタンスを参照します。 選択**キー**です。

    ![Key Vault のスクリーン ショット](./media/data-lake-store-encryption/keyvault.png)

3.  Data Lake Store アカウントに関連付けられたキーを選択し、このキーの新しいバージョンを作成します。 Data Lake Store 現在のみをサポートするキーの新しいバージョンへのキーのローテーションに注意してください。 別のキーを回転をサポートしていません。

   ![新しいバージョンが強調表示されていると、キーのスクリーン ショット ウィンドウ](./media/data-lake-store-encryption/keynewversion.png)

4.  Data Lake Store のストレージ アカウントへの参照し、選択**暗号化**です。

    ![Data Lake Store のスクリーン ショットのストレージ アカウント ウィンドウで強調表示されている暗号化](./media/data-lake-store-encryption/select-encryption.png)

5.  メッセージは、キーの新しいキーのバージョンが使用できることを通知します。 をクリックして**キーの回転**キーを新しいバージョンに更新します。

    ![メッセージと強調表示の回転のキーを持つウィンドウを Data Lake Store のスクリーン ショット](./media/data-lake-store-encryption/rotatekey.png)

この操作は、2 分未満を実行する必要があります、キー ローテーションのための予想されるダウン時間はありません。 操作が完了したら、新しいバージョンのキーは使用中です。
