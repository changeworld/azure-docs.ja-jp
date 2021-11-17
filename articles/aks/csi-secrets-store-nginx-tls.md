---
title: Azure Kubernetes Service で TLS を使用して NGINX イングレス コントローラーを有効にするようにシークレット ストア CSI ドライバーを設定する
description: Azure Kubernetes Service (AKS) で TLS を使用して NGINX イングレス コントローラーを有効にするようにシークレット ストア CSI ドライバーを設定する方法。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: f7c40dbcf0944ca5c78182d72346f3fc295ef50c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848745"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>TLS を使用して NGINX イングレス コントローラーを有効にするようにシークレット ストア CSI ドライバーを設定する

この記事では、Azure Kubernetes Service (AKS) クラスターと Azure Key Vault (AKV) インスタンスで TLS を使用して NGINX イングレス コントローラーをセキュリティで保護するプロセスについて説明します。 詳細については、[Kubernetes の TLS][kubernetes-ingress-tls] に関するページを参照してください。

イングレス TLS 証明書をクラスターにインポートするには、次の 2 つの方法のいずれかを使用します。

- **アプリケーション** - アプリケーション配置マニフェストによって、プロバイダー ボリュームが宣言されてマウントされます。 アプリケーションがデプロイされているときだけ、クラスター内で証明書が利用可能になります。アプリケーションが削除されると、シークレットも削除されます。 このシナリオは、アプリケーションのセキュリティ　インフラストラクチャおよびクラスターとの統合を担当する開発チームに適しています。
- **イングレス コントローラー** - プロバイダー ボリュームを宣言してマウントするようにイングレス デプロイが変更されます。 シークレットは、イングレス ポッドの作成時にインポートされます。 アプリケーションのポッドは TLS 証明書にアクセスできません。 このシナリオは、あるチーム (つまり、IT) がインフラストラクチャとネットワーク コンポーネント (HTTPS TLS 証明書を含む) を管理およびプロビジョニングし、他のチームがアプリケーションのライフサイクルを管理するシナリオに適しています。 この場合、イングレスは 1 つの名前空間またはワークロードに固有であり、アプリケーションと同じ名前空間にデプロイされます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- 開始する前に、Azure CLI バージョンが `2.30.0` 以上であることを確認するか、[最新バージョンをインストール](/cli/azure/install-azure-cli)してください。
- シークレット ストア CSI ドライバーが構成されている AKS クラスター。
- Azure Key Vault インスタンス。


## <a name="generate-a-tls-certificate"></a>TLS 証明書を生成する

```bash
export CERT_NAME=ingresscert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out ingress-tls.crt \
    -keyout ingress-tls.key \
    -subj "/CN=demo.test.com/O=ingress-tls"
```

### <a name="import-the-certificate-to-akv"></a>AKV に証明書をインポートする

```bash
export AKV_NAME="[YOUR AKV NAME]"
openssl pkcs12 -export -in ingress-tls.crt -inkey ingress-tls.key  -out $CERT_NAME.pfx
# skip Password prompt
```

```azurecli-interactive
az keyvault certificate import --vault-name $AKV_NAME -n $CERT_NAME -f $CERT_NAME.pfx
```

## <a name="deploy-a-secretproviderclass"></a>SecretProviderClass をデプロイする

まず、新しい名前空間を作成します。

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

[アクセス ID を提供する方法][csi-ss-identity-access]を選択し、それに従って SecretProviderClass YAML を構成します。 補足:
- `objectType=secret` は、AKV からプライベート キーと証明書を取得する唯一の方法なので、必ずこれを使用してください。
- `secretObjects` セクションに `type` として `kubernetes.io/tls` を設定します。

SecretProviderClass の例については、次を参照してください。

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-tls
spec:
  provider: azure
  secretObjects:                            # secretObjects defines the desired state of synced K8s secret objects
  - secretName: ingress-tls-csi
    type: kubernetes.io/tls
    data: 
    - objectName: $CERT_NAME
      key: tls.key
    - objectName: $CERT_NAME
      key: tls.crt
  parameters:
    usePodIdentity: "false"
    keyvaultName: $AKV_NAME                 # the name of the AKV instance
    objects: |
      array:
        - |
          objectName: $CERT_NAME
          objectType: secret
    tenantId: $TENANT_ID                    # the tenant ID of the AKV instance
