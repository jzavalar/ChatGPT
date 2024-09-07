# API de OpenAI

## Scrip Elemental

Ejemplo de un script en Python que utiliza la API de OpenAI para procesar archivos de texto de transcripciones. Este script envía el contenido de un archivo de texto a la API de OpenAI, recibe la respuesta, y permite realizar un análisis interactivo posterior.

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

Este script ahora proporciona una experiencia más informativa, guiando al usuario a través de cada paso del proceso.

### Consideraciones Adicionales

   - *Dividir el texto*: Si las transcripciones son muy largas, podrías necesitar dividir el archivo en partes más pequeñas para procesarlas de manera efectiva, dado que la API tiene un límite de tokens por solicitud.
   - *Interacción posterior*: El script actual simplemente muestra el resultado en la consola, pero podrías adaptarlo para interactuar más con la API, hacer preguntas adicionales o analizar respuestas en diferentes partes de la transcripción.

Con este script, podrás empezar a probar el procesamiento y análisis de tus transcripciones utilizando la API de OpenAI.
