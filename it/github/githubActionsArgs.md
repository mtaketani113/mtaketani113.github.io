---
layout: default
title: Pushの複数のbuild-args
description: Github ActionsでDocker HubへのPushで複数のbuild-argsを設定する際の方法について記載しまします。
category: github
---

Github Actionsで複数のbuild-argsを設定する際の方法について記載しまします。

記載した理由ですが、調べるのに少しかかったためです。あるサイトのはカンマ区切りで記載すると記載されていましたが、
それを実施してもうまくいかず、何度もコミットしなおしました。

## Gtihub ActionsでのDockerのPush

まずですが、Github ActionsをつかってTagを作った時にDocker hubに自動でPushするときには以下のように書きます。

```Yaml
name: Publish Docker image

on:
  push:
    branches-ignore:
      - '**'
    tags:
      - '*'

jobs:
  push_to_registry:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v2
      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v3
        with:
          images: mtaketani113/spring-boot-demo
      - name: Log in to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - name: Build and Push to Docker Hub
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

`secrets.XXX`というのは、GithubのSettingsで設定できます。

ここでは、`DOCKER_USERNAME`と`DOCKER_PASSWORD`を設定しています。
これがDocker hubのユーザ名とパスワードです。

## build-push-actionでArgを設定

DockerでARGを設定することがあると思います。
環境変数を渡したりする場合です。

その方法ですが、複数設定する場合、調べてるとカンマ区切りで実施していたのでそれでOKなのかとやってたのですが、うまくわたりませんでした。

どうも以下のように書かないといけないダメなようです。

```Yaml
          build-args: |
            GITHUB_ACTOR=${{ secrets.USER_NAME }}
            GITHUB_TOKEN=${{ secrets.ACCESS_TOKEN }}
```


全体だと以下のような感じです。

```Yaml
name: Publish Docker image

on:
  push:
    branches-ignore:
      - '**'
    tags:
      - '*'

jobs:
  push_to_registry:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v2
      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v3
        with:
          images: mtaketani113/spring-boot-demo
      - name: Log in to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - name: Build and Push to Docker Hub
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          build-args: |
            GITHUB_ACTOR=${{ secrets.USER_NAME }}
            GITHUB_TOKEN=${{ secrets.ACCESS_TOKEN }}
```

こちらのリンクに実際のコードを配置しています。
[0.0.12](https://github.com/mtaketani113/mybatisDemoForSpringboot3/blob/0.0.12/.github/workflows/docker.yaml)


## まとめ

build-argsですが、複数ある場合、カンマ区切りではうまく動きません。パイプを利用して複数記載しましょう。
