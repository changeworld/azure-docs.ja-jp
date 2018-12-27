---
title: チュートリアル - Azure Batch AI と Horovod を使用した分散トレーニング | Microsoft Docs
description: チュートリアル - Azure Batch AI サービス、Azure CLI、Horovod を使用して分散モデルをトレーニングする方法。
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 45255845d8645391ee33471830ac2ef27870a40d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408631"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>チュートリアル:Horovod を使用して分散モデルをトレーニングする

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

このチュートリアルでは、Batch AI クラスターの複数のノードで分散ディープ ラーニング モデルを並列実行してモデルをトレーニングします。 Batch AI は、Azure GPU のクラスター上で機械学習および AI モデルを大規模にトレーニングするためのマネージド サービスです。 

このチュートリアルでは、一般的な Batch AI ワークフローと、Azure CLI を使用して Batch AI リソースを操作する方法について説明します。 取り上げるトピックは次のとおりです。

> [!div class="checklist"]
> * Batch AI ワークスペース、実験、クラスターの設定
> * 入力および出力用の Azure ファイル共有の設定
> * Horovod を使用したディープ ラーニング モデルの並列化
> * トレーニング ジョブの送信
> * ジョブの監視
> * トレーニング結果の取得

このチュートリアルでは、[Horovod](https://github.com/uber/horovod) と並列で実行するためにオブジェクト検出モデルが変更されます。 このモデルは、[CIFAR-10 画像データセット](https://www.cs.toronto.edu/~kriz/cifar.html)でトレーニングされます。 トレーニング ジョブは、24 個の vCPU と 4 個の GPU を含むクラスター上で実行され、完了までに約 60 分かかります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.38 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="why-use-horovod"></a>Horovod を使用する理由

このチュートリアルで使用する Horovod は、Tensorflow、Keras、PyTorch の分散トレーニング フレームワークです。 Horovod を使用すると、単一の GPU で実行するように設計されたトレーニング スクリプトを、わずか数行のコードを使用して分散システムで効率的に実行されるスクリプトに変換できます。

Batch AI では、Horovod に加え、他の一般的なオープンソース フレームワークを使用した分散トレーニングもサポートされています。 運用環境でモデルのトレーニングに使用するフレームワークのライセンス条項を必ず確認してください。

## <a name="prepare-the-batch-ai-environment"></a>Batch AI 環境の準備

### <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドを使用して、`eastus` リージョンに `batchai.horovod` という名前のリソース グループを作成します。 このリソース グループを使用して、Batch AI リソースをデプロイします。

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>ワークスペースの作成

`az batchai workspace create` コマンドを使用して、Batch AI ワークスペースを作成します。 ワークスペースとは、他の Batch AI リソースの最上位コレクションです。 次のコマンドでは、リソース グループに `batchaidev` というワークスペースを作成します。

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>実験の作成

Batch AI 実験では、クエリを実行し、まとめて管理する 1 つ以上のジョブをグループ化します。 次の `az batchai experiment create` コマンドでは、ワークスペースとリソース グループの下に `cifar` という実験を作成します。

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>GPU クラスターの設定

次に、実験を実行する GPU クラスターを設定します。 Batch AI には、特定のニーズに合わせてクラスターをカスタマイズするための柔軟なオプションが用意されています。

次の `az batchai cluster create` コマンドでは、ワークスペースとリソース グループの下に `nc6cluster` という 4 ノード クラスターを作成します。 既定では、クラスター内の VM は、コンテナー ベースのアプリケーションをホストするように設計された Ubuntu Server イメージを実行します。 この例のクラスター ノードでは、NVIDIA Tesla K80 GPU を 1 つ備えた `Standard_NC6` サイズを使用します。

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

`az batchai cluster show` コマンドを実行して、クラスターの状態を確認します。 クラスターが完全にプロビジョニングされるまで、通常は数分かかります。

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

クラスター作成の初期段階では、クラスターは `resizing` 状態になっています。 クラスターの状態が変わる間に、次の手順に進みます。 状態が `steady` になり、ノードが `idle` になったら、クラスターでトレーニング ジョブを実行できるようになります。 例: 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>ストレージの設定

`az storage account create` コマンドを使用して、トレーニング スクリプトとトレーニングの出力を保存するストレージ アカウントを作成します。

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

`az storage share create` コマンドを使用して、アカウントに `myshare` という Azure ファイル共有を作成します。

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

実際には、この同じストレージを複数のジョブと実験で使用できます。 リソースを整理しておくために、この実験に関連するファイルを格納するディレクトリをファイル共有内に作成します。 次の `az storage directory create` コマンドでは、`cifar` というディレクトリを作成します。

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

次に、実際のトレーニング スクリプトを準備し、新しく作成したディレクトリにアップロードします。

## <a name="create-the-training-script"></a>トレーニング スクリプトの作成

この実験では、Horovod を使用してオブジェクト検出モデルを並列で実行するために、いくつかの変更を加えて更新されたPython スクリプトを実行します。 [元のモデル](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py)では、TensorFlow バックエンドで Keras が使用されます。 

シェルの作業ディレクトリで、任意のテキスト エディターを使用して、次の内容を含む `cifar_cnn_distributed.py` という名前のファイルを作成します。 元のソース コードの変更には、`HOROVOD` プレフィックス付きのコメントが追加されています。

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

この例に示すように、Horovod フレームワークを使用した分散トレーニングを可能にするには、モデルを少しだけ更新する必要があります。 

このスクリプトでは、デモのために比較的小規模なモデルとデータセットを使用していることに注意してください。そのため、この分散モデルでは、必ずしもパフォーマンスが大幅に向上するわけではありません。 分散トレーニングの真の効果を確認するには、もっと大規模なモデルとデータセットを使用します。

## <a name="upload-the-training-script"></a>トレーニング スクリプトのアップロード

スクリプトが準備ができたら、以前に作成したファイル共有ディレクトリにスクリプトをアップロードします。 次の `az storage file upload` コマンドでは、ローカル作業ディレクトリから適切な場所にスクリプトをアップロードします。

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>トレーニング ジョブの送信

これまでの手順を完了したら、トレーニング ジョブを作成します。 Batch AI では、`job.json` ファイルを使用して、ジョブの実行方法のパラメーターを定義します。 任意のテキスト エディターを使用して、次の内容を含む `job.json` というジョブ構成ファイルを作成します。

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

各プロパティの説明を次に示します。

| プロパティ | 説明 |
| --------- | --------- |
| `nodeCount` | ジョブ専用のノードの数。 ここでは、ジョブは `4` つのノードで並列実行されます。 |
| `horovodSettings` | `pythonScriptFilePath` フィールドでは、以前に作成した `cifar` ディレクトリにある Horovod スクリプトのパスを定義します。 `commandLineArgs` フィールドは、スクリプトを実行するためのコマンド ライン引数です。 この実験では、必要な引数はモデルの保存先のディレクトリだけです。 `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` は、ファイル共有がマウントされているパスです。 | 
| `stdOutErrPathPrefix` | ジョブの出力とログを格納するパス。この例では、同じ `cifar` ディレクトリになります。 |
| `jobPreparation` | ジョブを実行する環境を準備するための特別な指示。 このスクリプトでは、指定された MPI および Horovod パッケージをインストールする必要があります。 |
| `containerSettings` | ジョブが実行されるコンテナーの設定。 このジョブでは、`tensorflow` を使用して作成された Docker コンテナーを使用します。

この構成を使用し、`az batchai job create` コマンドを使用してジョブを作成します。 次のコマンドでは、この時点までに設定されたすべてのリソースを使用して、`cifar_distributed` という新しいジョブをキューに登録します。 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

ノードが現在ビジー状態の場合、ジョブの実行を開始するまでにしばらく時間がかかることがあります。 `az batchai job show` コマンドを使用して、ジョブの実行状態を確認します。

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>分散トレーニングの視覚化

ジョブの実行が開始されたら、`az batchai cluster show` コマンドをもう一度使用して、クラスター ノードの状態を照会します。 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

出力は次のようになり、4 つのノードがすべて実行状態であることが示されます。 この結果は、4 つのノードがすべて、現在、分散トレーニングで使用されていることを示しています。

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>ジョブの監視

### <a name="list-output-files"></a>出力ファイルの一覧表示

ジョブの実行中に、`az batchai job file list` コマンドを使用して、ジョブで生成される出力ファイルを一覧表示します。

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

この実験では、出力は次のようになります。 ジョブの全体的な出力は `stdout.txt` に記録され、`stderr.txt` にはメインの実行で発生したエラーが出力されます。 その他のファイルは、個々のノードに対応する出力、エラー、ジョブの準備のログです。

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>出力ファイルのストリーミング

`az batchai job file stream` コマンドを使用して、ファイルの内容をストリーミングします。 次の例では、メイン出力ログをストリーミングします。

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

ジョブの実行中、このコマンドはトレーニング ジョブの標準出力をストリーミングし、次のような出力を表示します。

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

このスクリプトでは、25 エポックのトレーニングを実行します (トレーニング データ セットを学習させます)。 このプロセスには約 60 分かかります。 

## <a name="retrieve-the-results"></a>結果の取得

スクリプトが完了し、すべてが正常に実行されていれば、検証精度は約 70 から 75% になります。トレーニング済みモデルは、ファイル共有 (`cifar/saved_models/keras_cifar10_trained_model.h5`) に保存されます。 

通常、モデル トレーニングはより大規模なワークフローに含まれます。 たとえば、トレーニング済みモデルを別のアプリケーションで公開できます。 トレーニング済みモデルをローカルにダウンロードするには、`az storage file download` コマンドを使用します。 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>リソースのクリーンアップ

ジョブの実行が完了したら、コンピューティング コストを節約するためのベスト プラクティスとして、アイドル時間に課金されないようにすべてのクラスターを `0 nodes` に縮小します。 次の `az batchai cluster resize` コマンドを使用します。 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

後でジョブを実行するときは、サイズを 1 ノード以上に変更します。 

ワークスペースとストレージ アカウントを今後使用する予定がない場合は、`az group delete` コマンドを使用してリソース グループを削除します。 リソース グループを削除すると、そのグループに属するすべてのリソースが削除されます。

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Batch AI ワークスペース、実験、クラスターの設定
> * 入力および出力用の Azure ファイル共有の設定
> * Horovod を使用したモデルの並列化
> * トレーニング ジョブの送信
> * ジョブの監視
> * トレーニング結果の取得

さまざまなフレームワークでの Batch AI の使用例については、GitHub のレシピを参照してください。

> [!div class="nextstepaction"]
> [Batch AI Recipes](https://github.com/Azure/BatchAI/tree/master/recipes)
