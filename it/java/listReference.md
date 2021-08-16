---
layout: default
title: Listの参照中の編集
description: Listはスレッドセーフではないため参照中に編集するとExceptionが発生することがあります。その現象について記載します。
category: java
created_at: 2021-08-13
last_modifeid_at: 2021-08-13
---

Listはスレッドセーフではないので、マルチスレッドでなくても参照中に編集すると`java.util.ConcurrentModificationException`が発生することがあります。

しかし、以外に発生する場合としない場合があるので、記載します。

拡張for文を利用する場合です。
```Java
        List<String> list = new ArrayList<>();
        
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");
        
        for(String str : list){
            if("c".equals(str)) {
                list.remove("c");
            }else {
                System.out.println(str);
            }
        }
```

上記の場合は
```
a
b
```
と正常終了します。

`c`が削除されてずれるため、最後に参照されないので`java.util.ConcurrentModificationException`は発生しません。

次にListに一つ要素を足した場合です。
```Java
        List<String> list = new ArrayList<>();
        
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");
        list.add("e");
        
        for(String str : list){
            if("c".equals(str)) {
                list.remove("c");
            }else {
                System.out.println(str);
            }
        }
```



この場合は`e`を参照する際に`java.util.ConcurrentModificationException`が発生します。

結果はこんな感じ。
```Java
a
b
Exception in thread "main" java.util.ConcurrentModificationException
    at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1042)
    at java.base/java.util.ArrayList$Itr.next(ArrayList.java:996)
    ・・・・
```

`c`が削除されますが、ずれて最後の`e`が参照されるためNGなのです。

次に普通にfor文を利用した場合です。
```Java
        List<String> list = new ArrayList<>();
        
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");
        list.add("e");
        
        for(int i = 0; i < list.size(); i++){
            String str = list.get(i);
            
            if("c".equals(str)) {
                list.remove("c");
            }else {
                System.out.println(str);
            }
        }
```
こちらは正常に動き結果は以下の通りになります。
```
a
b
e
```
参照と編集が同時には行われていないからですかね。

次に`forEach`を利用した場合です。
```Java
        List<String> list = new ArrayList<>();
        
        list.add("a");
        list.add("b");
        list.add("c");
        list.add("d");

        list.forEach(str ->{
            if("c".equals(str)) {
                list.remove("c");
            }else {
                System.out.println(str);
            }
        });
```

こちらは`c`を削除した瞬間に`java.util.ConcurrentModificationException`が発生します。

結果はこんな感じ。
```Java
a
b
Exception in thread "main" java.util.ConcurrentModificationException
    at java.base/java.util.ArrayList.forEach(ArrayList.java:1542)
    ・・・
```

色々なパターンがあるので、for文を回してlist自体を削除するのが、あまりよくないのかもしれませんね。
新しいListを作ってそちらに格納するか、
`list.romve("c");`や条件が複雑なら`list.removeIf(s -> "c".equals(s));`などを利用するのがよいですかね。