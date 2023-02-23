# Gcp Datastore, Projection Query的坑

### 有天用projection query的時候看到我的output居然增殖了

```python!
from google.cloud import datastore

client = datastore.Client()

query = client.query(kind='Person')
query.projection = ['emails']
results = query.fetch()
```
如果只看這段程式碼，應該會覺得“就很一般的投影查詢，我只要看找下來的entity內的email column而已”。

對，而且看起來gcp的文件也是這樣想的：
> https://cloud.google.com/datastore/docs/concepts/queries#projection_queries

```!
Projection queries

Most queries return whole entities as their results, but often an application is actually interested in only a few of the entity's properties. Projection queries allow you to query for just those specific properties of an entity that you actually need, at lower latency and cost than retrieving the entire entity.

Projection queries require the specified properties to be indexed.
```

好的，那假設我今天放了一個 **list** 在email欄位內呢？

假設你的entity內的email欄位長這樣：["a@hotmail.com", "b@hotmail.com", "c@hotmail.com"]

畢竟一個人可能有很多個信箱，這樣存資料看起來也是合理的

如果我們的投影查詢要把email欄位投出來，預期的output應該是這樣：

```!
[<Entity('Person', id) {'email': ["a@hotmail.com", "b@hotmail.com", "c@hotmail.com"]}>, entity2, entity3, ....., entityN]
```

但是實際上，datastore的投影查詢會把list內的東西**全部分離**：

```!
[<Entity('Person', id) {'email': 'a@hotmail.com'}>, <Entity('Person', id) {'email': 'b@hotmail.com'}>, <Entity('Person', id) {'promotional_methods': 'c@hotmail.com'}>......]

```

看到這邊的時候，我以為我對投影查詢的理解從開始就出錯了，~~還好不是~~

## 可能原因

這邊的表現目前還找不到文件佐證到底為什麼，但從datastore的GUI表現來看(對list欄位可以直接搜尋element)，推測我們在用list存欄位資料時，實際上datastore是存成了複數個entity(list內有幾筆就有幾個entity，~~我暫時不想思考排列組合~~)，對照gcp在對待index上那個空間看起來不要錢的態度來說，這個推論應該是有點道理的，當然如果正在看這篇文的你知道文件有寫，請一定在下面留言

## 結語

所以如果你正在使用datastore的projection query，先去檢查一下表現是不是符合預期，尤其是你的entity欄位內包含list型別的話......

自從我在endpoint內抓到gateway要用的東西後，我對gcp文件的信賴度就是負的了...~~還是文件可以提issue跟PR我就去丟~~