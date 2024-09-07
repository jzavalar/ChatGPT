# API de OpenAI

## Script Elemental

Ejemplo de un script en Python que utiliza la API de OpenAI para procesar archivos de texto, por ejemplo, de transcripciones de audio. Este script envía el contenido de un archivo de texto a la API de OpenAI, recibe la respuesta, y permite realizar un análisis interactivo posterior.

### Paso 1: Instalar dependencias

Primero, asegúrate de que tienes instalada la biblioteca de OpenAI. Puedes instalarla usando pip:

```bash
pip install openai
```

### Paso 2: Configurar tu API Key

Antes de ejecutar el script, necesitarás configurar tu clave de API de OpenAI. Puedes obtener esta clave al registrarte en la plataforma de OpenAI.

### Paso 3: Script en Python

Esta versión del script contiene mensajes adicionales para el usuario que explican el nombre del script, su objetivo y el proceso a medida que avanza.

```python
import openai
import os

# Configura tu clave API de OpenAI
openai.api_key = 'TU_API_KEY_AQUI'

def mostrar_bienvenida():
    """Muestra un mensaje de bienvenida con el nombre y el objetivo del script."""
    print("-----------------------------------------------------")
    print("            Script de Análisis de Transcripción       ")
    print("-----------------------------------------------------")
    print("Este script permite analizar transcripciones de audio")
    print("mediante la API de OpenAI, facilitando un análisis ")
    print("interactivo posterior. El objetivo es procesar grandes")
    print("cantidades de texto y obtener respuestas útiles.")
    print("-----------------------------------------------------\n")

def leer_archivo(file_path):
    """Lee el contenido de un archivo de texto."""
    if not os.path.exists(file_path):
        print(f"Error: El archivo '{file_path}' no existe.")
        return None
    print(f"Leyendo el archivo: {file_path}...")
    with open(file_path, 'r', encoding='utf-8') as file:
        return file.read()

def procesar_texto_con_api(texto):
    """Envía el texto a la API de OpenAI para su procesamiento."""
    print("Enviando el texto a la API de OpenAI para análisis...")
    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",  # Puedes usar "gpt-3.5-turbo" o cualquier modelo compatible.
            messages=[
                {"role": "system", "content": "Eres un asistente que ayuda a analizar transcripciones."},
                {"role": "user", "content": texto}
            ],
            max_tokens=1500,  # Puedes ajustar este valor dependiendo de la longitud de la respuesta que necesitas.
            temperature=0.7  # Controla la creatividad de la respuesta.
        )
        print("Análisis completado con éxito.\n")
        return response['choices'][0]['message']['content']
    except Exception as e:
        return f"Error al procesar el texto: {str(e)}"

def main():
    mostrar_bienvenida()
    
    # Ruta al archivo de texto de la transcripción
    archivo_transcripcion = 'ruta/a/tu/transcripcion.txt'
    
    # Leer el contenido del archivo
    texto = leer_archivo(archivo_transcripcion)
    
    if texto:
        # Procesar el texto usando la API de OpenAI
        resultado = procesar_texto_con_api(texto)
        
        # Mostrar el resultado
        print("Resultado del análisis:")
        print(resultado)
    else:
        print("El archivo no se pudo leer. Por favor, verifica la ruta y vuelve a intentarlo.")

if __name__ == "__main__":
    main()
```

**Explicación**

1. **Función `mostrar_bienvenida()`**:
   - Esta función muestra un mensaje introductorio que incluye el nombre del script y su objetivo. Se llama al inicio del programa para informar al usuario sobre el propósito del script.

2. **Mensajes adicionales**:
   - Se han agregado mensajes que indican el progreso del script, como al leer el archivo, enviar el texto a la API, y al recibir la respuesta.

3. **Verificación de la existencia del archivo**:
   - Se añade una verificación para asegurarse de que el archivo existe antes de intentar leerlo, informando al usuario si hay un problema.

**Consideraciones Adicionales**

   - *Dividir el texto*: Si las transcripciones son muy largas, podrías necesitar dividir el archivo en partes más pequeñas para procesarlas de manera efectiva, dado que la API tiene un límite de tokens por solicitud.
   - *Interacción posterior*: El script actual simplemente muestra el resultado en la consola, pero podrías adaptarlo para interactuar más con la API, hacer preguntas adicionales o analizar respuestas en diferentes partes de la transcripción.

Con este script, podrás empezar a probar el procesamiento y análisis de tus transcripciones utilizando la API de OpenAI.


## Ejecución del Script

Este script proporciona una experiencia más informativa, guiando al usuario a través de cada paso del proceso en Fedora 39:

### Prerrequisitos

1. **Instalar Python**:
   Fedora 39 debería venir con Python ya instalado, pero puedes verificarlo y, si es necesario, instalarlo o actualizarlo usando el siguiente comando:
   ```bash
   python3 --version
   ```

   Si Python no está instalado, puedes instalarlo con:
   ```bash
   sudo dnf install python3
   ```

