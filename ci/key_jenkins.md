---
layout: default
title: Work with ssh keys for GIT
---

### Генерація закритого і відкритого ключів для Git репозитарію

```
ssh-keygen -t rsa -b 4096 -C "Key for Jenkins"
```

Після запиту:
```
Enter file in which to save the key
```
вказуємо шлях і назву файлу ключа (наприклад, id_rsa_jenkins)

Після запиту паролю потрібно ввести і підтвердити пароль:

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Після цього буде згенеровано і збережено файли з відкритим (з розширенням .pub) і приватним ключами (без розширення).

### Добавлення ключів до Bitbucket-у

1. Заходимо в Settings - Access keys:
  1. "Add key"
  2. Вибираємо поравильні Permission (при потребі потім можна змінити)
  3. В поле Key(required) вставляємо вміст файлу id_rsa_jenkins.pub (відкритий ключ)
  4. Нажимаємо кнопку "Add key"

### Добавлення ключа в jenkins

Вибираємо Credentials - Add Credentials

Вводимо наступні значення:


| Kind | SSH Username with private key |
| Scope | Global (Jenkins, nodes, items, all child items, etc) |
| Username | наприклад, git_repository_credentials |
| Private Key | вміст файлу id_rsa_jenkins (закритий ключ) |
| Passphrase | пароль, який задавали при генерації ключа |
| ID | jenkins_repository |
| Description | Пишемо що хочемо |

Нажимаємо "Save"

