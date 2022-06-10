---
layout: default
title: flushでOutOfMemoryを防ぐ
description: ファイル出力で大きなサイズのファイルを出力する際にflushしないとOutOfMemoryになる可能性があります。flushすることで、対処しましょう。
category: java
image: /images/it/java/cafe_mark.png
created_at: 2022-06-11
last_modifeid_at: 2022-06-11
---

ファイル出力で大きなサイズのファイルを出力する際に`flush`しないと`OutOfMemory`になる可能性があります。  
`flush`はためた情報をファイルに出力します。出力することで今までためてたメモリの情報を解放できます。
`close`するときにも`flush`されるのですが、ファイルが大きいときはある程度ためて出力するなどするとよいと思います。

プログラムの例です。

```Java
File file = new File("c:\\tmp\\test.txt");
try(BufferedWriter bw = new BufferedWriter(new FileWriter(file))){
  for(int i = 0; i < 100; i++) {
    bw.write("こんにちは");
    bw.newLine();
    bw.flush();
  }
}catch(IOException e){
  System.out.println(e);
}
```

このプログラムでは1行1行出力してますが、
ある程度ためてflushするなどもあるかと思います。

この程度でしたらflushを入れなくても特に問題なく動くかと思いますが、
ファイルサイズが数メガ、数ギガバイトとなった時は、メモリを大量に使うことになります。
`bw.write`はメモリ上に蓄えているだけです。`flush`で実際にファイルに出しています。

ちなみに、Java7からはtry-with-resourcesを使えて、`AutoCloseable`が実装されている場合
`try(BufferedWriter bw = new BufferedWriter(new FileWriter(file)))`と書くことで
`finally`で`close`を書かなくてもよくなっていますので、どんどん利用しましょう。

ちなみに`close`も書くとこんな感じです。やっぱり長くなりますね~。
```Java
File file = new File("c:\\tmp\\test.txt");
BufferedWriter bw = new BufferedWriter(new FileWriter(file))
try(BufferedWriter bw = new BufferedWriter(new FileWriter(file))){
  for(int i = 0; i < 100; i++) {
    bw.write("こんにちは");
    bw.newLine();
    bw.flush();
  }
}catch(IOException e){
  System.out.println(e);
}finally {
  try {
    if(bw != null) {
      bw.close();
    }
  } catch(IOException e) {
    e.printStackTrace();
  }
}
```