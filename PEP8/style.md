Для конвертации Markdown (`.md`) в HTML с красивыми стилями и подсветкой синтаксиса кода можно использовать библиотеку **`markdown`** вместе с **`Pygments`** для подсветки кода. Вот пошаговый пример:

---

### 1. Установи необходимые библиотеки
Для начала установи библиотеки:
```bash
pip install markdown Pygments
```

---

### 2. Код для конвертации Markdown в HTML с подсветкой синтаксиса
```python
import markdown
from pygments import highlight
from pygments.lexers import get_lexer_by_name
from pygments.formatters import HtmlFormatter

# Функция для подсветки синтаксиса кода
def highlight_code(code, language):
    try:
        lexer = get_lexer_by_name(language, stripall=True)
    except:
        lexer = get_lexer_by_name('text', stripall=True)  # Если язык не найден, используем текстовый
    formatter = HtmlFormatter(style='monokai', cssclass='codehilite')
    return highlight(code, lexer, formatter)

# Расширение для Markdown, чтобы обрабатывать блоки кода
class CodeBlockExtension(markdown.extensions.Extension):
    def extendMarkdown(self, md):
        md.registerExtension(self)
        md.preprocessors.register(CodeBlockPreprocessor(md), 'code_block', 25)

class CodeBlockPreprocessor(markdown.preprocessors.Preprocessor):
    def run(self, lines):
        new_lines = []
        in_code_block = False
        code_lines = []
        language = ''

        for line in lines:
            if line.startswith('```'):
                if in_code_block:
                    # Завершаем блок кода
                    highlighted_code = highlight_code('\n'.join(code_lines), language)
                    new_lines.append(highlighted_code)
                    in_code_block = False
                    code_lines = []
                else:
                    # Начинаем блок кода
                    in_code_block = True
                    language = line[3:].strip()  # Извлекаем язык программирования
            elif in_code_block:
                code_lines.append(line)
            else:
                new_lines.append(line)

        return new_lines

# Функция для конвертации Markdown в HTML
def md_to_html(md_file, html_file):
    # Чтение Markdown файла
    with open(md_file, 'r', encoding='utf-8') as f:
        md_text = f.read()

    # Преобразование Markdown в HTML с подсветкой кода
    extensions = [CodeBlockExtension(), 'fenced_code']
    html_text = markdown.markdown(md_text, extensions=extensions)

    # Добавление CSS-стилей для подсветки кода
    css_styles = HtmlFormatter(style='monokai').get_style_defs('.codehilite')

    # Создание полного HTML-документа
    html = f"""
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8">
        <title>Markdown to HTML</title>
        <style>
            body {{ 
                font-family: Arial, sans-serif; 
                line-height: 1.6; 
                margin: 40px; 
                color: #333; 
            }}
            h1, h2, h3 {{ 
                color: #2c3e50; 
            }}
            pre {{ 
                background: #272822; 
                padding: 10px; 
                border-radius: 5px; 
                overflow-x: auto; 
            }}
            {css_styles}
        </style>
    </head>
    <body>
        {html_text}
    </body>
    </html>
    """

    # Запись HTML в файл
    with open(html_file, 'w', encoding='utf-8') as f:
        f.write(html)

# Пример использования
md_to_html('example.md', 'output.html')
```

---

### 3. Как это работает:
1. **Подсветка синтаксиса**:
   - Используется библиотека `Pygments` для подсветки кода. Она поддерживает множество языков программирования.
   - Блоки кода в Markdown (обернутые в ```) обрабатываются отдельно.

2. **Расширение Markdown**:
   - Создано кастомное расширение для обработки блоков кода.

3. **CSS-стили**:
   - Добавлены стили для красивого отображения текста и подсветки кода (используется стиль `monokai`).

4. **Генерация HTML**:
   - Markdown преобразуется в HTML, и добавляются стили для красивого отображения.

---

### 4. Пример Markdown файла (`example.md`):
```markdown
# Заголовок 1
Это пример Markdown файла.

## Заголовок 2
- Пункт 1
- Пункт 2
- Пункт 3

### Заголовок 3
Пример кода на Python:

```python
def hello_world():
    print("Hello, World!")
```

Пример кода на JavaScript:

```javascript
function helloWorld() {
    console.log("Hello, World!");
}
```
```

---

### 5. Запуск программы
1. Сохрани код в файл, например, `md_to_html.py`.
2. Создай Markdown файл (`example.md`) с содержимым, как в примере выше.
3. Запусти скрипт:
   ```bash
   python md_to_html.py
   ```
4. В результате будет создан файл `output.html`, который можно открыть в браузере.

---

### 6. Пример вывода (`output.html`):
- Заголовки, списки и текст будут стилизованы.
- Блоки кода будут подсвечены в стиле `monokai`.

