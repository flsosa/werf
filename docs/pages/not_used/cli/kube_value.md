---
title: werf kube value
sidebar: not_used
permalink: not_used/cli/kube_value.html
---
### werf kube value get

```
werf kube value get [options] VALUE_KEY REPO
```

Выводит значение переменных, устанавливаемых werf. Список некоторых переменных:
* `global.namespace` - namespace (см. опцию `--namespace`)
* `global.werf.name` - имя проекта werf (см. опцию `--name`)
* `global.werf.repo` - имя репозитория
* `global.werf.docker_tag` - тег образа
* `global.werf.dimg.[<имя dimg>.]docker_image` - docker-образа включая адрес репозитория и тег
* `global.werf.dimg.[<имя dimg>.]docker_image_id` - хэш образа, например - `sha256:cce87e0fe251a295a9ae81c8343b48472a74879cd75a0fbbd035bb50f69a2b02`, либо `-` если docker-registry не доступен
* `global.werf.ci.is_branch` - имеет значение `true`, в случае если происходит деплой из ветки (в случае использования werf только для deploy, будет `false`)
* `global.werf.ci.is_tag` - имеет значение `true`, в случае если происходит деплой из тега
* `global.werf.ci.tag` - имеет истинное значение только если `is_tag=true`, иначе `-`
* `global.werf.ci.branch` - имеет истинное значение только если is_branch=true, иначе `-`
* `global.werf.ci.ref` - тоже что tag или branch, - если `is_tag=false` и `is_branch=false`, то имеет значение `-`

Существует возможность получить `commit_id` для любого git-артефакта, который описан для dimg-образа. Чтобы такое значение появилось в helm-values, надо в config при описании git-артефакта указать директиву `as <name>`, тогда в helm появится значение: `global.werf.dimg.<имя dimg>.git.<имя, указанное в as>.commit_id`. Данный `commit_id` - это тот коммит, который соответствует состоянию git-артефакта в соответствующем dimg'е. Например:
```
dimg "rails" do
git "https://github.com/hello/world" do
  as "hello_world"
  ...
end
end
```
Получаем значение: `global.werf.dimg.rails.git.hello_world.commit_id=abcd1010`.

Примеры использования `werf kube value get`:

```
$ werf kube value get . :minikube
---
global:
  namespace: default
  werf:
    name: werf-test-submodules
    repo: localhost:5000/werf-test-submodules
    docker_tag: :latest
    ci:
      is_tag: false
      is_branch: true
      branch: master
      tag: \"-\"
      ref: master
    is_nameless_dimg: true
    dimg:
      docker_image: localhost:5000/werf-test-submodules:latest
      docker_image_id: sha256:cce87e0fe251a295a9ae81c8343b48472a74879cd75a0fbbd035bb50f69a2b02
```

```
$ werf kube value get global.werf.ci :minikube
---
is_tag: false
is_branch: true
branch: master
tag: \"-\"
ref: master
```