---
layout: default
title: ReactのPropsの条件分岐
description: ReactにおけるJSXのProps（属性）を条件によって変えたいことがあり、Reactの型の条件分岐について記載します。
category: javascript
image: /images/it/javascript/javascript_code.jpg
created_at: 2023-05-19
last_modifeid_at: 2023-05-19
---

ReactにおけるJSXのProps（属性）を条件によって変えたいことがあり、
その方法について調べたので少し記載します。

参考サイトは[こちら](https://de-milestones.com/react-props-conditional-type/)です

## 条件分岐の書き方

型を必須部分と分岐する部分をわけて
分岐部分を「&」でつなぎ、
分岐をそれぞれ「|」でつなげば完了です。

分岐で使わないほうは「never」と書くことで
呼ぶ方では利用しなくてよいです。

具体的な書き方はこちらです。
```Javascript
type Props = {
  children: ReactNode
} & (
  {
    type? : 'single'
    user: string
    users?: never
  }
  |
  {
    type : 'multi'
    user?: never
    users: Array<string>
  }
)

const UserLabel = ({ children, type, user, users }: Props) => {
  return (
    <>
      {children}
      {type === 'multi'
        ? users.map(user => (
            <label>user</label>
          )) : <label>user</label>}
    </>
  )
}
```

参考にしたサイトからちょっと工夫して
typeを省略した場合は`single`とみなすようにしています。

呼び出し方はこちら
```Javascript
// singleの場合
  <UserLabel type='single' user={'taro'} >
    <span>single</span>
  </UserLabel>
  // もしくは
  <UserLabel user={'taro'} >
    <span>single</span>
  </UserLabel>
// multiの場合
  <Header type='multi' users={['taro', 'jiro']} >
    <span>multi</span>
  </UserLabel>
```

## まとめ

上記のように「&」と「|」で条件をつなげて書くことで、引数を分岐することができ
Javascriptだけで書いていた時よりも引数を制限して間違って書いたときにエラーにすることができます。

どんどん利用していきたいと思います。


