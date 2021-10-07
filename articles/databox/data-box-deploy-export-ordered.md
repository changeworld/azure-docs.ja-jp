---
title: Azure Data Box からデータをエクスポートするチュートリアル | Microsoft Docs
description: デプロイの前提条件と、Azure Data Box からデータをエクスポートする方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: 298d935b9b673e0b77bdd3e66cc3d348e2d52a07
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361530"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>チュートリアル:Azure Data Box のエクスポート注文を作成する

Azure Data Box は、Azure から特定の場所にデータを移動できるハイブリッド ソリューションです。 このチュートリアルでは、Azure Data Box のエクスポート注文を作成する方法について説明します。 エクスポート注文を作成する主な理由は、オンプレミスのストレージが侵害され、バックアップを復元する必要がある場合のディザスター リカバリーのためです。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * エクスポートの前提条件
> * エクスポート用の Data Box の注文
> * エクスポート注文の追跡
> * エクスポート注文のキャンセル

## <a name="prerequisites"></a>前提条件

デバイスを注文する前に、Data Box サービスとデバイスに関する次の構成の前提条件を完了してください。

### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Azure Data Box で使用できる既存のリソース グループがあることを確認します。

* データをエクスポートする Azure Storage アカウントが、[Data Box でサポートされるストレージ アカウント](data-box-system-requirements.md#supported-storage-accounts)で説明されている、サポートされているストレージ アカウントの種類の 1 つであることを確認してください。

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

* データセンター ネットワークに接続されているホスト コンピューターがあること。 Azure Data Box からこのコンピューターにデータをコピーします。 ホスト コンピューターでは、「[Azure Data Box system requirements](data-box-system-requirements.md)」(Azure Data Box のシステム要件) に記載されている、サポートされるオペレーティング システムが実行されている必要があります。

* お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。

## <a name="order-data-box-for-export"></a>エクスポート用の Data Box の注文

デバイスを注文するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用して、次の URL にサインインします。[https://portal.azure.com](https://portal.azure.com)

2. **[+ リソースの作成]** を選択し、*Azure Data Box* を検索します。 **[Azure Data Box]** を選択します。

   ![リソースの作成](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. **［作成］** を選択します

   ![Azure Data Box リソースを作成する](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. ご利用のリージョンで Azure Data Box サービスが利用可能かどうかを確認します。 次の情報を入力または選択し、 **[適用]** を選択します。

    |設定  |値  |
    |---------|---------|
    |転送の種類     | **[Azure にエクスポート]** を選択します。        |
    |サブスクリプション     | Data Box サービス用の EA、CSP、または Azure スポンサー サブスクリプションを選択します。 <br> サブスクリプションは、課金アカウントにリンクされます。       |
    |Resource group     |    既存のリソース グループを選択します。 <br> リソース グループとは、まとめて管理したり、デプロイしたりできるリソースの論理コンテナーです。         |
    |Source Azure region (ソース Azure リージョン)    |    データが現在存在する Azure リージョンを選択します。         |
    |Destination country (宛先の国)     |     デバイスを発送する国を選択します。        |

   ![Data Box の設定の選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. **[Data Box]** を選択します。 1 つの注文での使用可能な最大容量は 80 TB です。 データ サイズがこれより大きい場合は、複数の注文を作成できます。

   ![Data Box の容量の選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. **[Order]\(注文\)** で **[Basic]\(基本\)** の注文情報を指定します。 次の情報を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |サブスクリプション     | サブスクリプションは、前の選択に基づいて自動的に設定されます。|
    |Resource group | 前に選択したリソース グループ。 |
    |Export order name (エクスポート注文名)     |  注文を追跡するためのフレンドリ名を指定します。 <br> 名前の長さは 3 ～ 24 文字で、文字、数字、ハイフンを使うことができます。 <br> 名前の最初と最後は、文字か数字とする必要があります。      |

    ![Export order basics (エクスポート注文の基本)](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    **[Next:Data Selection]\(次へ: データの選択\)** を選択して続行します。

7. **[Data Selection]\(データの選択\)** で、 **[Add storage account and export type]\(ストレージ アカウントとエクスポートの種類を追加\)** を選択します。

    ![ストレージ アカウントとエクスポートの種類を追加](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. **[Select export option]\(エクスポート オプションの選択\)** で、エクスポート オプションの詳細を指定します。 次の情報を入力または選択し、 **[追加]** を選択します。

    |設定  |値  |
    |---------|---------|
    |ストレージ アカウント     | エクスポートするデータがある Azure Storage アカウント。 |
    |Export type (エクスポートの種類)     | エクスポートするデータの種類を **[All objects]\(すべてのオブジェクト\)** および **[Use XML file]\(XML ファイルを使用\)** から指定します。<ul><li> **[All objects]\(すべてのオブジェクト\)** - **[Transfer options]\(転送オプション\)** の選択に応じて、ジョブですべてのデータをエクスポートするように指定します。</li><li> **[Use XML file]\(XML ファイルを使用\)** - ストレージ アカウントからエクスポートする BLOB またはファイルのパスとプレフィックスのセットを含む XML ファイルを指定します。 選択したストレージ アカウントのコンテナーには XML ファイルが含まれている必要があります。ファイル共有から選択することは現在サポートされていません。 ファイルは、空でない .xml ファイルである必要があります。</li></ul>        |
    |Transfer options (転送オプション)     |  **[Select all]\(すべて選択\)** 、 **[All BLOB]\(すべての BLOB\)** 、および **[All files]\(すべてのファイル\)** からデータ転送オプションを指定します。 <ul><li> **[Select All]\(すべて選択\)** - すべての BLOB と Azure Files をエクスポートすることを指定します。 BLOB のみがサポートされているストレージ アカウント (Blob ストレージ アカウント) を使用している場合は、 **[All Files]\(すべてのファイル\)** オプションは選択できません。</li><li> **[All BLOB]\(すべての BLOB\)** - ブロック BLOB とページ BLOB のみエクスポートすることを指定します。</li><li> **[All Files]\(すべてのファイル\)** - BLOB を除くすべてのファイルをエクスポートすることを指定します。 使用しているストレージ アカウントの種類 (GPv1 と GPv2、Premium Storage、または BLOB ストレージ) によって、エクスポートできるデータの種類が決まります。 詳細については、[エクスポートでサポートされるストレージ アカウント](../import-export/storage-import-export-requirements.md#supported-storage-types)に関する記事を参照してください。</li></ul>         |
    |Include verbose log (詳細ログを含める)     | 正常にエクスポートされたすべてのファイルの一覧を含む詳細ログ ファイルが必要かどうかを示します。 エクスポート注文のコピー ログと詳細ログの詳細については、[ログの参照](data-box-export-logs.md#view-logs-during-data-copy)に関するセクションを参照してください。 |

    > [!NOTE]
    > **[Export type]\(エクスポートの種類\)** 設定で **[Use XML file]\(XML ファイルを使用\)** を選択した場合は、XML ファイルに有効なパスまたはプレフィックスが含まれていることを確認する必要があります。 XML ファイルを構築して指定する必要があります。 ファイルが無効な場合、または指定されたパスと一致するデータがない場合は、一部のデータまたはすべてのデータがエクスポートされずに終了します。 ガイダンスについては、「[XML ファイルを作成する](#create-xml-file)」を参照してください。

    XML ファイルをコンテナーに追加する方法については、「[XML ファイルを使用した注文のエクスポート](data-box-deploy-export-ordered.md#export-order-using-xml-file)」を参照してください。

   ![Select export option (エクスポート オプションの選択)](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   XML 入力の例については、「[XML ファイルを作成する](#create-xml-file)」を参照してください。

9. **[Data Selection]\(データの選択\)** で、設定を確認し、 **[Next:Security>]\(次へ: セキュリティ>\)** を選択して続行します。

   ![エクスポート注文、データ選択](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    **[セキュリティ]** 画面では、自分の暗号化キーを使用でき、二重暗号化の使用を選択できます。

    **[セキュリティ]** 画面の設定はすべてオプションです。 設定を変更しない場合は、既定の設定が適用されます。

    ![Data Box インポート注文ウィザードの [セキュリティ] 画面](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. 独自のカスタマー マネージド キーを使用して新しいリソースのロック解除パスキーを保護する場合は、 **[暗号化の種類]** を展開します。

    Azure Data Box のカスタマー マネージド キーの構成は省略可能です。 Data Box では、既定で Microsoft マネージド キーを使用してロック解除パスキーが保護されます。

    カスタマー マネージド キーは、デバイス上のデータの暗号化方法には影響しません。 キーは、デバイスのロック解除パスキーを暗号化するためにのみ使用されます。

    カスタマー マネージド キーを使用しない場合は、手順 16 に進んでください。

    ![[暗号化の種類] 設定を示すセキュリティ画面](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. キーの種類として **[カスタマー マネージド キー]** を選択します。 次に、 **[キー コンテナーとキーを選択します]** を選択します。
   
    ![セキュリティ画面。カスタマー マネージド キーの設定](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. **[Azure Key Vault からのキーの選択]** 画面で、サブスクリプションが自動的に設定されます。

    - **[キー コンテナー]** で、ドロップダウン リストから既存のキー コンテナーを選択できます。

      ![[Select key from Azure Key Vault]\(Azure Key Vault からのキーの選択\) 画面](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - **[新規作成]** を選択して、新しいキー コンテナーを作成することもできます。 **[キー コンテナーの作成]** 画面で、リソース グループとキー コンテナーの名前を入力します。 **[論理的な削除]** と **[消去保護]** が有効になっていることを確認します。 他のすべての既定値をそのまま使用し、 **[確認および作成]** を選択します。

      ![新しい Azure キー コンテナーの作成設定](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      キー コンテナーの情報を確認し、 **[作成]** を選択します。 キー コンテナーの作成が完了するまで数分待ちます。

      ![新しい Azure キー コンテナーの確認画面](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. **[Azure Key Vault からのキーの選択]** 画面では、キー コンテナー内の既存のキーを選択できます。

    ![Azure Key Vault から既存のキーを選択する](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    新しいキーを作成する場合は、 **[新規作成]** を選択します。 RSA キーを使用する必要があります。 サイズは 2,048 以上にすることができます。 新しいキーの名前を入力し、他の既定値をそのまま使用して、 **[作成]** を選択します。

      ![新しいキーの作成オプション](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      キーがキー コンテナーに作成されると通知されます。

14. 使用するキーの **バージョン** を選択し、 **[選択]** を選択します。

      ![キー コンテナーで作成された新しいキー](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    新しいキー バージョンを作成する場合は、 **[新規作成]** を選択します。

    ![新しいキー バージョンを作成するためのダイアログ ボックスを開く](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    **[Create new key]\(新しいキーの作成\)** 画面で、新しいキー バージョンの設定を選択し、 **[作成]** を選択します。

    ![新しいキー バージョンを作成する](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    **[セキュリティ]** 画面の **[暗号化の種類]** 設定に、キー コンテナーとキーが表示されます。

    ![カスタマー マネージド キーのキーとキー コンテナー](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. このリソースへのアクセスを管理するために使用するユーザー ID を選択します。 **[Select a user identity]\(ユーザー ID を選択する\)** を選択します。 右側のパネルで、使用するサブスクリプションとマネージド ID を選択します。 次に **[選択]** を選択します。

    ユーザー割り当てマネージド ID は、複数のリソースを管理するために使用できるスタンドアロンの Azure リソースです。 詳細については、[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。  

    新しいマネージド ID を作成する必要がある場合は、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」のガイダンスに従ってください。
    
    ![[Select a user identity]\(ユーザー ID を選択する\)](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    ユーザー ID が **[暗号化の種類]** の設定に表示されます。

    **[暗号化の種類]** の設定は折りたたんでかまいません。

    ![選択したユーザー ID が [暗号化の種類] の設定に表示される](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. ソフトウェアベースの二重暗号化を有効にしたい場合は、 **[Double encryption (for high-security environments)]\(二重暗号化 (高度にセキュリティ保護された環境用)\)** を展開し、 **[Enable double encryption for the order]\(この注文に関して二重暗号化を有効にする\)** を選択します。 

    Data Box 上のデータに対し、AES-256 ビットの暗号化に加えて、ソフトウェアベースの暗号化が実行されます。

    > [!NOTE]
    > このオプションを有効にすると、注文の処理とデータのコピーにかかる時間が長くなることがあります。 注文後に、このオプションを変更することはできません。

    ![Data Box インポートの [セキュリティ] 画面 (二重暗号化)](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    **[Next:連絡先の詳細]** を選択して続行します。

11. **[Contact Details]\(連絡先の詳細\)** で、 **[+ Add Shipping Address]\(+ 配送先住所の追加\)** を選択して発送情報を入力します。

    ![Add shipping address (配送先住所の追加)](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. **[Add Shipping Address]\(配送先住所の追加\)** に、お客様の氏名、会社の名前と郵送先住所、および有効な電話番号を入力します。 **[検証]** を選択します。 配送先住所でサービスが利用可能かどうかが確認されます。 指定した配送先住所でサービスを利用できる場合は、その旨が通知されます。

    ![配送先住所の検証 (Validate shipping address)](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    自己管理型の発送が利用可能なリージョンで注文する場合は、このオプションを選択できます。 自己管理の出荷の詳細については、[自己管理の出荷の使用](data-box-portal-customer-managed-shipping.md)に関するページをご覧ください。

13. 配送の詳細が正常に検証されたら、 **[Add shipping address]\(配送先住所の追加\)** を選択します。

14. **[Contact Details]\(連絡先の詳細\)** で、配送先住所とメール アドレスを確認します。 指定した電子メール アドレスに、注文の状態の更新に関する電子メール通知が送信されます。

    グループの管理者が辞めた場合も引き続き通知を受け取ることができるように、グループ メールを使用することをお勧めします。

    ![連絡先の詳細](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. **[Next:Review + Order>]\(次へ: 確認と注文\)** を選択します。 注文の作成を続行するには、使用条件に同意する必要があります。

16. **[注文]** を選択します。 注文が作成されるまで数分かかります。

    ![注文の確定](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>XML ファイルを使用した注文のエクスポート

**[Use XML file]\(XML ファイルを使用\)** を選択した場合は、エクスポートする特定のコンテナーと BLOB (ページおよびブロック) を指定できます。 次の手順は、データのエクスポートに XML ファイルを使用する方法を示しています。 XML ファイルを構築するには、「[XML ファイルを作成する](#create-xml-file)」のガイダンスに従います。

XML ファイルを使用してデータをエクスポートするには:

1. **[Export type]\(エクスポートの種類\)** には、 **[Use XML file]\(XML ファイルを使用\)** を選択します。 これは、エクスポートする特定の BLOB と Azure ファイルを指定する XML ファイルです。 XML ファイルを追加するには、 **[Click here to select an XML file]\(ここをクリックして XML ファイルを選択\)** を選択します。

     ![エクスポート オプションの選択、XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. **[+ Container]\(+ コンテナー\)** を選択して、コンテナーを作成します。

    ![エクスポート オプションの選択、コンテナー](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Azure portal の右側から表示される **[New Container]\(新しいコンテナー\)** タブで、コンテナーの名前を追加します。 名前は小文字にする必要があり、数字とダッシュ '-' を含めることができます。 次に、ドロップダウン リスト ボックスから **[Public access level]\(パブリック アクセス レベル\)** を選択します。 他のユーザーがデータにアクセスできないようにするため、 **[Private (non anonymous access)]\(プライベート (非匿名アクセス))\** を選択することをお勧めします。 コンテナーのアクセス レベルの詳細については、[コンテナーのアクセス許可](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)に関する記事を参照してください。

   ![エクスポート オプションの選択、新しいコンテナー設定](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. **［作成］** を選択します

   ![エクスポート オプションの選択、新しいコンテナーの作成。](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   コンテナーが正常に作成されると、次のメッセージが表示されます。

   ![コンテナーは正常に作成されました](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. 作成したコンテナーを選択し、ダブルクリックします。

   ![コンテナーの詳細を表示する](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. コンテナーをダブルクリックすると、コンテナーのプロパティ ビューが表示されます。 ここで、エクスポートする BLOB および、または Azure Files の一覧が含まれる XML ファイルを添付 (あるいは参照) します。 **[アップロード]** を選択します。

   ![コンテナーに BLOB をアップロードする](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. これで、XML ファイルがコンテナーに追加されました。 この XML で指定した BLOB と Azure Files のみがエクスポートされます。

   ![コンテナーに追加された XML ファイル](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="create-xml-file"></a>XML ファイルを作成する

エクスポートする BLOB とファイルを XML ファイルを使用して選択する場合は、次のガイドラインに従って XML ファイルを作成します。
- **「サンプル XML ファイル」タブ:** 各タグの例を含むサンプル XML ファイルをコピーします。
- **「XML ファイルの概要」タブ:** XML ファイルのタグ要件を確認します。
- **「プレフィックスの例」タブ:** エクスポートする複数の BLOB とファイルを選択する有効なプレフィックスの例を参照します。

### <a name="sample-xml-file"></a>[サンプル XML ファイル](#tab/sample-xml-file)

このサンプル XML ファイルには、Data Box のエクスポート注文でエクスポートする BLOB とファイルの選択に使用される各 XML タグの例が含まれています。 

- XML ファイルの要件については、「**XML ファイルの概要**」タブを参照してください。
- 有効な BLOB プレフィックスとファイル プレフィックスのその他の例については、「**プレフィックスの例**」タブを参照してください。

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!--BlobList selects individual blobs (BlobPath) and multiple blobs (BlobPathPrefix) in Blob storage for export.-->
   <BlobList>
      <BlobPath>/container1/blob.txt</BlobPath> <!-- Exports /container1/blob.txt -->
      <BlobPathPrefix>/container2/</BlobPathPrefix> <!--Exports all blobs in container2 -->
      <BlobPathPrefix>/container</BlobPathPrefix>  <!-- Exports all containers beginning with prefix: "container" -->
      <BlobPathPrefix>/container1/2021Q2</BlobPathPrefix> <!-- Exports all blobs in container1 with prefix: "2021Q2" -->
   </BlobList>
   
   <!--AzureFileList selects individual files (FilePath) and multiple files (FilePathPrefix) in Azure File storage for export.-->
   <AzureFileList>
      <FilePath>/fileshare1/file.txt</FilePath> <!-- Exports /fileshare1/file.txt -->
      <FilePathPrefix>/fileshare1/</FilePath> <!-- Exports all directories and files in fileshare1 -->
      <FilePathPrefix>/fileshare</FilePathPrefix> <!-- Exports all directories and files in any fileshare with prefix: "fileshare" -->
      <FilePathPrefix>/fileshare2/contosowest</FilePathPrefix> <!-- Exports all directories and files in fileshare2 with prefix: "contosowest" -->
   </AzureFileList>
```

### <a name="xml-file-overview"></a>[XML ファイルの概要](#tab/xml-file-overview)

エクスポート注文のための XML ファイルを構築する場合は、次のガイドラインに従います。 タグ形式が正しくないと、エクスポート エラーが発生するおそれがあります。

エクスポート注文を行う際に XML ファイルをアップロードする手順については、「[XML ファイルを使用した注文のエクスポート](#export-order-using-xml-file)」を参照してください。

#### <a name="path-vs-prefix"></a>パスとプレフィックス

XML ファイルで XML タグを正しく作成するには、パスとプレフィックスの違いを理解する必要があります。

* "*パス*" は、1 つの BLOB またはファイルを選択して、フィルター処理します。
* "*プレフィックス*" は、複数の BLOB または複数のファイルを選択して、フィルター処理します。

正しい形式のプレフィックスの例については、「**プレフィックスの例**」タブを参照してください。

#### <a name="tag-usage"></a>タグの使用法

次の XML タグは、Data Box のエクスポート注文用 XML ファイルで使用されます。

| XML タグ           |説明 |
|-------------------|------------|
|`<BlobList>`       |&lt;BlobPath&gt; および &lt;BlobPathPrefix&gt; タグの親タグ。|
|`<BlobPath>`       |1 つの BLOB を選択します。 |
|`<BlobPathPrefix>` |共通のプレフィックスを持つ複数の BLOB を選択します。 例については、「**プレフィックスの例**」タブを参照してください。|
|`<AzureFileList>`  |&lt;FilePath&gt; および &lt;FilePathPrefix&gt; タグの親タグ。|
|`<FilePath>`       |1 つのファイルを選択します。 |
|`<FilePathPrefix>` |共通のプレフィックスを持つ複数のファイルを選択します。 例については、「**プレフィックスの例**」タブを参照してください。|

コンテキストの中のタグを確認するには、「**サンプル XML ファイル**」タブを参照してください。

#### <a name="xml-tag-requirements"></a>XML タグの要件

* XML タグでは常に大文字と小文字が区別されるため、上の表のタグと正確に一致する必要があります。
* 開始タグと終了タグは一致している必要があります。
* XML タグまたは書式指定が正しくないと、データのエクスポートに失敗するおそれがあります。
* BLOB のプレフィックスやファイルのプレフィックスが正しくないと、データはエクスポートされません。 有効なプレフィックスの例については、「**プレフィックスの例**」タブを参照してください。

### <a name="prefix-examples"></a>[プレフィックスの例](#tab/prefix-examples)

次のサンプル パスは、エクスポートする複数の BLOB またはファイルを選択するプレフィックスを構築するさまざまな方法を示しています。

#### <a name="valid-blob-path-prefixes"></a>有効な BLOB パス プレフィックス

以下のサンプル パスは、Azure BLOB ストレージ内の複数の BLOB をエクスポート対象として選択するために、&lt;BlobPathPrefix&gt; タグと共に使用します。

|BLOB パス プレフィックス        |説明                                                                     |タグの例                         |
|------------------------|--------------------------------------------------------------------------------|------------------------------------|
|/                       |ストレージ アカウント内のすべての BLOB をエクスポートします。                                       |`<BlobPathPrefix>/</BlobPathPrefix>`|
|/$root/                 |ルート コンテナー内のすべての BLOB をエクスポートします。                                        |`<BlobPathPrefix>/$root/</BlobPathPrefix>`|
|/container2/            |コンテナー **container2** 内のすべての BLOB をエクスポートします。                              |`<BlobPathPrefix>/container2/</BlobPathPrefix>`|
|/container          |プレフィックス **container** で始まる任意のコンテナー内にあるすべての BLOB をエクスポートします。      |`<BlobPathPrefix>/container</BlobPathPrefix>`|
|/container1/2021Q2      |コンテナー **container1** 内にあり、プレフィックス **2021Q2** で始まるすべての BLOB をエクスポートします。|`<BlobPathPrefix>/container1/2021Q2</BlobPathPrefix>`|

エクスポートする "*1 つの*" BLOB を選択するには、&lt;BlobPath&gt; タグを使用して、コンテナー パスと BLOB 名を指定します。 たとえば、**container1** コンテナー内の **blob.txt** を選択するには、次のタグを使用します: `<BlobPath>/container1/blob.txt</BlobPath>`。

#### <a name="valid-file-path-prefixes"></a>有効なファイル パス プレフィックス

以下のサンプル パスは、エクスポートする複数の Azure ファイルを選択するために、&lt;FilePathPrefix&gt; タグと共に使用します。

|ファイル パス プレフィックス        |説明                                                                                          |タグの例|
|------------------------|-----------------------------------------------------------------------------------------------------|-----------|
|/                       |ストレージ アカウント内のすべてのファイルとディレクトリをエクスポートします。 |`<FilePathPrefix>/</FilePath>Prefix>`|
|/fileshare1/            |**fileshare1** という名前の共有内にあるすべてのファイルとディレクトリをエクスポートします。                                                 |`<FilePathPrefix>/fileshare1/</FilePath>Prefix>`|
|/fileshare              |プレフィックス **fileshare** で始まる任意のファイル共有内にあるすべてのファイルとディレクトリをエクスポートします。 |`<FilePathPrefix>/fileshare</FilePath>Prefix>`|
|/fileshare2/contosowest |ファイル共有 **fileshare2** 内にあり、プレフィックス **contosowest** で始まるすべてのファイルとディレクトリをエクスポートします。|`<FilePathPrefix>/fileshare2/contosowest</FilePath>Prefix>`|

エクスポートする "*1 つの*" ファイルを選択するには、&lt;FilePath&gt; タグを使用して、共有パスとファイル名を指定します。 たとえば、**fileshare1** 内の **file.txt** を選択するには、次のタグを使用します: `<FilePath>/fileshare1/file.txt</FilePath>`

---

## <a name="track-the-order"></a>注文を追跡する

注文後、Azure portal から注文の状態を追跡できます。 Data Box の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルでは、注文は、 **[注文済み]** 状態で表示されます。

デバイスの準備が完了すると、選択したストレージ アカウントからデータのコピーが開始されます。 ポータルには、 **[Data copy in progress]\(データのコピーが進行中\)** 状態の注文が表示されます。

![Data Box エクスポート注文、データ コピーが進行中](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box によってソース ストレージ アカウントからデータがコピーされます。 データのコピーが完了すると Data Box がロックされ、ポータルに **[Copy Completed]\(コピー完了\)** 状態の注文が表示されます。

![Data Box エクスポート注文、データ コピー完了](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Azure Storage から Data Box へのデータのエクスポートが失敗する場合があります。 アーカイブされた BLOB のエクスポートはサポートされていないため、BLOB がアーカイブ BLOB でないことを確認します。 

> [!NOTE]
> アーカイブ BLOB の場合には、それらの BLOB を Azure Storage アカウントから Data Box にエクスポートする前にリハイドレートする必要があります。 詳細については、[アーカイブ BLOB のリハイドレート]( ../storage/blobs/storage-blob-rehydration.md)に関するページを参照してください。

デバイスが使用できない場合は、通知されます。 デバイスが使用可能な場合は、Microsoft が配送用のデバイスを特定し、配送の準備をします。 デバイスの準備中に、次のアクションが実行されます。

* デバイスに関連付けられているストレージ アカウントごとに SMB 共有が作成されます。
* 各共有で、ユーザー名やパスワードなどのアクセス資格情報が生成されます。
* デバイスはロックされ、デバイスのロック解除パスワードを使用してのみアクセスできます。 パスワードを取得するには、Azure portal アカウントにログインし、 **[デバイスの詳細]** を選択する必要があります。

次に、Microsoft は、デバイスを準備して地域の運送業者を通じて発送します。 デバイスが出荷されると、お客様に追跡番号が送信されます。 ポータルには、**出荷済み** 状態の注文が表示されます。

![出荷済みの Data Box のエクスポート注文](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

自己管理型の発送を選択した場合、データセンターからデバイスを受け取る準備が整うと、次の手順が記載されたメール通知が送信されます。 自己管理型の発送の詳細については、[自己管理型の発送](data-box-portal-customer-managed-shipping.md)に関するページを参照してください。

![引き取り準備が完了した自己管理型の発送](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>注文をキャンセルする

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** を選択します。

注文は、注文した後、注文処理が開始されるまでの任意の時点でキャンセルできます。

キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * エクスポートの前提条件
> * エクスポート用の Data Box の注文
> * エクスポート注文の追跡
> * エクスポート注文のキャンセル

次のチュートリアルに進んで、Data Box を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を設定する](./data-box-deploy-set-up.md)
