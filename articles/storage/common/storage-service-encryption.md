---
title: 保存データに対する Azure Storage 暗号化
description: Azure Storage では、クラウドに永続化される前にデータを自動的に暗号化することによって、データが保護されます。 Microsoft のマネージド キーを利用してストレージ アカウント内のデータを暗号化することも、独自のキーで暗号化を管理することもできます。
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c737ccf83dae0cc4b198b9cd708a55b988e6593b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457945"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>保存データに対する Azure Storage 暗号化

Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 Azure Storage 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-azure-storage-encryption"></a>Azure Storage 暗号化について

Azure Storage 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。 Azure Storage 暗号化は、Windows での BitLocker 暗号化に似ています。

Azure Storage 暗号化は、Resource Manager と従来のストレージ アカウントの両方を含む、すべてのストレージ アカウントで有効になります。 Azure Storage 暗号化を無効にすることはできません。 データは既定で保護されるので、Azure Storage 暗号化を利用するために、コードまたはアプリケーションを変更する必要はありません。

ストレージ アカウント内のデータは、パフォーマンス レベル (Standard または Premium)、アクセス層 (ホットまたはクール)、デプロイ モデル (Azure Resource Manager またはクラシック) に関係なく、暗号化されます。 アーカイブ層のすべての BLOB も暗号化されます。 すべての Azure Storage 冗長性オプションは暗号化をサポートし、geo レプリケーションが有効になっている場合は、プライマリとセカンダリ両方のリージョンのすべてのデータが暗号化されます。 BLOB、ディスク、ファイル、キュー、テーブルなど、すべての Azure Storage リソースが暗号化されます。 すべてのオブジェクト メタデータも暗号化されます。 Azure Storage 暗号化に対する追加コストはありません。

2017 年 10 月 20 日より後に Azure Storage に書き込まれたすべてのブロック BLOB、追加 BLOB、またはページ BLOB は暗号化されます。 この日付より前に作成された BLOB は、引き続きバックグラウンド プロセスによって暗号化されます。 2017 年 10 月 20 日より前に作成された BLOB を強制的に暗号化するには、BLOB を書き換えることができます。 BLOB の暗号化状態を確認する方法については、「[BLOB の暗号化状態を確認する](../blobs/storage-blob-encryption-status.md)」を参照してください。

Azure Storage 暗号化の基になっている暗号化モジュールについて詳しくは、「[Cryptography API: Next Generation (暗号化 API: 次世代)](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

新しいストレージ アカウント内のデータは Microsoft マネージド キーで暗号化されます。 Microsoft マネージド キーを利用してデータを暗号化することも、独自のキーで暗号化を管理することもできます。 独自のキーで暗号化を管理する場合は、次の 2 つのオプションがあります。

- Azure Key Vault では、BLOB ストレージと Azure Files でのデータの暗号化と暗号化解除に使用する "*カスタマー マネージド キー*" を指定できます。<sup>1、2</sup> カスタマー マネージド キーの詳細については、「[Azure Key Vault でカスタマー マネージド キーを使用して Azure Storage の暗号化を管理する](encryption-customer-managed-keys.md)」を参照してください。
- BLOB ストレージの操作では、"*カスタマー指定のキー*" を指定できます。 BLOB ストレージ に対して読み取りまたは書き込み要求を行うクライアントは、BLOB データの暗号化と暗号化解除の方法を細かく制御するために、要求に暗号化キーを含めることができます。 カスタマー指定のキーの詳細については、「[BLOB ストレージに対する要求で暗号化キーを指定する (プレビュー)](encryption-customer-provided-keys.md)」を参照してください。

次の表では、Azure Storage 暗号化のキー管理オプションを比較しています。

|                                        |    Microsoft のマネージド キー                             |    カスタマー マネージド キー                                                                                                                        |    カスタマー指定のキー                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    暗号化/暗号化解除の操作    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    サポートされている Azure Storage サービス    |    All                                                |    BLOB ストレージ、Azure Files<sup>1、2</sup>                                                                                                               |    BLOB ストレージ                                                                  |
|    キー記憶域                         |    Microsoft キー ストア    |    Azure Key Vault                                                                                                                              |    お客様独自のキー ストア                                                                 |
|    キーのローテーションの責任         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    キー コントロール                          |    Microsoft                                     |    Customer                                                                                                                    |    Customer                                                                 |

<sup>1</sup> Queue storage でのカスタマー マネージド キーの使用をサポートするアカウントの作成については、[キューのカスタマー マネージド キーをサポートするアカウントの作成](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)に関するページを参照してください。<br />
<sup>2</sup> Table Storage でのカスタマー マネージド キーの使用をサポートするアカウントの作成については、[テーブルのカスタマー マネージド キーをサポートするアカウントの作成](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault とは](../../key-vault/general/overview.md)
- [Azure portal から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-portal.md)
- [PowerShell から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-powershell.md)
- [Azure CLI から Azure Storage 暗号化用にカスタマー マネージド キーを構成する](storage-encryption-keys-cli.md)