---

### 7. Дополнительные настройки
- **Стили Pygments**:
  Можно выбрать другой стиль подсветки кода, заменив `monokai` на другой, например, `default`, `friendly`, `tango` и т.д.:
  ```python
  formatter = HtmlFormatter(style='friendly', cssclass='codehilite')
  ```

- **Дополнительные CSS**:
  Если нужно добавить больше стилей, отредактируй CSS в HTML-шаблоне.

---

Библиотека **Pygments** поддерживает множество стилей для подсветки синтаксиса. Каждый стиль имеет уникальное название, которое можно указать в коде. Вот список доступных стилей:

---

### Список стилей Pygments:
1. **default** — Стандартный стиль.
2. **monokai** — Стиль, вдохновленный Monokai (популярная тема для редакторов кода).
3. **manni** — Стиль от автора Pygments.
4. **perldoc** — Стиль, похожий на документацию Perl.
5. **borland** — Стиль, вдохновленный IDE Borland.
6. **colorful** — Яркий и красочный стиль.
7. **murphy** — Стиль с темным фоном.
8. **vs** — Стиль, похожий на Visual Studio.
9. **trac** — Стиль, вдохновленный Trac.
10. **tango** — Стиль, основанный на Tango Desktop Project.
11. **fruity** — Яркий и "фруктовый" стиль.
12. **autumn** — Стиль с теплыми осенними цветами.
13. **bw** — Черно-белый стиль.
14. **emacs** — Стиль, вдохновленный Emacs.
15. **vim** — Стиль, вдохновленный Vim.
16. **pastie** — Стиль, похожий на Pastebin.
17. **friendly** — Упрощенный и дружелюбный стиль.
18. **native** — Стиль, вдохновленный IDLE (Python IDE).

---

### Как указать стиль в коде
В коде стиль указывается при создании объекта `HtmlFormatter`. Например:

```python
from pygments.formatters import HtmlFormatter

# Указываем стиль 'monokai'
formatter = HtmlFormatter(style='monokai', cssclass='codehilite')
```

---

### Пример использования разных стилей
Вот пример, как можно динамически выбирать стиль:

```python
from pygments.formatters import HtmlFormatter

# Список доступных стилей
styles = [
    'default', 'monokai', 'manni', 'perldoc', 'borland', 'colorful',
    'murphy', 'vs', 'trac', 'tango', 'fruity', 'autumn', 'bw', 'emacs',
    'vim', 'pastie', 'friendly', 'native'
]

# Выбираем стиль (например, 'monokai')
selected_style = 'monokai'

# Создаем форматтер с выбранным стилем
formatter = HtmlFormatter(style=selected_style, cssclass='codehilite')

# Получаем CSS для выбранного стиля
css_styles = formatter.get_style_defs('.codehilite')
print(css_styles)  # Выводим CSS
```

---

### Как добавить стиль в HTML
CSS-стили, сгенерированные Pygments, можно добавить в HTML-документ. Например:

```python
html = f"""
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Markdown to HTML</title>
    <style>
        body {{ 
            font-family: Arial, sans-serif; 
            line-height: 1.6; 
            margin: 40px; 
            color: #333; 
        }}
        h1, h2, h3 {{ 
            color: #2c3e50; 
        }}
        pre {{ 
            background: #272822; 
            padding: 10px; 
            border-radius: 5px; 
            overflow-x: auto; 
        }}
        {css_styles}  <!-- Добавляем стили Pygments -->
    </style>
</head>
<body>
    {html_text}
</body>
</html>
"""
```

---

### Как выбрать стиль
- Если ты хочешь темную тему, попробуй **monokai** или **murphy**.
- Если нужна светлая тема, подойдут **friendly**, **tango** или **default**.
- Для минимализма используй **bw** (черно-белый).

---

### Пример вывода CSS для стиля `monokai`
```css
.codehilite .hll { background-color: #49483e }
.codehilite  { background: #272822; color: #f8f8f2 }
.codehilite .c { color: #75715e } /* Comment */
.codehilite .err { color: #960050; background-color: #1e0010 } /* Error */
.codehilite .k { color: #66d9ef } /* Keyword */
.codehilite .l { color: #ae81ff } /* Literal */
.codehilite .n { color: #f8f8f2 } /* Name */
.codehilite .o { color: #f92672 } /* Operator */
.codehilite .p { color: #f8f8f2 } /* Punctuation */
.codehilite .cm { color: #75715e } /* Comment.Multiline */
.codehilite .cp { color: #75715e } /* Comment.Preproc */
.codehilite .c1 { color: #75715e } /* Comment.Single */
.codehilite .cs { color: #75715e } /* Comment.Special */
...
```

---

