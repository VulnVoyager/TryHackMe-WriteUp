
# WriteUp 'Friday Overtime'

*   **Author:** VIVI
*   **Link:** [Friday overtime](https://tryhackme.com/room/fridayovertime)

## 🧠 Analysis

Описание комнаты:
Step into the shoes of a Cyber Threat Intelligence Analyst and put your investigation skills to the test.

По более расширенному описанию в самой комнате становится ясно, что нам дадут вредоносные файлы, изучив которые мы должны ответить на ряд вопросов.

## 🔎 Research and Answers

**Q1: Who shared the malware samples?**

После старта и прогрузки машины, нужно открыть браузер, затем авторизоваться по выданным данным в CTI платформу, открыть письмо и найти отправителя письма:
<img width="1363" height="899" alt="image" src="https://github.com/user-attachments/assets/03d50e52-0d18-4c40-beac-bae0796c15f0" />

**Q2: What is the SHA1 hash of the file "pRsm.dll" inside samples.zip?**

Из письма находит приложенный архив samples.zip, скачиваем. В теле письма также указан пароль к архиву. Разорхивируем файлы, открываем косноль и пишем:
```
sha1sum pRsm.dll
```
и получаем ответ на вопрос
<img width="816" height="514" alt="image" src="https://github.com/user-attachments/assets/46686cbf-61f5-49b5-8e87-044882666c01" />

**Q3: Which malware framework utilizes these DLLs as add-on modules?**

Берем полученный хэш с прошлого задания и заходим на virustotal. Вбиваем хэш во вкладку search
По результатам поиска определяем фреймворк, отвечая тем самым на вопрос
<img width="873" height="409" alt="image" src="https://github.com/user-attachments/assets/6a20b4a2-e35a-4100-ba91-fba5782a0cde" />

**Q4: Which MITRE ATT&CK Technique is linked to using pRsm.dll in this malware framework?**

На той же странице virustotal заходим во вкладку community и смотрим коммент, там будет ссылка на источник
<img width="939" height="275" alt="image" src="https://github.com/user-attachments/assets/fbb2e77a-cd3d-4cb1-9c5a-d705fb732f04" />

После перехода в источник скроллим вниз и находим подходящую технику:
<img width="1363" height="749" alt="image" src="https://github.com/user-attachments/assets/f8384329-53da-40bd-94d2-65179807cf9c" />

**Q5: What is the CyberChef defanged URL of the malicious download location first seen on 2020-11-02?**

В том же источнике находим url:
<img width="1278" height="846" alt="image" src="https://github.com/user-attachments/assets/8672643b-fb97-4465-8fec-58dd0b28b353" />

следом прогоняем его в cyberchief:
<img width="1492" height="679" alt="image" src="https://github.com/user-attachments/assets/7570a13d-fa20-4544-aba4-d85e8ebeba4f" />

**Q6: What is the CyberChef defanged IP address of the C&C server first detected on 2020-09-14 using these modules?**

В этом же источнике находим ответ и на этот вопрос:
<img width="866" height="329" alt="image" src="https://github.com/user-attachments/assets/e9bc7c0c-0d14-4c1c-ad80-d8dd4bcfcab0" />

можно обойтись без использования кибер шефа,  просто точки заключить в скобки []

**Q7: What is the md5 hash of the spyagent family spyware hosted on the same IP targeting Android devices in Jun 2025?**

Вбиваем найденный ip адрес в virus total и находим во вкладке Relations искомый хэш
<img width="1798" height="719" alt="image" src="https://github.com/user-attachments/assets/70b2c5f8-bef0-4800-862d-2f8684b64b22" />

## 💡 Conclusions

Ранг Medium, не знаю почему, так как комната далась довольно легко, нужно лишь базовое представление о хэшах и уметь искать информацию в интернете

В целом на вечер поразмять мозг на минут 30-40 вполне себе неплохая комната
