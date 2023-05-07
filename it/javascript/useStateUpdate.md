---
layout: default
title: useStateでのオブジェクトの更新
description: ReactでuseStateでJsonや配列を更新したのですがうまくいかず、少しは待ったので記載します。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2023-05-04
last_modifeid_at: 2023-05-04
---

ReactでuseStateで連想配列やJsonや配列を更新したのですがうまくいかず、少しは待ったので記載します。

useStateでsetすると画面が更新されるのですが、
以下のように記載してボタンをクリックしたのですが、更新されませんでした。

## useStateで更新されない書き方

```Javascript
const [customer, setCustomer] = useState({
  id: '',
  name: '',
  address: '',
})

・・・・

const click = () => {
  customer.id = '100';
  customer.name = 'XX株式会社';
  customer.address = '東京都';

  setCustomer(customer);
}
```

## 原因

原因はオブジェクトの参照先が変わっていないためのようです。

Jsonや配列だと参照先が変わっておらず、中身のデータが変わっているだけだと
setしても、変わったと認識されないようです。

以外に単純な原因ですが、気づけなかった。

## 対策

対策は、参照先が変わるようにするだけです。

連想配列の時はこんな感じ

```Javascript
const [customer, setCustomer] = useState({
  id: '',
  name: '',
  address: '',
})

・・・・

const click = () => {
  customer.id = '100';
  customer.name = 'XX株式会社';
  customer.address = '東京都';

  setCustomer({...customer,　id: '101',　name: 'YY株式会社',　address: '大阪府'});
}
```

`{...xxx, param: yyy}`という形で書くことでオブジェクトのコピーが作られて更新されるため
参照先が変わるのでsetすることで更新されるようになります。

配列の時も同様で以下のように書けばよいです。

```Javascript
setCustomers([...customers, {id: '101', name: 'YY株式会社', address: '大阪府'}])
```

## まとめ

reactの`useState`でJsonや配列を更新する場合、
参照先を変えないと更新がかからないです。

コピーを作って更新するようにしましょう。