# Chat Distribuido por Consola con ZeroC Ice y Gradle Multimódulo

Taller guiado de **Computación en Internet I** (09810 - TIC) · Universidad Icesi · Período 2026-2 · NRC 12378

Sistema de chat multiusuario por consola construido con el middleware **ZeroC Ice** (RPC). El contrato remoto se define en **Slice** y el proyecto está organizado como un **Gradle multimódulo**.

## Integrantes

| Estudiante | Código |
|---|---|
| Juan Camilo Pelaez Marulanda | A00411206 |
| Andres Martinez Martinez | A00411070 |

## Arquitectura

| Módulo | Descripción |
|---|---|
| `common` | Contiene el contrato Slice (`Chat.ice`) y genera el código Java compartido (interfaces, estructuras y excepciones). |
| `server` | Servidor Ice con el *Servant* `ChatRoomI` (thread-safe). Escucha en el puerto TCP **10000** con la identidad `ChatService`. |
| `client` | Cliente interactivo por consola con un hilo demonio que consulta mensajes nuevos cada 500 ms. |

```text
ice-chat-project/
|-- settings.gradle
|-- build.gradle
|-- common/
|   |-- build.gradle
|   `-- src/main/slice/Chat.ice
|-- server/
|   |-- build.gradle
|   `-- src/main/java/chat/server/
|       |-- ChatRoomI.java
|       `-- ServerMain.java
`-- client/
    |-- build.gradle
    `-- src/main/java/chat/client/
        `-- ClientMain.java
```

## Requisitos previos

- **JDK 17 o superior** (`java -version`)
- **Gradle** (`gradle -v`) o el wrapper del proyecto (`./gradlew`)
- **ZeroC Ice 3.7** instalado, con el compilador **`slice2java`** disponible en el `PATH` (`slice2java --version`)
- Acceso a Maven Central para descargar la dependencia `com.zeroc:ice:3.7.10`

## Compilación

Desde la raíz del proyecto (`ice-chat-project/`):

```bash
# Con Gradle instalado en el sistema
gradle build

# O con el wrapper (en Windows: gradlew.bat build)
./gradlew build
```

Gradle ejecuta automáticamente la tarea `compileSlice` (que invoca `slice2java` sobre `Chat.ice`), empaqueta `common` y compila `server` y `client`. El resultado esperado es `BUILD SUCCESSFUL`.

Si desea generar solo el código Java a partir del contrato Slice:

```bash
slice2java --output-dir common/src/main/java common/src/main/slice/Chat.ice
```

## Ejecución

Abra  terminales independientes, ubicadas en la raíz del proyecto (`ice-chat-project/`).

**Terminal 1 — Servidor** (debe iniciarse primero):

```bash
gradle :server:run --console=plain
```

Debe imprimir `SERVIDOR ZEROC ICE INICIADO EXITOSAMENTE` y quedar escuchando en el puerto 10000.

**Terminal 2 — Cliente 1 (nombre):**

```bash
gradle :client:run --console=plain
```

## Comandos del chat

| Comando | Acción |
|---|---|
| _texto libre_ | Envía el mensaje a la sala. |
| `/users` | Lista los usuarios conectados. |
| `/exit` | Cierra la sesión y sale del chat. |

## Solución de problemas

| Problema | Posible solución |
|---|---|
| `gradle` no se reconoce como comando | Instalar Gradle y agregarlo al `PATH`, o usar `./gradlew`. |
| `slice2java` no se encuentra | Instalar ZeroC Ice 3.7 y agregar su carpeta `bin` al `PATH`. |
| `Connection refused` en el cliente | Verificar que el servidor (Terminal 1) esté en ejecución y que el puerto 10000 esté libre. |
| El cliente no lee el teclado | Ejecutar con `--console=plain`; `client/build.gradle` ya define `standardInput = System.in`. |

## Tecnologías

Java 17 · ZeroC Ice 3.7.10 · Slice · Gradle multimódulo
