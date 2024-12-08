# Ведение логов: rsyslog

#### <a name='toc'>Содержание</a>

1. [Что такое Rsyslog?](#1)
2. [Как происходит логирование?](#2)
3. [Основные лог файлы](#3)
4. [Настройка сервера](#4)
5. [Настройка клиента](#5)
6. [Чтение логов на сервере](#6)
7. [Дополнительные источники](#recommended_sources)


## [[⬆]](#toc) <a name='1'>Что такое Rsyslog?</a>

![image](https://github.com/user-attachments/assets/cc27ddbd-1e9f-49aa-9b32-379790fde162)

Развитие rsyslog началось в 2004 году, в качестве форка используемого тогда сервиса Syslog. Программа очень быстро набрала популярность среди пользователей и сейчас она поставляется по умолчанию во многих дистрибутивах Linux.

Rsyslog - это очень быстрый, расширяемый сервис для управления логами с огромным количеством возможностей. Среди его возможностей можно отметить поддержку фильтрации контента, а также передачу логов по сетям. Разработчики утверждают, что система очень быстрая, программа может обрабатывать до миллиона сообщений в секунду.


## [[⬆]](#toc) <a name='2'>Как происходит логирование?</a>

Все программы Linux ведут лог путем отправки сообщений об ошибках или своем состоянии с помощью сокета syslog или просто записывая все сообщения в файл, который будет находиться в каталоге /var/log/.

Но важное значение имеет уровень подробности логирования. Вы можете настраивать подробность в каждой отдельной программе, или же с помощью syslog. Это поможет уменьшить использование дискового пространства, на хранение логов. Но тут нужно найти компромисс между количеством информации и использованием диска.

Например, ядро Linux определяет такие уровни логов, или как мы будем называть их ниже - приоритеты:

- **KERN_EMERG** - система неработоспособна;
- **KERN_ALERT** - нужно немедленно принять меры;
- **KERN_CRIT** - критическая ошибка;
- **KERN_ERR** - обычная ошибка;
- **KERN_WARNING** - предупреждение;
- **KERN_NOTICE** - замечание;
- **KERN_INFO** - информационное сообщение;
- **KERN_DEBUG** - сообщения отладки.

Подобные уровни лога поддерживаются в большинстве программ, которые ведут логи.


## [[⬆]](#toc) <a name='3'>Основные лог файлы</a>

| Путь | Описание |
| ------- | ----------- |
| `/var/log/syslog или /var/log/messages` | содержит глобальный системный журнал, в котором пишутся сообщения с момента запуска системы, от ядра Linux, различных служб, обнаруженных устройствах, сетевых интерфейсов и много другого.|
| `/var/log/auth.log или /var/log/secure` | информация об авторизации пользователей, включая удачные и неудачные попытки входа в систему, а также задействованные механизмы аутентификации. |
| `/var/log/dmesg` | драйвера устройств. Одноименной командой можно просмотреть вывод содержимого файла. Размер журнала ограничен, когда файл достигнет своего предела, старые сообщения будут перезаписаны более новыми. Задав ключ --level= можно отфильтровать вывод по критерию значимости. |
| `/usr/lib/systemd/system` | юниты/модули сервисов, установленных с помощью менеджера пакетов. Самый простой пример — веб-серверы: Apache или Nginx. |
| `/var/log/alternatives.log` | Вывод программы update-alternatives, в котором находятся символические ссылки на команды или библиотеки по умолчанию. |
| `/var/log/anaconda.log` | Записи, зарегистрированные во время установки системы. |
| `/var/log/audit` | Записи, созданные службой аудита auditd. |
| `/var/log/boot.log` | Информация, которая пишется при загрузке операционной системы. |
| `/var/log/cron` | Отчет службы crond об исполняемых командах и сообщения от самих команд. |
| `/var/log/cups` | Все, что связано с печатью и принтерами. |
| `/var/log/kern.log` | Журнал содержит сообщения от ядра и предупреждения, которые могут быть полезны при устранении ошибок пользовательских модулей встроенных в ядро. |
| `/var/log/maillog/ или /var/log/mail.log` | Журнал почтового сервера, используемого на ОС. |
| `/var/log/pm-powersave.log` | Сообщения службы экономии заряда батареи. |
| `/var/log/samba/` | Логи файлового сервера Samba, который используется для доступа к общим папкам Windows и предоставления доступа пользователям Windows к общим папкам Linux. |
| `/var/log/spooler` | Для представителей старой школы, содержит сообщения USENET. Чаще всего бывает пустым и заброшенным. |
| `/var/log/Xorg.0.log` | Логи X сервера. Чаще всего бесполезны, но если в них есть строки начинающиеся с EE, то следует обратить на них внимание |
| `/var/log/yum.log` | Для программ установленных с помощью Yum в RedHat Linux. |
| `/var/log/emerge.log` | Для ebuild-ов установленных из Portage с помощью emerge в Gentoo Linux. |
| `/var/log/dpkg.log` | Для программ установленных с помощью dpkg в Debian Linux и всем семействе родственных дистрибутивах. |
| `/var/log/lastlog` | Последняя сессия пользователей. Прочитать можно командой last. |
| `/var/log/tallylog` | Аудит неудачных попыток входа в систему. Вывод на экран с помощью утилиты pam_tally2. |
| `/var/log/btmp` | Еже один журнал записи неудачных попыток входа в систему. Просто так, на всякий случай, если вы еще не догадались где следует искать следы активности взломщиков. |
| `/var/log/wtmp` | Еще один журнал записи входа пользователей в систему. Вывод на экран командой utmpdump. |
| `/var/log/mysql/` | Лог базы данных MySQL. |
| `/var/log/httpd/ или /var/log/apache2/` | Лог веб сервера Apache, журнал доступа находится в access_log, а ошибки — в error_log. |
| `/var/log/lighthttpd/` | Лог веб сервера lighttpd. |
| `/var/log/nginx/` | Лог веб сервера nginx |



Предварительная настройка 

![image](https://github.com/user-attachments/assets/29c1e8fd-5c98-45c7-aa0c-a5513ce31200)

![image](https://github.com/user-attachments/assets/26dd1f58-01ad-4e9c-b5db-a2b8ff23ad41)



## [[⬆]](#toc) <a name='4'>Настройка сервера</a>

## [[⬆]](#toc) <a name='5'>Настройка клиента</a>

## [[⬆]](#toc) <a name='6'>Чтение логов на сервере</a>













#### 5. [[⬆]](#toc) <a name='recommended_sources'>Дополнительные источники</a>

1. [Rsyslog - Википедия](https://en.wikipedia.org/wiki/Rsyslog)
2. [Настройка rsyslog для хранения логов](https://www.dmosk.ru/miniinstruktions.php?mini=rsyslog)
3. [Настройка Rsyslog в Linux](https://losst.pro/nastrojka-rsyslog-v-linux)
4. [&&&&&&&&&&&&&&&&&&&&&&&&&](https://pikabu.ru/story/sistema_initsializatsii_5191339)
5. &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
