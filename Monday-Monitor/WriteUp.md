
# WriteUp 'Monday Monitor'

*   **Author:** VIVI
*   **Link:** [Monday Monitor](https://tryhackme.com/room/mondaymonitor)

## 🧠 Analysis

Описание комнаты:
"Swiftspend Finance, the coolest fintech company in town, is on a mission to level up its cyber security game to keep those digital adversaries at bay and ensure their customers stay safe and sound.

Led by the tech-savvy Senior Security Engineer John Sterling, Swiftspend's latest project is about beefing up their endpoint monitoring using Wazuh and Sysmon. They've been running some tests to see how well their cyber guardians can sniff out trouble. And guess what? You're the cyber sleuth they've called in to crack the code!

The tests were run on Apr 29, 2024, between 12:00:00 and 20:00:00. As you dive into the logs, you'll look for any suspicious process shenanigans or weird network connections, you name it! Your mission? Unravel the mysteries within the logs and dish out some epic insights to fine-tune Swiftspend's defences."

7 вопросов на которые нужно найти ответы в Wazuh
## 🔎 Research and Answers

**Q1: Initial access was established using a downloaded file. What is the file name saved on the host?**

Для того чтобы найти искомый файл можно отфильтровать события по url и добавить столбец с параметром data.win.eventdata.commandLine
<img width="1915" height="978" alt="image" src="https://github.com/user-attachments/assets/cd43fd60-0afb-4a99-8372-5d6a9271cca9" />

В этом алерте мы и можем увидеть искомое название файла
<img width="1566" height="874" alt="image" src="https://github.com/user-attachments/assets/c758db07-532a-40b4-8804-230acd75f6fa" />

**Q2: What is the full command run to create a scheduled task?**

В поиск вбиваем scheduler и видим 10 событий два из которых выглядят нелигитмными
<img width="1916" height="949" alt="image" src="https://github.com/user-attachments/assets/9f992288-894b-4c79-b07b-83942744f1ca" />
<img width="1906" height="947" alt="image" src="https://github.com/user-attachments/assets/6b31c152-2a3a-40c4-bea5-9c420b1be504" />

**Q3: What time is the scheduled task meant to run?**

Вы ответе на предыдущий вопрос также содержится ответ и на этот

**Q4: What was encoded?**

Декодируем из base64 payload который был в запланированной задаче

<img width="1917" height="642" alt="image" src="https://github.com/user-attachments/assets/d41ab03c-af04-4dd9-a3bc-f6ab6a2fc0ca" />

**Q5: What password was set for the new user account?**

Фильтруем по ключевому слову net (net.exe) и находим событие с созданием нового пользователя guest и с его паролем
<img width="1556" height="120" alt="image" src="https://github.com/user-attachments/assets/7bf4be08-4362-4bd4-a2fd-a8f27efa13c1" />

**Q6: What is the name of the .exe that was used to dump credentials?**

Фильтр по слову mimikatz (приложение с открытым исходным кодом, которое позволяет пользователям просматривать и сохранять учетные данные аутентификации)
В событиях видим название exe файла под которым сохранен mimikatz

<img width="1916" height="950" alt="image" src="https://github.com/user-attachments/assets/534df444-a998-44fe-bd61-f14a6e5a49a4" />

**Q7: Data was exfiltrated from the host. What was the flag that was part of the data?**

Фильтруем по слову POST
Находим единственное событие которое содержит в себе флаг
<img width="1918" height="948" alt="image" src="https://github.com/user-attachments/assets/f7ffbca0-89d2-4b1e-bd53-d1b9a0e9a00d" />
