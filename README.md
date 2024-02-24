# Домашнее задание к занятию «Управление доступом»

### Цель задания

В тестовой среде Kubernetes нужно предоставить ограниченный доступ пользователю.

------

### Чеклист готовности к домашнему заданию

1. Установлено k8s-решение, например MicroK8S.
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым github-репозиторием.

------

### Инструменты / дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) RBAC.
2. [Пользователи и авторизация RBAC в Kubernetes](https://habr.com/ru/company/flant/blog/470503/).
3. [RBAC with Kubernetes in Minikube](https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b).

------

### Задание 1. Создайте конфигурацию для подключения пользователя

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для подключения.

<p align="center">
    <img width="1200 height="600" src="/img/ssl-config-arkadiy.png">
</p>

<p align="center">
    <img width="1200 height="600" src="/img/kube-config.png">
</p>

3. Создайте роли и все необходимые настройки для пользователя.
4. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (`kubectl logs pod <pod_id>`, `kubectl describe pod <pod_id>`).
5. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

| Deployment |
| :---: |
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: d-multitool
  labels:
    app: d-multitool
  namespace: my-ns
spec:
  replicas: 1
  selector:
    matchLabels:
      app: d-multitool
  template:
    metadata:
      labels:
        app: d-multitool
    spec:
      containers:
      - image: wbitt/network-multitool
        name: multitool
        imagePullPolicy: IfNotPresent
```

| Role |
| :---: |
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-role
  namespace: my-ns
rules:
  - apiGroups: [ "" ]
    resources: [ "pods", "pods/log", "pods/describe" ]
    verbs: [ "list", "gets", "watch" ]
```

| RoleBinding |
| :---: |
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-role-b
  namespace: my-ns
subjects:
  - kind: User
    name: arkadiy
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-role
  apiGroup: rbac.authorization.k8s.io
```

<p align="center">
    <img width="1200 height="600" src="/img/context-arkadiyk8s.png">
</p>


------

### Правила приёма работы

1. Домашняя работа оформляется в своём Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------

