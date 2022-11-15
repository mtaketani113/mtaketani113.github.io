---
layout: default
title: List→Mapの変換の(u, v) -> v の意味
description: JavaのList→Mapの変換をラムダ式で(u, v) -> v の意味について調べてみました。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-07-18
last_modifeid_at: 2022-07-19
---

[List→Mapの変換で順序を保持する](/it/java/listToMapSort.html)で
JavaのList→Mapの変換をラムダ式で順序が保持する方法を記載しました。

その際に`(u, v) -> v`というのがありましたが、これが何を意味しているかを調べてみました。

どうも、同一のキーがあった時にどうしますかということを書いているようです。

実際に試してみましょう。

以下のようなソースを書きました。

```Java
public class Main {

  public static void main(String[] args) throws Exception {

    @Getter
    class Test{
      String key;
      int value;
      
      Test(String key, int value){
        this.key = key;
        this.value = value;
      }
    }
    
    List<Test> testList = new ArrayList<>();
    
    for(int i = 0; i < 10; i++) {
      Test test = new Test("key" + i, i);
      testList.add(test);
    }

    Test add = new Test("key0", 10);
    testList.add(add);
    
    Map<String, Integer> testMap =testList.stream()
      .collect(Collectors.toMap(
        Test::getKey,
        Test::getValue,
        (u, v) -> v, // ここを変更します
        LinkedHashMap::new
        )
      );
    
    testMap.forEach((u, v) -> 
        System.out.println(u + " "+ v)
    );  
  }
}
```

以下のように、1つ目の値が後に入った`key0 10`が出ます。

```
key0 10
key1 1
key2 2
key3 3
key4 4
key5 5
key6 6
key7 7
key8 8
key9 9
```

これで以下の`Collectors.toMap`の部分を変更します。

```Java
Map<String, Integer> testMap =testList.stream()
  .collect(Collectors.toMap(
    Test::getKey,
    Test::getValue,
    (u, v) -> u, // ここを変更
    LinkedHashMap::new 
    )
  );
```

そうすると結果は以下のように1つ目の値が
先に入った`key0 0`が出ます。

```
key0 0
key1 1
key2 2
key3 3
key4 4
key5 5
key6 6
key7 7
key8 8
key9 9
```

また、足し算にもできます。
0番目だと0+10で後勝ちと変わらないので、key1に変えて試してみます。

```Java
Test add = new Test("key1", 10);
testList.add(add);
	    
Map<String, Integer> testMap =testList.stream()
 .collect(Collectors.toMap(
	  Test::getKey,
	  Test::getValue,
    (u, v) -> u + v, // ここを変更
    LinkedHashMap::new
    )
  );
```

結果はkey1のところが足し算になっています。
```
key0 0
key1 11
key2 2
key3 3
key4 4
key5 5
key6 6
key7 7
key8 8
key9 9
```

Exceptionをthrowもできます。

```Java
Map<String, Integer> testMap =testList.stream()
 .collect(Collectors.toMap(
	  Test::getKey,
	  Test::getValue,
    (u, v) -> {throw new IllegalArgumentException();},, // ここを変更
    LinkedHashMap::new
    )
  );
```

## まとめ

`Collectors.toMap`の`(u, v) -> v`の部分は同一のキーがあった場合の処理のようです。

- `(u, v) -> v`だと後勝ち
- `(u, v) -> u`だと先勝ち
- `(u, v) -> u + v`だと足し算します。
- `(u, v) -> {throw new IllegalArgumentException();}`だと`IllegalArgumentException`をthrowします。

上記のようになりますので、場合に合わせて利用ください。