```

Kubernetes クラスターに SecretProviderClass を適用します。

```bash
kubectl apply -f secretProviderClass.yaml -n $NAMESPACE
```

## <a name="deploy-the-ingress-controller"></a>イングレス コントローラーをデプロイする

### <a name="add-the-official-ingress-chart-repository"></a>公式のイングレス チャート リポジトリを追加する

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### <a name="configure-and-deploy-the-nginx-ingress"></a>NGINX イングレスを構成してデプロイする

前述のように、シナリオにしたがって、証明書をアプリケーションとイングレス コントローラーのどちらにバインドするかを選択できます。 選択内容に応じて、以下の手順に従います。

#### <a name="bind-certificate-to-application"></a>証明書をアプリケーションにバインドする

アプリケーションのデプロイでは、シークレット ストア CSI ドライバーの Azure Key Vault プロバイダーが参照されます。

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>証明書をイングレス コントローラーにバインドする

イングレス コントローラーのデプロイでは、シークレット ストア CSI ドライバーの Azure Key Vault プロバイダーが参照されます。

> [!NOTE]
> アクセス方法として Azure Active Directory (AAD) ポッド ID を使用しない場合は、`--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME` の行を削除します。

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME \
    -f - <<EOF
controller:
  extraVolumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-tls"
  extraVolumeMounts:
      - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
EOF
```

Kubernetes シークレットが作成されていることを確認します。

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>アプリケーションの配置

ここでも、シナリオに応じて、手順は若干変わります。 これまでに選択したシナリオに対応する指示に従ってください。

### <a name="deploy-the-application-using-an-application-reference"></a>アプリケーション参照を使用してアプリケーションをデプロイする

次の内容を含む `deployment.yaml` という名前のファイルを作成します。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
      volumes:
        - name: secrets-store-inline
          csi:
            driver: secrets-store.csi.k8s.io
            readOnly: true
            volumeAttributes:
              secretProviderClass: "azure-tls"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

それをクラスターに適用します。

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Kubernetes シークレットが作成されていることを確認します。

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>イングレス コントローラー参照を使用してアプリケーションをデプロイする

次の内容を含む `deployment.yaml` という名前のファイルを作成します。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

それをクラスターに適用します。

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>シークレットを参照するイングレス リソースをデプロイする

最後に、シークレットを参照する Kubernetes イングレス リソースをデプロイできます。 次の内容を含む `ingress.yaml` という名前のファイルを作成します。

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-tls
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.test.com
    secretName: ingress-tls-csi
  rules:
  - host: demo.test.com
    http:
      paths:
      - backend:
          service:
            name: busybox-one
            port:
              number: 80
        path: /(.*)
      - backend:
          service:
            name: busybox-two
            port:
              number: 80
        path: /two(/|$)(.*)
```

前に作成したシークレットを参照する `tls` セクションをメモし、ファイルをクラスターに適用します。

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>イングレス コントローラーの外部 IP アドレスを取得する

`kubectl get service` を使用してイングレス コントローラーの外部 IP アドレスを取得します。

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>TLS でセキュリティ保護されたイングレスをテストする

`curl` を使用して、TLS を使用してイングレスが適切に構成されていることを確認します。 前の手順で取得した外部 IP を必ず使用してください。

```bash
curl -v -k --resolve demo.test.com:443:52.xx.xx.xx https://demo.test.com

# You should see output similar to the following
*  subject: CN=demo.test.com; O=ingress-tls
*  start date: Oct 15 04:23:46 2021 GMT
*  expire date: Oct 15 04:23:46 2022 GMT
*  issuer: CN=demo.test.com; O=ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
```

<!-- LINKS INTERNAL -->
[csi-ss-identity-access]: ./csi-secrets-store-identity-access.md 
<!-- LINKS EXTERNAL -->
[kubernetes-ingress-tls]: https://kubernetes.io/docs/concepts/services-networking/ingress/#tls