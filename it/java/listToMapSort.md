---
layout: default
title: List→Mapの変換で順序を保持する
description: List→Mapの変換をラムダ式で普通に実施するとListの順序が保持されません。保持する方法を記載します。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-07-18
last_modifeid_at: 2022-07-18
---

List→Mapの変換をラムダ式で普通に実施するとListの順序が保持されません。保持する方法を記載します。

まずは、以下のようなプログラムを作成しました。

```Java
public class Main {

  public static void main(String[] args) throws Exception {

    @Getter
    class Test{
      String key;
      String value;
      
      Test(String key, String value){
        this.key = key;
        this.value = value;
      }
    }
    
    List<Test> testList = new ArrayList<>();
    
    for(int i = 0; i < 10; i++) {
      Test test = new Test("key" + i, "value" + i);
      testList.add(test);
    }
    
    Map<String, String> testMap =testList.stream()
      .collect(Collectors.toMap(
        Test::getKey,
        Test::getValue
        )
      );
    
    testMap.forEach((u, v) -> 
        System.out.println(u + " "+ v)
    );  
  }
}
```

実行すると以下のような結果が出て、順番がバラバラになります。

```
key1 value1
key2 value2
key0 value0
key5 value5
key6 value6
key3 value3
key4 value4
key9 value9
key7 value7
key8 value8

```

これで以下の`Collectors.toMap`の部分を変更します。

```Java
Map<String, String> testMap =testList.stream()
  .collect(Collectors.toMap(
    Test::getKey,
    Test::getValue,
    (u, v) -> v, // この行を追加
    LinkedHashMap::new // この行を追加
    )
  );
```

そうすると結果は以下のように順番になります。

```
key0 value0
key1 value1
key2 value2
key3 value3
key4 value4
key5 value5
key6 value6
key7 value7
key8 value8
key9 value9
```

しかし、このままだとkeyが重複したときに後が地になります。
試しにfor分の後に以下のように書いてみましょう。

```Java
Test add = new Test("key0", "key10");
testList.add(add);
```

そうすると、以下のように値が変わります。
```
key0 key10
key1 value1
key2 value2
key3 value3
key4 value4
key5 value5
key6 value6
key7 value7
key8 value8
key9 value9
```

キーが重複した場合にExceptionを出したい場合は
`(u, v) -> v`ここの部分を`(u, v) -> {throw new IllegalArgumentException();}`と変えることことで
キーが重複している場合にExceptionをThrowします。


## まとめ

List→Mapにラムダ式で変換する場合に、順序を保持するには、`Collectors.toMap`で以下を追加すれ場可能です。
```Java
  (u, v) -> v, // この行を追加
  LinkedHashMap::new // この行を追加
```

`(u, v) -> v`ここの部分を`(u, v) -> {throw new IllegalArgumentException();}`と変えることことで
キーが重複している場合にExceptionをThrowすることができます。
