---
title: Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする | Microsoft Docs
description: Resource Manager テンプレートを使用して Azure Blob Storage から Data Lake Storage にアップグレードする方法を説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: normesta
ms.openlocfilehash: 025aa395fa6d2fd3a8fe98f4781a6b554e2b506d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710534"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 の機能で Azure Blob Storage をアップグレードする

この記事は、ファイルやディレクトリ レベルのセキュリティや、より高速な操作などの機能を利用できるようにするのに役立ちます。 これらの機能は、ビッグ データ分析ワークロードで広く使用されており、まとめて Azure Data Lake Storage Gen2 と呼ばれます。 

これらの機能の詳細を確認し、ワークロード、アプリケーション、コスト、サービス統合、ツール、機能、ドキュメントに対するこのアップグレードの影響を評価するには、「[Azure Data Lake Storage Gen2 の機能での Azure Blob Storage のアップグレード](upgrade-to-data-lake-storage-gen2.md)」を参照してください。

> [!IMPORTANT]
> アップグレードは一方向です。 アップグレードを実行した後で、アカウントを元に戻す方法はありません。 非運用環境でアップグレードを検証することをお勧めします。

## <a name="perform-the-upgrade"></a>アップグレードを実行する

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[Data Lake Gen2 の移行]** を選択します。

   **[Azure Data Lake Gen2 の機能を持つストレージ アカウントにアップグレードする]** 構成ページが表示されます。

   > [!div class="mx-imgBorder"]
   > ![[構成] ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. **[手順 1: アップグレードする前にアカウントの変更を確認する]** セクションを展開し、 **[変更を確認して同意する]** をクリックします。

5. **[Review account changes]\(アカウントの変更の確認\)** ページで、チェック ボックスをオンにして、 **[Agree to changes]\(変更に同意する\)** をクリックします。

6. **[手順 2: アップグレードする前にアカウントを検証する]** セクションを展開し、 **[検証の開始]** をクリックします。

   検証に失敗した場合は、ページにエラーが表示されます。 場合によっては、 **[エラーの表示]** リンクが表示されます。 リンクが表示されたら、それを選択します。 

   > [!div class="mx-imgBorder"]
   > ![[エラーの表示] リンク](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   次に、**error.json** ファイルのコンテキスト メニューから、 **[ダウンロード]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![エラー json ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   ダウンロードしたファイルを開き、アカウントが検証ステップに合格しなかった理由を確認します。 

   次の JSON は、アカウントで互換性のない機能が有効になっていることを示しています。 この場合は、その機能を無効にしてから、検証プロセスを再度開始します。

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. アカウントが正常に検証された後、 **[手順 3: アカウントのアップグレード]** セクションを展開して、 **[アップグレードの開始]** をクリックします。

   > [!IMPORTANT]
   > アカウントがアップグレードされている間、書き込み操作は無効になります。 読み取り操作は無効になりませんが、アップグレード プロセスが不安定になる可能性があるため、読み取り操作を中断することを強く推奨します。

   移行が正常に完了すると、次のようなメッセージが表示されます。 

   > [!div class="mx-imgBorder"]
   > ![移行完了ページ](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

## <a name="migrate-data-workloads-and-applications"></a>データ、ワークロード、アプリケーションを移行する 

1. **Blob service** エンドポイントまたは **Data Lake Storage** エンドポイントを指し示すように、[ワークロード内のサービス](data-lake-storage-integrate-with-azure-services.md)を構成します。

   > [!div class="mx-imgBorder"]
   > ![アカウントのエンドポイント](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Windows Azure Storage Blob ドライバーまたは [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) ドライバーを使用する Hadoop ワークロードの場合は、[Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) ドライバーを使用するようにそれらを変更します。 **Blob service** エンドポイントに対して要求を行う WASB ドライバーとは異なり、ABFS ドライバーはアカウントの **Data Lake Storage** エンドポイントに対して要求を行います。

2. カスタム アプリケーションをテストして、アップグレードされたアカウントで想定どおりに動作することを確認します。 

   [Data Lake Storage でのマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)を使用すると、ほとんどのアプリケーションで BLOB API を変更せずに引き続き使用できます。 問題が発生する場合、または API を使用してディレクトリ操作と ACL を使用したい場合は、Data Lake Storage Gen2 API を使用するようにコードの一部を移動することを検討します。 [.NET](data-lake-storage-directory-file-acl-dotnet.md)、[Java](data-lake-storage-directory-file-acl-java.md)、[Python](data-lake-storage-directory-file-acl-python.md)、[Node.js](data-lake-storage-acl-javascript.md)、[REST](/rest/api/storageservices/data-lake-storage-gen2) に関するガイドを参照してください。 

3. カスタム スクリプトをテストして、アップグレードされたアカウントで想定どおりに動作することを確認します。 

   BLOB API の場合と同様に、スクリプトの多くは、変更を必要とせずに機能する可能性があります。 ただし、必要な場合は、Data Lake Storage Gen2 の [PowerShell コマンドレット](data-lake-storage-directory-file-acl-powershell.md)と [Azure CLI コマンド](data-lake-storage-directory-file-acl-cli.md)を使用するようにスクリプト ファイルをアップグレードできます。
 

## <a name="see-also"></a>こちらもご覧ください

[Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)