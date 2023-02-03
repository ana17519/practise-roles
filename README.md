**Домашнее задание к занятию "3. Использование Yandex Cloud"**

Данный плейбук предназначен для установки `Clickhouse`, `Vector`, `Lighthouse` на хосты, указанные в `inventory` файлах.

**group_vars**

| Variable              | Details                                                            |
|:----------------------|:-------------------------------------------------------------------|
| `clickhouse_version`  | версия `Clickhouse`                                                |
| `clickhouse_packages` | `RPM` пакеты `Clickhouse`, которые необходимо скачать              |
| `vector_url`          | URL адрес для скачивания `RPM` пакетов `Vector`                    |
| `vector_version`      | версия `Vector`                                                    |
| `vector_config`       | раздел, содержащий справку по всем параметрам конфигурации вектора |
| `lighthouse_url`      | ссылка на репозиторий `Lighthouse`                                 |
| `lighhouse_location_dir`       | директория для файлов                                              |
| `lighthouse_nginx_user`      | пользователь для  `nginx`                                            |

**Inventory файл**

Группа `clickhouse` состоит из хоста `clickhouse-01` и метод коннекта `ansible_host`.

Группа `vector` состоит из хоста `vector-01` и метод коннекта `ansible_host`.

Группа `lighthouse` состоит из хоста `lighthouse-01` и метод коннекта `ansible_host`.

**Playbook**

Playbook состоит из 4 `play`.

Play `Install nginx` применяется на группу `lighthouse` и предназначен для установки и запуска `nginx`.

remote_user для коннекта: `remote_user: ana`

`handler` для старта и перезагрузки `nginx`:

```
  handlers:
    - name: Start nginx
      become: true
      ansible.builtin.command: nginx
    - name: Reload nginx
      become: true
      ansible.builtin.service:
        name: nginx
        state: restarted
```

| Prefix     |  Task | Details                                            |
|-----------|-------------------------|----------------------------------------------------|
| `NGINX`   | `Install epel-release`   | Установка релизного репозитория                    |                                                                          |
| `NGINX`   | `Install nginx`         | Установка `nginx`, используя `yum`                 |
| `NGINX`   | `Create general config` | Создание конфига на основе шаблона `nginx.conf.j2` |

Play `Install lighthouse` применяется на группу `lighthouse` и предназначен для установки и запуска `lighthouse`.

remote_user для коннекта: `remote_user: ana`

`handler` для перезагрузки `lighthouse`:

```
  handlers:
    - name: Reload nginx
      become: true
      ansible.builtin.service:
        name: nginx
        state: restarted
```

| Prefix |  Task | Details                                                                             |
|-----------------|------------------------|-------------------------------------------------------------------------------------|
| `Lighthouse`    | `Install dependencies` | Установка зависимостей: git                                                         |                                                                          |
| `Lighthouse`         | `Copy from git`        | Сканчивание модуля по url с использованием git и установка в место, указанное в dest |
| `Lighthouse`         | `Create lighthouse config` | Создание конфига на основе шаблона `lighthouse.conf.j2`                             |

Play `Install Clickhouse` применяется на группу `clickhouse` и предназначен для установки и запуска `Clickhouse`.

remote_user для коннекта: `remote_user: ana`

`handler` для запуска `clickhouse-server`:

```
  handlers:
    - name: Start clickhouse service
      become: true
      ansible.builtin.service:
        name: clickhouse-server
        state: restarted
```

| Prefix       |  Task                                   | Details                                                                                                                                                                                                                                          |
|--------------|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Clickhouse` | `Get clickhouse distrib`      | Скачивание `RPM` пакетов, используется `clickhouse_packages` с указанием пакетов. Так как не у всех пакетов есть `noarch` версии, добавляется перехват ошибки `rescue`                                                                           |
| `Clickhouse` | `Install packages` | Установка `RPM` пакетов, в `notify` указывается, что task требует запуск handler `Start clickhouse service`                                                                                                                                      |
| `Clickhouse` | `Flush  handlers`  | Добавляется обработчик `Start clickhouse service` для того, чтобы handler выполнился после install task, а не по завершению всех задач. Если пропустить этот этап, то задача создания таблиц завершится ошибкой, тк не запущен clickhouse-server |
| `Clickhouse` | `Create  database`            | Создаем в `Clickhouse` БД с названием "logs", добавляем условия `failed_when` и `changed_when`, при которых таск будет иметь состояние `failed` и `changed`                                                                                      |

Play `Install Vector` применяется на группу хостов `Vector` и нужен для установки и запуска `Vector`.

remote_user для коннекта: `remote_user: ana`

| Prefix | Task                                                                                                              | Details                                                                                                          |
|---|-------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `Vector` | `Install rpm`                                                                                                     | Скачивание `RPM` пакетов                                                                                         |
 | `Vector` | ` Template config`    | Применяется шаблон конфига vector, задается путь конфига, владелец - ansible, затем запуск валидации конфига                                                                                       |
 | `Vector` | `Create systemd unit`      | Изменяется модуль службы vector |
| `Vector` |  `Start service` | запуск  сервиса `Start Vector service`                                                                           |



**Templates**

Шаблон `vector.service.j2` нужен для изменения модуля службы `vector`. 
В нем определяется строка запуска `vector`и есть указание, что unit должен быть запущен под пользователем `ansible`.

Шаблон `vector.yml.j2` используется для настройки конфига `vector`. 
В нем указывается, что файл конфига находится в переменной vector_config.

Шаблон `nginx.conf.j2` используется для первичной настройки nginx, задается пользователь для nginx.

Шаблон `lighthouse.conf.j2` настраивает nginx на работу с lighthouse. В нем указывается порт 80, root директорию и index страницу.
