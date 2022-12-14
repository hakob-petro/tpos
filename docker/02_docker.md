# Домашнее задание №2. Docker

* **Мягкий deadline:** 13.11, 23:59.
* **Жёсткий deadline:** 13.11, 23:59.

**Основная идея:** необходимо написать docker-compose файл и дополнительные файлы, которые будут разворачивать набор из 3 связанных сервисов.    

## Подробное описание

С помощью docker-compose и Dockerfile необходимо создать 3 следующих сервиса, которые формируют полноценный веб-сервер:

### Внутренняя сеть

Сервисы должны находиться и общаться в одной Docker-сети, которую вы должны описать в docker-compose файле.

**Требования:**

- для базового потока - драйвер сети **bridge**
- для продвинутого потока - драйвер сети **macvlan**

### Сервис-1. База данных

Контейнер для хранения данных. 
Какая именно будет использована БД - неважно, можно использовать готовый образ. 

**Требования:**
- хранить данные, необходимые для работы веб-приложения. Данные представляют собой два столбца: 
  - `name` (строковый)
  - `age` (целочисленный)
- можно использовать готовый контейнер из https://hub.docker.com/.

### Сервис-2. Скрипт для заполнения БД

Контейнер со скриптом для наполнения базы данных. 
Сервис запускается, подключается к БД по сети и заполняет ее набором данных.

**Примечания:**

- можете писать скрипт на любом языке, советуем Python или Bash
- нужно написать Dockerfile, с помощью которого можно собрать образ для контейнера Сервиса-2 

**Требования:**
- Набор данных представлен CSV-файлом `data.csv` с двумя колонками с заголовком
- Сервис-2 стартует после старта Сервиса-1
- Сервис-2 останавливается после заполнения БД
- Сервис-2 выводит содержимое БД после ее заполнения в stdout (проверка корректности заполнения)

### Сервис-3. Веб-сервер 

Контейнер с веб-сервером, который может отдавать данные из БД по http-запросу.

**Примечания:**

- можете писать веб-сервер на любом языке и фреймворке, советуем Python и [Flask](https://flask.palletsprojects.com/en/2.2.x/quickstart/).
- нужно написать Dockerfile, с помощью которого можно собрать образ для контейнера Сервиса-2

**Требования:**
- веб-сервер должен запускаться после Сервиса-1
- веб-сервер соединяется с контейнером БД по сети, делает запрос, получает ответ и отдает данные в http ответе
- веб-сервер умеет принимать следующие http-запросы:
  - `GET /` - возвращает все данные из БД в формате JSON, статус 200
  - `GET /health` - возвращает JSON `{"status": "OK"}`, статус 200
  - остальные запросы - возвращает статус 404
- веб-сервер должен запускаться на порте 8000
- контейнер с веб-сервером должен иметь проброшенный порт `8000:8000` (во время тестов можете использовать выделенный вам порт, но при отправке замените его на 8000)

## Критерии оценивания

Домашка оценивается в 2 балла. Баллы (даже частичные выставляются только в том случае если система работает и отдает данные).

Далее описаны баллы за каждый сервис и штрафы за некорректное выполнение задания.

### Внутренняя сеть

**Штрафы:**
- **[-30% ко всем сервисам!]** Сеть для сервисов не создана / создана не того типа
- **[-20% к соотв. сервису]** Контейнер не подключен к созданной сети

### Сервис-1 (0.3 баллов)

**Штрафы:**
- **[-100%]** БД создается заполненной (данные появляются без участия Сервиса-2)

### Сервис-2 (0.7 баллов)

**Штрафы:**

- **[-100%]** Данные забиты хардкодом в скрипте или Dockerfile - нельзя поменять данные без пересборки образа Сервиса-2
- **[-50%]** Сервис-2 не останавливается после заполнения БД
- **[-50%]** CSV-файл с данными добавляется не с помощью монтирования Volume, а с помощью добавления файла в образ в Dockerfile через COPY / ADD
- **[-30%]** Сервис-2 не проверяет заполнение данных

### Сервис-3 (1.0 балл)

**Штрафы:**
- **[-100%]** Вместо вывода данных по сети, реализован вывод данных в stdout
- **[-20%]** Не реализована корректная работа с запросом `GET /`
- **[-20%]** Не реализована корректная работа с запросом `GET /health`
- **[-20%]** Не реализована корректная работа с остальными запросами

## Формат проверки

Проверяющий будет помещать рядом с вашим docker-compose.yaml собственный файл с данными `data.csv` и запускать ваши сервисы с помощью команды:

`docker-compose up -d`

После чего смотреть логи ваших контейнеров и выполнять http-запросы.

В вашем docker-compose файле необходимо прописать, что сервисы-2 и 3 должны быть собраны при вызове.

## Формат сдачи

Сдача происходит точно также, как и в предыдущем ДЗ. Вы заливаете код в ветку `hwdockertask1` (не мастер!) репозитория `***-hwdocker` в `gitlab.atp-fivt.org` и делаете Merge Request.
Assignee должен быть пустым (Unassigned)!
