### Домашнее задание к занятию «Использование Terraform в команде».
### Цели задания:
1.Научиться использовать remote state с блокировками.

2.Освоить приёмы командной работы.

### Чек-лист готовности к домашнему заданию.
1.Зарегистрирован аккаунт в Yandex Cloud. Использован промокод на грант.

2.Установлен инструмент Yandex CLI.

3.Любые ВМ, использованные при выполнении задания, должны быть прерываемыми, для экономии средств.

### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
Убедитесь что ваша версия Terraform ~>1.12.0 Пишем красивый код, хардкод значения не допустимы!

### Задание 0.
1.Прочтите статью: https://neprivet.com/

2.Пожалуйста, распространите данную идею в своем коллективе.

### Задание 1.
1.Возьмите код:

из ДЗ к лекции 4,

из демо к лекции 4.

2.Проверьте код с помощью tflint и checkov. Вам не нужно инициализировать этот проект.

3.Перечислите, какие типы ошибок обнаружены в проекте (без дублей).

### Ответ.
### tflint

1 version constraints
нет или неполные ограничения
terraform required_version
required_providers с version

2 неиспользуемые объявления
variables
locals
outputs
data
resources

3 некорректные ссылки
на несуществующие variables
на отсутствующие атрибуты ресурсов

4 устаревший синтаксис
лишняя интерполяция "${var.x}" вместо var.x
deprecated аргументы провайдеров
устаревшие блоки и конструкции terraform

5 нейминг
невалидные имена ресурсов и переменных
конфликты имён
нарушение принятого нейминга

6 типы
несоответствие типов
string вместо number и наоборот
ошибочные значения аргументов.

### checkov

1 security groups
слишком широкие правила 0.0.0.0/0
inbound и outbound
особенно порты 22 80 443

2 шифрование
ebs volume encrypted = false
отсутствует шифрование у связанных ресурсов

3 s3 защита
public access block не включён
bucket потенциально публичный

4 логирование
нет access logging для s3
нет логирования там где ожидается

5 теги
отсутствуют обязательные tags
нет метаданных ресурсов

6 доступ из интернета
инстанс доступен извне без ограничений
ssh без source cidr
нет дополнительных мер защиты

### Задание 2.
1.Возьмите ваш GitHub-репозиторий с выполненным ДЗ 4 в ветке 'terraform-04' и сделайте из него ветку 'terraform-05'.

2.Настройте remote state с встроенными блокировками:

Создайте S3 bucket в Yandex Cloud для хранения state (если еще не создан)

Создайте service account с правами на чтение/запись в bucket

Настройте backend в providers.tf с использованием нового механизма блокировок:
terraform {
  required_version = "~>1.12.0"
  
  backend "s3" {
    bucket  = "ваш-bucket-name"
    key     = "terraform.tfstate"
    region  = "ru-central1"
    
    # Встроенный механизм блокировок (Terraform >= 1.6)
    # Не требует отдельной базы данных!
    use_lockfile = true
    
    endpoints = {
      s3 = "https://storage.yandexcloud.net"
    }
    
    skip_region_validation      = true
    skip_credentials_validation = true
    skip_requesting_account_id  = true
    skip_s3_checksum            = true
  }
}

Выполните terraform init -migrate-state для миграции state в S3

Предоставьте скриншоты процесса настройки и миграции

3.Закоммитьте в ветку 'terraform-05' все изменения.

4.Откройте в проекте terraform console, а в другом окне из этой же директории попробуйте запустить terraform apply.

5.Пришлите ответ об ошибке доступа к state (блокировка должна сработать автоматически).

6.Принудительно разблокируйте state командой terraform force-unlock <LOCK_ID>. Пришлите команду и вывод.

### Примечание: В Terraform >= 1.6 появился встроенный механизм блокировок через use_lockfile = true. Это упрощает настройку - больше не нужно создавать отдельную базу данных (YDB в режиме DynamoDB) для хранения блокировок. Lock-файл создается автоматически в том же S3 bucket рядом с state-файлом с именем <key>.lock.info.

### Ответ.

https://github.com/neD555/ter-homeworks-04-hw-04.md/tree/terraform-05
<img width="503" height="444" alt="Задание 2(1)" src="https://github.com/user-attachments/assets/d8f12c85-d739-4a9e-8932-247355ae1e19" />
<img width="518" height="665" alt="Задание 2(2)" src="https://github.com/user-attachments/assets/49752084-8420-4ce3-8806-0622726ff678" />
<img width="516" height="489" alt="Задание  2(3)" src="https://github.com/user-attachments/assets/238d9cf5-0916-40c7-a197-519d5acaff93" />
<img width="515" height="268" alt="Задание 2(4)" src="https://github.com/user-attachments/assets/5448ab9c-6f61-4cee-8802-b88d1b6254c3" />

### Задание 3.
1.Сделайте в GitHub из ветки 'terraform-05' новую ветку 'terraform-hotfix'.

2.Проверье код с помощью tflint и checkov, исправьте все предупреждения и ошибки в 'terraform-hotfix', сделайте коммит.

3.Откройте новый pull request 'terraform-hotfix' --> 'terraform-05'.

4.Вставьте в комментарий PR результат анализа tflint и checkov, план изменений инфраструктуры из вывода команды terraform plan.

5.Пришлите ссылку на PR для ревью. Вливать код в 'terraform-05' не нужно.

### Ответ.
https://github.com/neD555/ter-homeworks-04-hw-04.md/pull/1






