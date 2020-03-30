---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185839"
---
## <a name="guidelines-for-using-javascript"></a>JavaScript の使用に関するガイドライン

JavaScript を使用して、アプリケーションのインターフェイスをカスタマイズするときに、次のガイドラインに従います。

- `<a>` HTML 要素ではクリック イベントをバインドしないでください。
- Azure AD B2C コードやコメントへの依存関係は使用しないでください。
- Azure AD B2C の HTML 要素の順序や階層は変更しないでください。 UI 要素の順序を制御するには、Azure AD B2C のポリシーを使用します。
- 任意の RESTful サービスを呼び出すことができますが、次の考慮事項があります。
    - クライアント側の HTTP 呼び出しを許可するために、RESTful サービス CORS を設定することが必要な場合があります。
    - RESTful サービスは必ずセキュリティ保護し、HTTPS プロトコルのみを使用してください。
    - JavaScript を直接使用して Azure AD B2C のエンドポイントを呼び出すことはしないでください。
- JavaScript を埋め込んだり、外部の JavaScript ファイルにリンクしたりできます。 外部の JavaScript ファイルを使用する場合は、相対 URL ではなく必ず絶対 URL を使用してください。
- JavaScript フレームワーク:
    - Azure AD B2C では、特定のバージョンの jQuery を使用します。 別のバージョンの jQuery を含めないでください。 同じページに複数のバージョンを使用すると、問題が発生します。
    - RequireJS の使用はサポートされていません。
    - ほとんどの JavaScript フレームワークは、Azure AD B2C ではサポートされていません。
- `window.SETTINGS`、`window.CONTENT` オブジェクト (現在の UI 言語など) を呼び出すことによって、Azure AD B2C の設定を読み取ることができます。 これらのオブジェクトの値は変更しないでください。
- Azure AD B2C のエラー メッセージをカスタマイズするには、ポリシーでローカライズを使用します。
- ポリシーを使用して実現できるのであれば、それが一般に推奨される方法です。