2. **Instalar `pip`**:
   `pip` es el gestor de paquetes de Python. Deberías tenerlo instalado con Python, pero si no es así, instálalo con:
   ```bash
   sudo dnf install python3-pip
   ```

3. **Instalar la biblioteca de OpenAI**:
   Utiliza `pip` para instalar la biblioteca `openai`:
   ```bash
   pip3 install openai
   ```

### Obtener la API Key

Para obtener tu API Key de OpenAI:

1. **Regístrate en OpenAI**:
   Si aún no tienes una cuenta, regístrate en [OpenAI](https://platform.openai.com/signup).

2. **Accede al Dashboard**:
   Una vez que hayas iniciado sesión, accede a tu [Dashboard de OpenAI](https://platform.openai.com/account/api-keys).

3. **Genera una nueva API Key**:
   Si no tienes una API Key, puedes crear una nueva desde el Dashboard. Copia la clave generada, ya que la necesitarás para el script.

### Ejecutar el Script

1. **Crear el archivo del script**:
   Guarda el script proporcionado en un archivo, por ejemplo `analizar_transcripcion.py`.

2. **Configurar el archivo del script**:
   Abre el archivo en un editor de texto y reemplaza `'TU_API_KEY_AQUI'` con tu API Key de OpenAI. Guarda los cambios.

3. **Preparar el archivo de transcripción**:
   Asegúrate de que el archivo de texto que deseas analizar esté disponible en la ruta especificada en el script. Por ejemplo, si el archivo se llama `transcripcion.txt` y está en el mismo directorio que el script, asegúrate de que la ruta en el script sea `'transcripcion.txt'`.

4. **Ejecutar el script**:
   Abre una terminal y navega al directorio donde guardaste el archivo del script. Ejecuta el script con:
   ```bash
   python3 analizar_transcripcion.py
   ```

### Ejemplo de Ejecución

Aquí tienes un resumen de los comandos en la terminal:

```bash
# Verificar la versión de Python
python3 --version

# Instalar pip si no está instalado
sudo dnf install python3-pip

# Instalar la biblioteca de OpenAI
pip3 install openai

# Ejecutar el script
python3 analizar_transcripcion.py
```

Siguiendo estos pasos, deberías poder ejecutar el script y analizar tus transcripciones usando la API de OpenAI.

### Caso de Uso: Análisis de la Transcripción de una Canción

Para probar el script con el archivo de texto `"Juan Gabriel – Siempre En Mi Mente_transcript.srt"`, a continuación te proporciono un caso de uso detallado:

#### 1. **Descripción del Escenario**
Tienes un archivo de subtítulos con la transcripción de la canción "Siempre En Mi Mente" de Juan Gabriel en formato `.srt`. Deseas analizar esta transcripción usando el script para obtener un resumen o interpretación del contenido y poder realizar un análisis interactivo posterior.

#### 2. **Preparación**

- **Archivo de transcripción**: Asegúrate de que el archivo de subtítulos `"Juan Gabriel – Siempre En Mi Mente_transcript.srt"` esté disponible y ubicado en una ruta accesible desde donde ejecutarás el script.
- **Ruta del archivo**: Por ejemplo, si el archivo está en el mismo directorio que el script, la ruta sería simplemente `"./Juan Gabriel – Siempre En Mi Mente_transcript.srt"`.

#### 3. **Modificación del Script**

Modificarás la línea en el script donde se define la ruta del archivo de transcripción:

```python
archivo_transcripcion = './Juan Gabriel – Siempre En Mi Mente_transcript.srt'
```

#### 4. **Ejecución del Script**

1. **Ejecuta el script**: Asegúrate de que el script tenga la API Key configurada y que el archivo de transcripción esté en la ruta especificada.

   ```bash
   python analizar_transcripcion.py
   ```

2. **Salida Esperada**:
   - El script mostrará un mensaje de bienvenida y luego leerá el archivo de transcripción.
   - Enviará el contenido a la API de OpenAI.
   - Finalmente, imprimirá en la consola el análisis realizado por la API, que podría incluir un resumen, interpretación o insights sobre la canción.

#### 5. **Ejemplo de Salida**

La salida en la consola podría ser algo como:

```
-----------------------------------------------------
            Script de Análisis de Transcripción       
-----------------------------------------------------
Este script permite analizar transcripciones de audio
mediante la API de OpenAI, facilitando un análisis 
interactivo posterior. El objetivo es procesar grandes
cantidades de texto y obtener respuestas útiles.
-----------------------------------------------------

Leyendo el archivo: ./Juan Gabriel – Siempre En Mi Mente_transcript.srt...
Enviando el texto a la API de OpenAI para análisis...
Análisis completado con éxito.

Resultado del análisis:
La canción "Siempre En Mi Mente" de Juan Gabriel expresa el profundo remordimiento y el deseo de reconciliación de una persona que teme haber perdido a un ser querido por no haberle demostrado suficiente amor y atención. El tema central gira en torno al arrepentimiento y la esperanza de que la otra persona aún conserve sentimientos a pesar de los errores cometidos. La repetición de la frase "siempre en mi mente" subraya la persistencia del amor y la preocupación constante, incluso en la ausencia física de la otra persona...
```

#### 6. **Consideraciones Adicionales**

- **Formato de archivo `.srt`**: El script puede necesitar preprocesamiento si el archivo contiene marcas de tiempo que deben ser eliminadas antes de enviar el texto a la API. Si este es el caso, se puede agregar una función para limpiar las marcas de tiempo del archivo `.srt` antes de procesarlo.
  
- **División de texto**: Si la transcripción es muy larga, considera dividirla en partes para enviarlas por separado a la API y analizar cada parte por separado.

Este caso de uso te permitirá probar el script de manera efectiva y verificar que el análisis del contenido de la transcripción funcione como esperado.

### Tamaño Máximo de Tokens

El tamaño máximo de texto que puedes enviar a la API de OpenAI no se mide en términos de tamaño de archivo, sino en términos de tokens. Los tokens son unidades de texto que pueden ser tan cortas como un solo carácter o tan largas como una sola palabra, dependiendo del idioma y el contenido, de acuerdo a las siguientes consideraciones:

1. **Para GPT-3.5**:
   - El límite es de 4,096 tokens por solicitud, incluyendo tanto la entrada (prompt) como la salida (respuesta).

2. **Para GPT-4**:
   - El límite puede variar según el modelo específico. Los modelos GPT-4 con más capacidad pueden manejar hasta 8,000 tokens o incluso 32,000 tokens en total (entrada + salida), pero estos límites pueden variar según el modelo y la configuración específica que elijas.

### Manejo de Archivos Grandes

Si tus transcripciones superan estos límites de tokens:

1. **Dividir el Texto**:
   - Tendrás que dividir el texto en partes más pequeñas que estén dentro del límite de tokens. Puedes hacerlo manualmente o mediante un script que divida el texto en fragmentos adecuados.

2. **Procesar en Partes**:
   - Envía cada fragmento a la API por separado y luego combina los resultados según sea necesario.

3. **Contar Tokens**:
   - Usa una herramienta o biblioteca para contar los tokens en tu texto antes de enviarlo a la API. OpenAI proporciona herramientas para estimar el número de tokens.

#### Contar Tokens

Para contar tokens en Python, puedes usar el paquete `tiktoken`, que es compatible con los modelos de OpenAI. Aquí tienes un ejemplo básico:

Aquí tienes el script para que solicite al usuario la ruta del archivo a procesar y luego cuente el número de tokens en ese archivo.

```python
import tiktoken
import os

def contar_tokens(file_path):
    """Cuenta el número de tokens en un archivo de texto."""
    if not os.path.exists(file_path):
        print(f"Error: El archivo '{file_path}' no existe.")
        return None
    
    # Crear un tokenizador para el modelo GPT-4
    tokenizer = tiktoken.get_encoding("cl100k_base")

    print(f"Leyendo el archivo: {file_path}...")
    with open(file_path, 'r', encoding='utf-8') as file:
        texto = file.read()

    tokens = tokenizer.encode(texto)
    return len(tokens)

def main():
    print("-----------------------------------------------------")
    print("        Script para Contar Tokens en un Archivo       ")
    print("-----------------------------------------------------")
    print("Este script cuenta el número de tokens en un archivo")
    print("de texto utilizando la biblioteca tiktoken para los")
    print("modelos de OpenAI.")
    print("-----------------------------------------------------\n")
    
    # Solicitar al usuario la ruta del archivo de texto
    archivo_transcripcion = input("Por favor, introduce la ruta al archivo de texto: ").strip()
    
    # Contar los tokens en el archivo
    numero_de_tokens = contar_tokens(archivo_transcripcion)
    
    if numero_de_tokens is not None:
        print(f"Número de tokens en el archivo: {numero_de_tokens}")

if __name__ == "__main__":
    main()
```

**Instrucciones para Ejecutar el Script**

1. **Instalar `tiktoken`**:
   Si aún no has instalado la biblioteca `tiktoken`, hazlo con:
   ```bash
   pip3 install tiktoken
   ```

2. **Guardar el Script**:
   Guarda el script en un archivo, por ejemplo `contar_tokens.py`.

3. **Ejecutar el Script**:
   Abre una terminal, navega al directorio donde guardaste el script y ejecuta:
   ```bash
   python3 contar_tokens.py
   ```

4. **Introducir la Ruta del Archivo**:
   Cuando se te solicite, introduce la ruta al archivo de texto que deseas analizar. El script leerá el archivo, contará el número de tokens y mostrará el resultado.

Este script ahora está listo para que el usuario introduzca la ruta del archivo de texto que se desea procesar, y luego contará el número de tokens en ese archivo.
