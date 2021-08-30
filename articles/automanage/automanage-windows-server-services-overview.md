---
title: Windows Server サービス用 Automanage (プレビュー)
description: Windows Server サービス用 Automanage の概要と Windows Server Aure Edition に関わる機能
author: nwashburn-ms
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 07/09/2021
ms.author: niwashbu
ms.openlocfilehash: 475ea083a6412f38093f601ce34da8775357fa3e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286459"
---
# <a name="automanage-for-windows-server-services-preview"></a>Windows Server サービス用 Automanage (プレビュー)

Windows Server サービス用 Automanage によって、_Windows Server Aure Edition_ に特化した新機能が提供されます。  次のような機能があります。
- ホットパッチ
- SMB over QUIC
- 拡張ネットワーク

> [!IMPORTANT]
> Windows Server サービス用 Automanage は現在パブリック プレビュー段階にあります。 後述するホットパッチ機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Server サービス用 Automanage の機能は、これらの _Windows Server Azure Edition_ のイメージの 1 つ以上で見つかります。 

- Windows Server 2019 Datacenter: Azure Edition (コア)
- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

機能はイメージによって異なります。詳細については、[概要](#getting-started-with-windows-server-azure-edition)に関するページを参照してください。

## <a name="automanage-for-windows-server-capabilities"></a>Windows Server サービス用 Automanage

### <a name="hotpatch"></a>ホットパッチ

パブリック プレビュー段階のホットパッチは、次のイメージで利用できます。

- Windows Server 2019 Datacenter: Azure Edition (コア)
- Windows Server 2022 Datacenter: Azure Edition (コア)

ホットパッチにより、再起動しなくても、VM にセキュリティ更新プログラムを適用できます。  さらに、Windows Server 用 Automanage によって、ホットパッチのオンボード、構成、オーケストレーションが自動化されます。  詳細については、[ホットパッチ](automanage-hotpatch.md)に関するページを参照してください。  

### <a name="smb-over-quic"></a>SMB over QUIC

パブリック プレビュー段階の SMB over QUIC は、次のイメージで利用できます。

- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

SMB over QUIC を使用すると、ユーザーは VPN を使用せずにリモートで作業しているときに、SMB トラフィックを QUIC プロトコル経由でトンネリングすることで、ファイルにアクセスできます。  詳細については、「[SMB over QUIC](/windows-server/storage/file-server/smb-over-quic)」を参照してください。  

### <a name="azure-extended-network"></a>Azure 拡張ネットワーク

パブリック プレビュー段階の Azure 拡張ネットワークは、次のイメージで利用できます。

- Windows Server 2022 Datacenter: Azure Edition (デスクトップ エクスペリエンス)
- Windows Server 2022 Datacenter: Azure Edition (コア)

Azure 拡張ネットワークを使用すると、オンプレミスのサブネットを Azure に拡張して、オンプレミスの仮想マシンが Azure に移行されるときに、元のオンプレミスのプライベート IP アドレスが保持されるように設定できます。 詳細については、[Azure 拡張ネットワーク](/windows-server/manage/windows-admin-center/azure/azure-extended-network)に関するページを参照してください。  


## <a name="getting-started-with-windows-server-azure-edition"></a>Windows Server Azure Edition の概要

Windows Server 用 Automanage のどの機能を使用するかを事前に検討し、その後それらのすべての機能がサポートされる、対応する VM イメージを選択することが重要です。  _Windows Server Azure Edition_ のイメージによっては、一部の機能にしか対応していません。詳細については、以下の表を参照してください。

### <a name="deciding-which-image-to-use"></a>使用するイメージを決定する 

|画像|機能|
|--|--|
| Windows Server 2019 Datacenter: Azure Edition (コア) | ホットパッチ | 
|Windows Server 2022  Datacenter: Azure Edition (デスクトップ エクスペリエンス) | SMB over QUIC、拡張ネットワーク | 
| Windows Server 2022 Datacenter: Azure Edition (コア) | ホットパッチ、SMB over QUIC、拡張ネットワーク | 

### <a name="creating-a-vm"></a>VM の作成

新しい VM で Windows Server 用 Automanage の使用を開始するには、お好みの方法を使用して Azure VM を作成し、使用する [機能セット](#getting-started-with-windows-server-azure-edition)に対応する _Windows Server Azure Edition_ のイメージを選択します。  VM の作成時にそれらの機能の構成が必要になる場合があります。 VM の構成の詳細については、個々の機能のトピック ([ホットパッチ](automanage-hotpatch.md)など) で確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Automanage の詳細情報](automanage-virtual-machines.md)