---
title: アカウントのアクセス キーを管理する
titleSuffix: Azure Storage
description: ストレージ アカウントのアクセスキーを表示、管理、およびローテーションする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75975782"
---
# <a name="manage-storage-account-access-keys"></a>ストレージ アカウント アクセス キーを管理する

ストレージ アカウントを作成すると、Azure により 512 ビットのストレージ アカウント アクセス キーが 2 つ生成されます。 これらのキーは、共有キー認証を使用してストレージ アカウント内のデータへのアクセスする際、認証に使用されます。

アクセスキーの管理には Azure Key Vault を使用し、キーのローテーションと再生成は定期的に行うことをお勧めします。 Azure Key Vault を使用すると、アプリケーションを中断することなく簡単にキーをローテーションできます。 また、キーを手動でローテーションすることもできます。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>アクセス キーと接続文字列を表示する

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault を使用してアクセスキーを管理する

Microsoft では、Azure Key Vault を使用して、アクセスキーの管理とローテーションをするようお勧めします。 アプリケーションは、Key Vault 内のキーに安全にアクセスできるので、アプリケーションコードと一緒に格納することは避けてください。 キー管理に Key Vault を使用する方法の詳細については、次の記事を参照してください:

- [Azure Key Vault と PowerShell を使用してストレージ アカウント キーを管理する](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Azure Key Vault と Azure CLI を使用してストレージアカウントキーを管理する](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>アクセス キーを手動でローテーションする

Microsoft では、ストレージアカウントのセキュリティを確保するために、アクセスキーを定期的にローテーションすることをお勧めします。 可能であれば、Azure Key Vault を使用してアクセスキーを管理してください。 Key Vault を使用していない場合は、キーを手動でローテーションする必要があります。

キーをローテーションさせることができるように、2 つのアクセス キーが割り当てられます。 2 つのキーで、アプリケーションはプロセス全体を通じて Azure Storage へのアクセスを維持します。

> [!WARNING]
> アクセス キーの再生成は、ストレージ アカウント キーに依存するあらゆるアプリケーションまたは Azure サービスに影響を与える可能性があります。 メディア サービス、クラウド、モバイル アプリケーション、Azure Storage 向けのグラフィカル ユーザー インターフェイス アプリケーション ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) を含む、アクセス キーを使用してストレージ アカウントにアクセスするクライアントは新しいキーを使用するように更新する必要があります。

次のプロセスでストレージ アカウント キーをローテーションさせます。

1. セカンダリ キーを使用するようにアプリケーション コードの接続文字列を更新します。
2. ストレージ アカウントのプライマリ アクセス キーを再生成します。 Azure portal の **[アクセス キー]** ブレードで、 **[Key1 の再生成]** をクリックし、確認画面で **[はい]** をクリックして新しいキーを生成します。
3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
4. 同様に、セカンダリ アクセス キーを再生成します。

> [!NOTE]
> Microsoft では、すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーを循環させるときに、一部のアプリケーションがアクセス権を失います。

## <a name="next-steps"></a>次のステップ

- [Azure ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-account-create.md)
