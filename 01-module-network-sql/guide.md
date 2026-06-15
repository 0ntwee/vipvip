# Модуль 1. Настройка сетевого окружения, установка SQL-сервера
**Время:** 20 минут  
**Цель:** Настроить 4 сети в VMware, создать ВМ, установить SQL Server на Net1-Open

---

## Шаг 1. Создание сетей в VMware

1. На хост-машине (Alt Linux) открой VMware Workstation
2. **Edit → Virtual Network Editor** (нужны права root)
3. Создай 4 сети:

### VMnet1 — Сеть ЦО
- Type: **Host-only**
- Subnet IP: `172.16.224.224`
- Subnet mask: `255.255.255.224`
- DHCP: **Disable**

### VMnet2 — Сеть Филиала
- Type: **Host-only**
- Subnet IP: `10.10.20.128`
- Subnet mask: `255.255.255.128`
- DHCP: **Disable**

### VMnet3 — Межсетевая (Inet координаторов)
- Type: **Host-only**
- Subnet IP: `10.8.248.0`
- Subnet mask: `255.255.255.0`
- DHCP: **Disable**

### VMnet4 — Сеть Партнёра (для Модуля 6)
- Type: **Host-only** (или NAT)
- Subnet IP: `192.168.88.64`
- Subnet mask: `255.255.255.192`
- DHCP: **Disable**

📸 **Скриншот 1:** Virtual Network Editor со всеми сетями

---

## Шаг 2. Создание виртуальных машин

Создай ВМ с параметрами: Windows 10, 2 GB RAM, 40 GB HDD, 2 CPU

### Net1-Open (SQL-сервер)
- Network Adapter: **VMnet1** (Host-only)
- После установки Windows настрой:
  - IP: `172.16.224.226`
  - Mask: `255.255.255.224`
  - Gateway: `172.16.224.225`
  - DNS: можно оставить пустым
- **Отключи брандмауэр Windows** (Control Panel → Windows Defender Firewall → Turn off)
- Включи общий доступ к папкам (для подключения образов дисков)

📸 **Скриншот 2:** ipconfig /all на Net1-Open

---

## Шаг 3. Установка SQL Server на Net1-Open

1. Скопируй дистрибутив SQL Server на рабочий стол Net1-Open
2. Распакуй архив → автоматически откроется установщик
3. **Installation → New SQL Server stand-alone installation**
4. **Product Key:** введи если есть, или выбери Evaluation/Express
5. **License Terms:** Accept ✓
6. **Microsoft Update:** Skip (не проверяем обновления)
7. **Feature Selection:** оставь по умолчанию (Database Engine Services)
8. **Instance Configuration:** Default instance (MSSQLSERVER)
9. **Server Configuration:**
   - SQL Server Database Engine: `NT AUTHORITY\SYSTEM`
   - SQL Server Agent: `NT AUTHORITY\SYSTEM`
   - Все сервисы — **Automatic**
10. **Database Engine Configuration:**
    - Authentication Mode: **Mixed Mode**
    - **SA password:** `123qweR%` ⚠️ ЗАПОМНИ!
    - Add Current User ✓
11. **FILESTREAM** — ВКЛАДКА FILESTREAM:
    - ☑ Enable FILESTREAM for Transact-SQL access
    - ☑ Enable FILESTREAM for file I/O access
    -  Allow remote clients to have FILESTREAM access
    - ⚠️ **ВСЕ 3 ГАЛКИ ОБЯЗАТЕЛЬНЫ!** После установки изменить нельзя!
12. Install → жди завершения

📸 **Скриншот 3:** Окно FILESTREAM с 3 галками

---

## Шаг 4. Настройка SQL Server после установки

1. Открой **SQL Server Configuration Manager**
2. **SQL Server Network Configuration → Protocols for MSSQLSERVER**
3. Включи **TCP/IP** (правый клик → Enable)
4. Правый клик на TCP/IP → Properties → вкладка **IP Addresses**
5. Прокрути вниз до **IPAll**:
   - **TCP Port:** `49675` ️ ОБЯЗАТЕЛЬНО ЭТОТ ПОРТ!
   - TCP Dynamic Ports: очисти (пусто)
6. В секции **IP1** (или любом IP) найди свой IP `172.16.224.226` и укажи его в поле IP Address
7. **SQL Server Services:**
   - Перезапусти **SQL Server (MSSQLSERVER)**
   - **SQL Server Browser** → правый клик → Properties:
     - Service: Start Mode = **Automatic**
     - Вкладка Log On: **Local System account** ✓
   - Запусти SQL Server Browser
8. Открой порты в брандмауэре (или отключи брандмауэр полностью)

📸 **Скриншот 4:** TCP Port = 49675 в настройках

---

## Шаг 5. Проверка

1. Открой **SQL Server Management Studio (SSMS)**
2. Server name: `172.16.224.226` (или `localhost`)
3. Authentication: **SQL Server Authentication**
4. Login: `sa`
5. Password: `123qweR%`
6. Connect → выполни запрос: `SELECT @@VERSION`
7. Должна отобразиться версия SQL Server

 **Скриншот 5:** SSMS с результатом SELECT @@VERSION

---

## ✅ Чек-лист Модуля 1

- [ ] Созданы 4 сети в VMware
- [ ] Net1-Open имеет IP 172.16.224.226/27
- [ ] Брандмауэр на Net1-Open отключён
- [ ] SQL Server установлен
- [ ] FILESTREAM включён (3 галки)
- [ ] TCP порт = 49675
- [ ] SQL Server Browser запущен
- [ ] SSMS подключается к БД