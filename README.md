# Домашнее задание к занятию «Что такое DevOps. СI/СD» - Кузнецов Евгений Александрович

### Задание 1


**Что нужно сделать:**

1. Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
2. Установите на машину с jenkins [golang](https://golang.org/doc/install).
3. Используя свой аккаунт на GitHub, сделайте себе форк [репозитория](https://github.com/netology-code/sdvps-materials.git). В этом же репозитории находится [дополнительный материал для выполнения ДЗ](https://github.com/netology-code/sdvps-materials/blob/main/CICD/8.2-hw.md).
3. Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта ```go test .``` и  ```docker build .```.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

### Решение 1

Скриншот 1 к решению 1
![Screen 1](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_75.png)
Скриншот 2 к решению 1
![Screen 2](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_76.png)
Скриншот 3 к решению 1
![Screen 3](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_77.png)
Скриншот 4 к решению 1
![Screen 4](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_78.png)

---

### Задание 2

**Что нужно сделать:**

1. Создайте новый проект pipeline.
2. Перепишите сборку из задания 1 на declarative в виде кода.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

### Решение 2

Скриншот 1 к решению 2
![Screen 5](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_79.png)
Скриншот 2 к решению 2
![Screen 6](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_80.png)
Скриншот 3 к решению 2
![Screen 7](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_81.png)

Листинг кода к решению 2:
```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/jack34ru/sdvps-materials.git'}
  }
  stage('Test') {
   steps {
    sh '/usr/local/go/bin/go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t 192.168.0.33:8082/hello-world:v$BUILD_NUMBER'
   }
  }
  stage('Push') {
   steps {
    sh 'docker login 192.168.0.33:8082 -u admin -p admin && docker push 192.168.0.33:8082/hello-world:v$BUILD_NUMBER && docker logout'   }
  }
 }
}
```

---

### Задание 3

**Что нужно сделать:**

1. Установите на машину Nexus.
1. Создайте raw-hosted репозиторий.
1. Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
1. Загрузите файл в репозиторий с помощью jenkins.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

### Решение 3

Скриншот 1 к решению 3
![Screen 8](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_82.png)
Скриншот 2 к решению 3
![Screen 9](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_83.png)
Скриншот 3 к решению 3
![Screen 10](https://github.com/jack34ru/DevOps-CI-CD/blob/master/screenshots/Screenshot_84.png)

Листинг кода к решению 3:
```
pipeline {
    agent any
    stages {
        stage('Git') {
            steps {
                git 'https://github.com/jack34ru/sdvps-materials.git'
            }
        }
        stage('Test') {
            steps {
                sh '/usr/local/go/bin/go test .'
            }
        }
        stage('Build') {
            steps {
                sh 'CGO_ENABLED=0 GOOS=linux /usr/local/go/bin/go build -a -installsuffix nocgo -o build/hello-world:v$BUILD_NUMBER'
            }
        }
        stage('Push to Nexus') {
            steps {
                sh 'curl -u "admin:admin" --upload-file build/hello-world:v$BUILD_NUMBER http://192.168.0.33:8081/repository/raw_repository/hello-world:v$BUILD_NUMBER'
            }
        }
    }
}
```