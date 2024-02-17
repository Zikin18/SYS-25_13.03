# Домашнее задание к занятию «Защита сети» - Юрий Белобородов

### Подготовка к выполнению заданий

1. Подготовка защищаемой системы:

- установите **Suricata**,
- установите **Fail2Ban**.

2. Подготовка системы злоумышленника: установите **nmap** и **thc-hydra** либо скачайте и установите **Kali linux**.

Обе системы должны находится в одной подсети.


### Задание 1

Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:

**sudo nmap -sA < ip-адрес >**

**sudo nmap -sT < ip-адрес >**

**sudo nmap -sS < ip-адрес >**

**sudo nmap -sV < ip-адрес >**

По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html.

*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*

Ответ:

На атакуемой машине запускаем suricata

![1-1_start_suricata.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-1_start_suricata.png)

На kali запускаем сканирование через nmap

![1-2_nmap_ss.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-2_nmap_ss.png)

![1-3_nmap_sa.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-3_nmap_sa.png)

![1-4_nmap_st.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-4_nmap_st.png)

![1-5_nmap_sv.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-5_nmap_sv.png)

В случае сканов sS, sT и sV в логах suricata был отображены сообщение о нежелательном трафике и возможном сканировании.

![1-6_nmap_ss_suricata_log.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-6_nmap_ss_suricata_log.png)

![1-7_nmap_st_suricata_log.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-7_nmap_st_suricata_log.png)

![1-8_nmap_sv_suricata_log.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/1-8_nmap_sv_suricata_log.png)


### Задание 2

Проведите атаку на подбор пароля для службы SSH:

**hydra -L users.txt -P pass.txt < ip-адрес > ssh**

1. Настройка **hydra**: 
 
 - создайте два файла: **users.txt** и **pass.txt**;
 - в каждой строчке первого файла должны быть имена пользователей, второго — пароли. В нашем случае это могут быть случайные строки, но ради эксперимента можете добавить имя и пароль существующего пользователя.

Дополнительная информация по **hydra**: https://kali.tools/?p=1847.

2. Включение защиты SSH для Fail2Ban:

-  открыть файл /etc/fail2ban/jail.conf,
-  найти секцию **ssh**,
-  установить **enabled**  в **true**.

Дополнительная информация по **Fail2Ban**:https://putty.org.ru/articles/fail2ban-ssh.html.

*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*

Ответ:

Для подбора логина и пароля ssh были созданы файлы users.txt и pass.txt по 10 записей в каждом. 

Запуск сканирования без включенного fail2ban

![2-1_hydra_not_secure.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/2-1_hydra_not_secure.png)

Как выдно логин и пароль были успешно подобраны.
Сканирование отобразилось в логах suricata.

![2-2_suricata_log.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/2-2_suricata_log.png)

Теперь включим fail2ban и попробуем снова подобрать логин и пароль.
`sudo systemctl start fail2ban`

![2-3_hydra_secure.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/2-3_hydra_secure.png)

Как видим подобрать логин и пароль не удалось несмотря на то что что они были в выборке.
Лог fail2ban:

![2-4_fail_to_ban_log_ban.png](https://github.com/Zikin18/SYS-25_13.03/blob/master/2-4_fail_to_ban_log_ban.png)

После нескольких неудачных попыток ip с которого был запущен процесс подбора пароля и логина был заблокирован.
