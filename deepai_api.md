# ChatGPT de DeepAI mediante su API

## Receta General

Receta general para utilizar la API de [Deepai.org](https://deepai.org/chat) desde una laptop con Fedora 39 y enviar archivos para su análisis es la siguiente:

### 1. **Instalar Python y pip**

Primero, verifica si tienes Python y pip instalados en tu sistema. Abre una terminal y ejecuta:

```bash
python3 --version
pip3 --version
```

Si no están instalados, puedes instalarlos con el siguiente comando:

```bash
sudo dnf install python3 python3-pip
```

### 2. **Instalar las Bibliotecas Necesarias**

A continuación, asegúrate de tener la biblioteca `requests` instalada, que es comúnmente utilizada para enviar solicitudes HTTP:

```bash
pip3 install requests
```

### 3. **Obtener tu clave API de DeepAI**

- Ve a [DeepAI](https://deepai.org/) y regístrate o inicia sesión en tu cuenta.
- Navega a la sección de API en tu perfil para obtener tu clave API (API Key).

### 4. **Crear un Script de Python para Enviar Archivos**

Crea un archivo Python que contendrá el código para enviar un archivo a la API de DeepAI. Abre una terminal y usa tu editor de texto favorito (como `nano`, `vim` o `gedit`) para crear un nuevo archivo:

```bash
nano deepai_api_example.py
```

Luego, copia y pega el siguiente código, asegurándote de reemplazar `"YOUR_API_KEY"` con tu clave API:

```python
import requests

api_key = 'YOUR_API_KEY'
url = 'https://api.deepai.org/api/your-analysis-endpoint'  # Cambia esto al endpoint que desees utilizar

# Ruta al archivo que deseas enviar
file_path = 'ruta/al/tu/archivo.ext'  # Cambia esto a la ruta de tu archivo

# Lectura del archivo en modo binario
with open(file_path, 'rb') as file:
    response = requests.post(
        url,
        headers={'api-key': api_key},
        files={'file': file}
    )

# Imprimir la respuesta
print(response.json())
```

Asegúrate de cambiar:
- `your-analysis-endpoint` por el endpoint correspondiente de la API que deseas utilizar (consulta la [documentación de DeepAI](https://deepai.org/docs) para obtener los endpoints disponibles).
- `ruta/al/tu/archivo.ext` por la ruta del archivo que deseas enviar.

### 5. **Ejecutar el Script**

Guarda el archivo y cierra el editor. Ahora puedes ejecutar el script en la terminal con el siguiente comando:

```bash
python3 deepai_api_example.py
```

### 6. **Revisar la Respuesta**

Después de ejecutar el script, revisa la salida en la terminal. Deberías ver la respuesta JSON de la API, que contendrá los resultados del análisis.

### Consejos Adicionales
- **Documentación:** Consulta la [documentación de la API de DeepAI](https://deepai.org/docs) para entender mejor los parámetros y los resultados que puedes recibir.
- **Manejo de Errores:** Considera añadir manejo de errores en tu script para situaciones en las que la solicitud falle.

¡Y eso es todo! Ahora puedes enviar archivos a la API de DeepAI desde tu laptop con Fedora 39. 

### Endpoint para la API de DeepAI (ChatGPT)

Para interactuar con deepAI como modelo de inteligencia artificial a través de un sistema de chat, puedes usar la API de DeepAI, que ofrece varios modelos como ChatGPT. El endpoint pertinente para las interacciones de chat es: `https://api.openai.com/v1/chat/completions`.

### Endpoint para la API de DeepAI (ChatGPT)


## Endpoint:
- **URL:** `https://api.openai.com/v1/chat/completions`
  
Este endpoint permite crear interacciones de chat con modelos de lenguaje como ChatGPT, donde puedes enviar mensajes y recibir respuestas en formato de chat.


## Aplicación Web

Para garantizar que la aplicación web construida con Flask funcione a prueba de errores, es esencial implementar manejo de excepciones y validaciones de entrada más robustas. A continuación, se presenta la aplicación web elemnetal, así como las instrucciones para crearla. 

### Estructura

La estructura general de la aplicación web es la siguiente:

1. **Separación de Responsabilidades**: Introduciremos un archivo de configuración y un módulo separado para manejar la interacción con la API.

2. **Configuración del reemplazo de la API**: Usaremos un archivo de configuración para la clave API y otros parámetros que pueden ser modificados sin cambiar el código.

3. **Validaciones Más Profundas**: Añadiremos más validaciones a las entradas del usuario para asegurarnos de que solo datos válidos sean enviados.

4. **Manejo de Errores Más Amigable**: En vez de simplemente mostrar los errores, proporcionaremos mensajes más específicos que mejoren la experiencia del usuario.

5. **Estilo Mejorado**: Se puede incluir algo de CSS básico directamente en la plantilla para una mejor apariencia.

### Código de la Aplicación Web

#### Estructura de Archivos

Primero, asegúrate de que tu estructura de directorios sea la siguiente:

```
/tu_proyecto
│
├── app.py
├── api_client.py
├── config.py
├── templates
│   └── index.html
└── uploads
```

#### Archivo `config.py`

Este archivo contendrá la configuración de la aplicación:

```python
import os

class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'your_default_secret_key')
    UPLOAD_FOLDER = 'uploads'
    ALLOWED_EXTENSIONS = {'txt', 'docx', 'xlsx', 'pptx', 'pdf'}
```

#### Archivo `api_client.py`

Este módulo manejará la interacción con la API:

```python
import requests

def send_to_api(api_key, user_input, file_content=""):
    url = 'https://api.openai.com/v1/chat/completions'

    data = {
        "model": "gpt-3.5-turbo",
        "messages": [
            {"role": "user", "content": user_input},
            {"role": "user", "content": file_content}
        ],
        "temperature": 0.7,
    }

    try:
        response = requests.post(url, json=data, headers={"Authorization": f"Bearer {api_key}"})

        if response.status_code == 200:
            reply = response.json()
            return reply['choices'][0]['message']['content']
        else:
            raise ValueError(f"Error: {response.status_code} - {response.text}")
    except requests.RequestException as e:
        raise ConnectionError(f"Ocurrió un error al conectar con la API: {e}")
```

#### Archivo `app.py`

Ahora, el **archivo principal** de la aplicación será más limpio y organizado:

```python
from flask import Flask, request, render_template, flash, redirect, url_for
import os
from werkzeug.utils import secure_filename
from docx import Document
import openpyxl
from pptx import Presentation
from PyPDF2 import PdfReader
from config import Config
from api_client import send_to_api

app = Flask(__name__)
app.config.from_object(Config)

# Función para verificar extensiones de archivos permitidas
def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1].lower() in app.config['ALLOWED_EXTENSIONS']

# Función para leer el contenido de diferentes tipos de archivos
def read_file(file_path):
    file_extension = os.path.splitext(file_path)[1].lower()
    content = ""

    try:
        if file_extension == '.txt':
            with open(file_path, 'r', encoding='utf-8') as file:
                content = file.read()
        elif file_extension == '.docx':
            doc = Document(file_path)
            content = '\n'.join([para.text for para in doc.paragraphs])
        elif file_extension == '.xlsx':
            wb = openpyxl.load_workbook(file_path)
            for sheet in wb.worksheets:
                for row in sheet.iter_rows(values_only=True):
                    content += ' '.join(map(str, row)) + '\n'
        elif file_extension == '.pptx':
            prs = Presentation(file_path)
            for slide in prs.slides:
                for shape in slide.shapes:
                    if hasattr(shape, "text"):
                        content += shape.text + '\n'
        elif file_extension == '.pdf':
            with open(file_path, 'rb') as file:
                reader = PdfReader(file)
                for page in reader.pages:
                    content += page.extract_text() + '\n'
    except Exception as e:
        raise ValueError(f"Error al leer el archivo: {e}")

    return content

@app.route('/', methods=['GET', 'POST'])
def index():
    result = ""
    
    if request.method == 'POST':
        api_key = request.form['api_key'].strip()
        user_input = request.form['user_input'].strip()
        file_content = ""

        if not api_key or not user_input:
            flash('Se requiere clave de API y texto de entrada.', 'error')
            return redirect(url_for('index'))

        if 'file' not in request.files:
            flash('No se encontró ningún archivo.', 'error')
            return redirect(url_for('index'))
        
        file = request.files['file']

        if file.filename == '':
            flash('No se seleccionó ningún archivo.', 'error')
            return redirect(url_for('index'))

        if file and allowed_file(file.filename):
            try:
                file_path = os.path.join(app.config['UPLOAD_FOLDER'], secure_filename(file.filename))
                file.save(file_path)
                file_content = read_file(file_path)
            except Exception as e:
                flash(str(e), 'error')
                return redirect(url_for('index'))
        else:
            flash('Archivo no permitido. Soportamos solo archivos .txt, .docx, .xlsx, .pptx, .pdf', 'error')
            return redirect(url_for('index'))

        try:
            result = send_to_api(api_key, user_input, file_content)
        except Exception as e:
            flash(str(e), 'error')
            return redirect(url_for('index'))

        flash('Solicitud enviada con éxito', 'success')

    return render_template('index.html', result=result)

if __name__ == '__main__':
    if not os.path.exists(app.config['UPLOAD_FOLDER']):
        os.makedirs(app.config['UPLOAD_FOLDER'])  # Crear directorio para almacenar archivos
    app.run(debug=True)
```

#### Archivo `index.html`

Vamos a incluir un poco de estilo básico en el HTML:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplicación Web de Envío de Texto y Archivos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        h1 {
            color: #4CAF50;
        }
        .error {
            color: red;
        }
        .success {
            color: green;
        }
        form {
            margin-top: 20px;
        }
        label, input, textarea {
            display: block;
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <h1>Envía Texto y Archivos a la API</h1>

    {% with messages = get_flashed_messages(with_categories=true) %}
      {% if messages %}
        <ul>
        {% for category, message in messages %}
          <li class="{{ category }}">{{ message }}</li>
        {% endfor %}
        </ul>
      {% endif %}
    {% endwith %}

    <form method="post" enctype="multipart/form-data">
        <label for="api_key">Clave de API:</label>
        <input type="text" id="api_key" name="api_key" required>

        <label for="user_input">Ingresa tu texto:</label>
        <textarea id="user_input" name="user_input" rows="4" cols="50"></textarea>

        <label for="file">Selecciona un archivo:</label>
        <input type="file" id="file" name="file" accept=".txt,.docx,.xlsx,.pptx,.pdf">

        <input type="submit" value="Enviar">
    </form>

    {% if result %}
        <h2>Respuesta de la API:</h2>
        <pre>{{ result }}</pre>
    {% endif %}
</body>
</html>
```

### Lista de Verificación

**1. Estructura del Directorio:**
- `/tu_proyecto`
  - `app.py` (archivo principal de la aplicación)
  - `api_client.py` (módulo para llamadas a la API)
  - `config.py` (configuraciones de la aplicación)
  - `/templates` (directorio para las plantillas HTML)
    - `index.html` (plantilla HTML de la aplicación)
  - `/uploads` (directorio para almacenar archivos subidos)

**2. Asegúrate de tener las siguientes bibliotecas instaladas:**
- Flask
- requests
- python-docx
- openpyxl
- python-pptx
- PyPDF2

Para instalarlas, ejecuta:
```bash
pip install Flask requests python-docx openpyxl python-pptx PyPDF2
```

**3. Archivo `config.py`:**
- Verifica que has definido correctamente `SECRET_KEY` y `ALLOWED_EXTENSIONS`.

**4. Ejecutar la aplicación:**
- Desde el terminal, navega al directorio de tu proyecto y ejecuta:
```bash
python app.py
```

### Notas Finales

Con estas mejoras y la nueva estructura modular, la aplicación web será más fácil de mantener y escalar. También proporcionará una experiencia más robusta para el usuario. 


## Pruebas de la Aplicación Web

A continuación, se desarrolla un caso de uso para realizar pruebas en la aplicación web que hemos construido. Este caso de uso incluye instrucciones detalladas y resultados esperados para asegurarte de que la aplicación funciona correctamente.

### 1. **Objetivo**
Verificar que la aplicación web funciona como se espera, incluyendo el proceso de subir un archivo, enviar datos a la API y recibir una respuesta adecuada.

### 2. **Precondiciones**
- La aplicación web debe estar instalada y configurada correctamente en tu entorno.
- Todas las dependencias mencionadas deben estar instaladas.
- La carpeta `uploads` debe existir y ser accesible.

### 3. **Instrucciones de Prueba**

#### Prueba 1: Verificar la Carga de la Página

1. **Acciones**: Abre tu navegador y navega a `http://127.0.0.1:5000/`.
2. **Resultado esperado**: La página de la aplicación web debe cargarse, mostrando el título "Aplicación Web de Envío de Texto y Archivos", junto con los campos para ingresar la clave de API, el texto y seleccionar un archivo.

#### Prueba 2: Enviar Texto y Archivo Válido

1. **Acciones**: 
   - Introduce una clave de API válida en el campo correspondiente.
   - Ingresa un texto (por ejemplo, "¿Cuáles son las implicaciones de la inteligencia artificial?").
   - Selecciona un archivo de texto (por ejemplo, un archivo `test.txt` que has creado previamente) que contenga algún contenido.
   
2. **Resultado esperado**:
   - Al hacer clic en "Enviar", la aplicación debe procesar el archivo y enviar el texto a la API.
   - La respuesta de la API se debe mostrar debajo del formulario con el encabezado "Respuesta de la API:".

#### Prueba 3: Verificar Respuesta de la API

1. **Acciones**: 
   - Repite la prueba anterior con diferentes archivos (por ejemplo, un archivo `.docx`, `.xlsx`, `.pptx`, o `.pdf` que contengan texto).
   
2. **Resultado esperado**: La aplicación debe procesar correctamente cada archivo y mostrar la respuesta desde la API. Si hay contenido en el archivo, este debe ser incluido en la solicitud a la API.

#### Prueba 4: Manejo de Archivos No Permitidos

1. **Acciones**: 
   - Ingresa una clave de API válida y un texto.
   - Selecciona un archivo no permitido (por ejemplo, un archivo de imagen `.jpg`).
   
2. **Resultado esperado**: 
   - Al hacer clic en "Enviar", la aplicación debe mostrar un mensaje de error que indica que el archivo no es permitido.

#### Prueba 5: Manejo de Campos Vacíos

1. **Acciones**: 
   - Deja el campo de la clave de API vacío y el campo de texto vacío.
   - Intenta enviar el formulario.
   
2. **Resultado esperado**: 
   - La aplicación debe mostrar un mensaje de error indicando que se requiere la clave de API y el texto de entrada.

#### Prueba 6: Prueba de Conexión Fallida a la API

1. **Acciones**: 
   - Para simular una falla de conexión, puedes cambiar la URL de la API en el código (por ejemplo, cambiarla a una URL que no exista).
   - Luego, intenta enviar un archivo válido y texto válido.
   
2. **Resultado esperado**: 
   - La aplicación debe capturar la excepción y mostrar un mensaje indicando que hubo un error al conectar con la API.

#### 4. **Postcondiciones**
- La aplicación debe manejar todas las entradas del usuario adecuadamente e informar sobre errores cuando sea necesario.
- Los archivos subidos deben permanecer en la carpeta `uploads` y ser accesibles después de la prueba si no fueron eliminados.

#### 5. **Resultados Finales Esperados**
Al finalizar estas pruebas, deberías observar lo siguiente:

- La aplicación web carga sin errores y muestra el formulario adecuadamente.
- Se pueden enviar textos y archivos válidos, y recibir respuestas de la API categoría "user".
- La aplicación maneja errores de manera adecuada, informando a los usuarios sobre entradas no válidas o problemas de conexión.
- Los mensajes de error son claros y ayudan a los usuarios a corregir sus acciones.

#### 6. **Registro de Resultados**
Lleva un registro de los resultados de tus pruebas para cada paso, anotando cualquier error o comportamiento inesperado. Esto servirá para verificar la funcionalidad completa de la aplicación y también para futuras mejoras.

Con este caso de uso, deberías estar en una buena posición para realizar una prueba exhaustiva de la aplicación y confirmar que funciona según lo esperado. Si encuentras algún problema durante las pruebas, es útil revisarlo para corregir errores o mejorar la aplicación. 
