---
title: Azure Cosmos DB の MongoDB 用 API のデータを管理するための MongoDB 拡張コマンド
description: この記事では、MongoDB 拡張コマンドを使用して、Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583581"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理するために MongoDB 拡張コマンドを使用する 

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の MongoDB 用 API との通信は、オープン ソースで公開されている任意の [MongoDB クライアント ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 Azure Cosmos DB の MongoDB 用 API では、MongoDB [ワイヤ プロトコル](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)に従うことにより、既存のクライアント ドライバーを利用できます。

Azure Cosmos DB の MongoDB 用 API を使用することで、MongoDB アプリへの投資を保持しながら、グローバル配布、自動シャーディング、高可用性、低待機時間の保証、自動化、保存時の暗号化、バックアップなど多くの Cosmos DB の利点を活用できます。

## <a name="mongodb-protocol-support"></a>MongoDB のプロトコル サポート

既定では、Azure Cosmos DB の MongoDB 用 API はMongoDB サーバー バージョン 3.2 と互換性があります。詳細については、「[サポートされる機能と構文](mongodb-feature-support.md)」を参照してください。 現在、MongoDB バージョン 3.4 で追加された機能やクエリ演算子は、Azure Cosmos DB の MongoDB 用 API のプレビューとして使用できます。 次の拡張コマンドは、Azure Cosmos DB の MongoDB 用 API の格納データに対して CRUD 操作を実行するときに、Azure Cosmos DB の固有の機能をサポートします。

* [データベースの作成](#create-database)
* [データベースの更新](#update-database)
* [データベースの取得](#get-database)
* [コレクションの作成](#create-collection)
* [コレクションの更新](#update-collection)
* [コレクションの取得](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>データベースの作成

データベースの作成拡張コマンドは、新しい MongoDB データベースを作成します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。 CreateDatabase コマンドの形式は次のとおりです。

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction   |  string  |   カスタム コマンドの名前。"CreateDatabase" にする必要があります。      |
| offerThroughput | INT  | データベースに設定したプロビジョニング済みスループット。 このパラメーターは省略可能です。 |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**データベースの作成**

"test" という名前のデータベースを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**スループットを指定してデータベースを作成する**

"test" という名前で、プロビジョニング済みスループットが 1000 RU のデータベースを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>データベースの更新

データベースの更新拡張コマンドは、指定したデータベースに関連付けられているプロパティを更新します。 現時点では、更新できるのは "offerThroughput" プロパティのみです。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction    |    string     |   カスタム コマンドの名前。 "UpdateDatabase" にする必要があります。      |
|  offerThroughput   |  INT       |     データベースに設定する新しいプロビジョニング済みスループット。    |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**データベースに関連付けられているプロビジョニング済みスループットを更新する**

"test" という名前のデータべースのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>データベースの取得

データベースの取得拡張コマンドは、データベース オブジェクトを返します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。

```
{
  customAction: "GetDatabase"
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  customAction   |   string      |   カスタム コマンドの名前。 "GetDatabase" にする必要があります|
        
### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`        |   データベースの名前です。      |
|   `provisionedThroughput`  |    `int`      |    データベースに設定されているプロビジョニング済みスループット。 これは省略可能な応答パラメーターです。     |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**データベースを取得する**

"test" という名前のデータベースのデータベース オブジェクトを取得するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>コレクションの作成

コレクションの作成拡張コマンドは、新しい MongoDB コレクションを作成します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。 CreateCollection コマンドの形式は次のとおりです。

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

次の表では、コマンド内のパラメーターについて説明します。

| **フィールド** | **Type** | **必須** | **説明** |
|---------|---------|---------|---------|
| customAction | string | 必須 | カスタム コマンドの名前。 必ず "CreateCollection" にします。|
| collection | string | 必須 | コレクションの名前。 特殊文字は使用できません。|
| offerThroughput | INT | 省略可能* | データベースに設定するプロビジョニング済みスループット。 このパラメーターが指定されていない場合、既定値は 400 RU/秒に設定されます。 * 10,000 RU/秒を超えるスループットを指定するには、`shardKey` パラメーターが必要です。|
| shardKey | string | 省略可能* | シャード コレクション用シャード キーへのパス。 `offerThroughput` で 10,000 RU/秒を超える値を設定する場合、このパラメーターは必須です。  この値が指定されている場合は、挿入されたすべてのドキュメントでこの値が必要になります。 |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**シャード化されていないコレクションを作成する**

"testCollection" という名前で、プロビジョニング済みスループットが 1000 RU のシャード化されていないコレクションを作成するには、次のコマンドを使用します。 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**シャード コレクションを作成する**

"testCollection" という名前で、プロビジョニング済みスループットが 1,000 RU で、シャードキー プロパティが "a.b" のシャード コレクションを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>コレクションの更新

コレクションの更新拡張コマンドは、指定したコレクションに関連付けられているプロパティを更新します。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  customAction   |   string      |   カスタム コマンドの名前。 "UpdateCollection" にする必要があります。      |
|  collection   |   string      |   コレクションの名前。       |
| offerThroughput   |INT|   コレクションに設定するプロビジョニング済みスループット。|

## <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**コレクションに関連付けられているプロビジョニング済みスループットを更新する**

"testCollection" という名前のコレクションのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>コレクションの取得

コレクションの取得カスタム コマンドは、コレクション オブジェクトを返します。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction    |   string      |   カスタム コマンドの名前。 "GetCollection" にする必要があります。      |
| collection    |    string     |    コレクションの名前。     |

### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`     |   データベースの名前です。      |
| `collection`    |    `string`     |    コレクションの名前。     |
|  `shardKeyDefinition`   |   `document`      |  シャード キーとして使用されるインデックス仕様ドキュメント。 これは省略可能な応答パラメーターです。       |
|  `provisionedThroughput`   |   `int`      |    コレクションに設定するプロビジョニング済みスループット。 これは省略可能な応答パラメーターです。     |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**コレクションの取得**

"TestCollection" という名前のコレクションのコレクション オブジェクトを取得するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>カスタム コマンドの既定の出力

指定しない場合、カスタムの応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `code`    |   `int`      |   コマンドが失敗した場合 (ok == 0) のみ返されます。 MongoDB のエラー コードが含まれます。 これは省略可能な応答パラメーターです。      |
|  `errMsg`   |  `string`      |    コマンドが失敗した場合 (ok == 0) のみ返されます。 わかりやすいエラー メッセージが含まれます。 これは省略可能な応答パラメーターです。      |

## <a name="next-steps"></a>次のステップ

次は、以下の Azure Cosmos DB の概念について学習することができます。 

* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)
