# Informe de Pruebas Dinámicas — academic_platform

## 1. Introducción

Las pruebas dinámicas permiten verificar el comportamiento real de un sistema mediante la ejecución activa del software. A diferencia de las pruebas estáticas, estas requieren que el sistema esté en funcionamiento para observar sus respuestas ante entradas controladas.

El presente informe documenta el proceso de diseño, ejecución y análisis de pruebas dinámicas aplicadas al sistema **academic_platform**, una plataforma web académica institucional. Se utilizaron técnicas de partición de equivalencia, análisis de valores límite y validación estructural de base de datos.

---

## 2. Objetivos

### 2.1 Objetivo General

Ejecutar pruebas dinámicas sobre los módulos principales de **academic_platform** para verificar su correcto funcionamiento ante entradas válidas e inválidas, garantizando la calidad del sistema antes de su uso institucional.

### 2.2 Objetivos Específicos

- Diseñar casos de prueba basados en técnicas de caja negra (partición de equivalencia y análisis de valores límite).
- Ejecutar los casos de prueba mediante Selenium IDE sobre el navegador Chromium 144.
- Registrar los resultados obtenidos y compararlos con los resultados esperados.
- Identificar y documentar incidencias encontradas durante la ejecución.
- Emitir conclusiones y recomendaciones sobre la calidad del sistema.

---

## 3. Descripción del Sistema

| Atributo | Detalle |
|----------|---------|
| **Nombre** | academic_platform |
| **Tipo** | Plataforma web académica institucional |
| **Tecnología** | PHP 8.x con servidor local embebido |
| **Navegador de referencia** | Chromium 144 |
| **Herramienta de pruebas** | Selenium IDE |

### 3.1 Módulos del Sistema

| # | Módulo | Descripción |
|---|--------|-------------|
| 1 | Registro de Usuarios | Registro público para estudiantes y docentes |
| 2 | Autenticación | Login y control de acceso por rol |
| 3 | Gestión de Trabajos | Creación de actividades (docente) y entregas (estudiante) |
| 4 | Calificaciones | Puntuación manual y retroalimentación por docente |
| 5 | Seguimiento Académico | Visualización de notas: IA, manual y final |
| 6 | Validación de BD | Verificación del esquema de base de datos (`db-tests`) |
| 7 | Recuperación de Contraseña | Restablecimiento por token |

### 3.2 Roles del Sistema

- **Administrador** — Gestión completa del sistema
- **Docente** — Creación de actividades, calificación y retroalimentación
- **Estudiante** — Entrega de trabajos y consulta de seguimiento académico

---

## 4. Proceso de Pruebas Dinámicas

### 4.1 Técnicas Aplicadas

| Técnica | Descripción |
|---------|-------------|
| **Partición de equivalencia** | Agrupa entradas en clases válidas e inválidas para reducir casos redundantes |
| **Análisis de valores límite** | Prueba los valores exactos en los bordes de los rangos permitidos |
| **Validación estructural** | Verifica la integridad del esquema de base de datos |

### 4.2 Reglas de Validación por Campo

#### Registro de Usuario

| Campo | Regla |
|-------|-------|
| Nombre | 5 – 80 caracteres |
| Email | Formato válido, único en el sistema |
| Contraseña | 8 – 20 caracteres, requiere mayúscula, minúscula, número y símbolo |
| Rol | Solo `estudiante` o `docente` en registro público |

#### Entrega de Trabajo

| Campo | Regla |
|-------|-------|
| Extensión de archivo | `.pdf`, `.doc`, `.docx`, `.txt` |
| Tamaño de archivo | Máximo 2 MB |
| Respuesta abierta | 20 – 2000 caracteres |

#### Calificación

| Campo | Regla |
|-------|-------|
| Nota manual | 0 – 100 |
| Retroalimentación | 5 – 1000 caracteres |
| Duplicidad | Una entrega por estudiante por actividad |

---

## 5. Casos de Prueba

### Suite 1 — Registro de Usuarios

#### CP-01: Registro válido de usuario estudiante

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-01 |
| **Módulo** | Registro de Usuarios |
| **Técnica** | Camino feliz |
| **Precondición** | El email no existe en el sistema |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Nombre | `Estudiante Demo` |
| Email | `nuevo_estudiante@demo.com` |
| Contraseña | `Password1!` |
| Rol | `estudiante` |

**Pasos:**
1. Abrir el formulario de registro.
2. Completar todos los campos con valores válidos.
3. Hacer clic en "Registrar".

**Resultado esperado:** El sistema registra al usuario y redirige al login con mensaje de éxito.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

#### CP-02: Validación de nombre — límite inferior

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-02 |
| **Módulo** | Registro de Usuarios |
| **Técnica** | Análisis de valores límite |
| **Precondición** | Acceso al formulario de registro |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Nombre | `Juan` (4 caracteres) |
| Email | `test@demo.com` |
| Contraseña | `Password1!` |
| Rol | `estudiante` |

**Pasos:**
1. Ingresar nombre con 4 caracteres.
2. Completar los demás campos correctamente.
3. Hacer clic en "Registrar".

**Resultado esperado:** El sistema rechaza el registro con el mensaje: *"El nombre debe tener entre 5 y 80 caracteres"*.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

#### CP-03: Validación de email duplicado

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-03 |
| **Módulo** | Registro de Usuarios |
| **Técnica** | Partición de equivalencia |
| **Precondición** | El email `estudiante@demo.com` ya existe en el sistema |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Nombre | `Estudiante Test` |
| Email | `estudiante@demo.com` |
| Contraseña | `Password1!` |
| Rol | `estudiante` |

**Pasos:**
1. Ingresar un email ya registrado en el sistema.
2. Completar los demás campos correctamente.
3. Hacer clic en "Registrar".

**Resultado esperado:** El sistema rechaza el registro con el mensaje: *"Ese correo ya está registrado"*.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

### Suite 2 — Autenticación

#### CP-04: Login válido de estudiante

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-04 |
| **Módulo** | Autenticación |
| **Técnica** | Camino feliz |
| **Precondición** | El usuario `estudiante@demo.com` existe en el sistema |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Email | `estudiante@demo.com` |
| Contraseña | `Password1!` |

**Pasos:**
1. Abrir el formulario de login.
2. Ingresar email y contraseña válidos.
3. Hacer clic en "Ingresar".

**Resultado esperado:** El usuario accede al sistema y visualiza las opciones correspondientes a su rol.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

### Suite 3 — Gestión de Trabajos

#### CP-05: Entrega válida de trabajo académico

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-05 |
| **Módulo** | Gestión de Trabajos |
| **Técnica** | Camino feliz |
| **Precondición** | Sesión iniciada como estudiante, actividad disponible |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Archivo | `trabajo.txt` (extensión permitida, <2 MB) |
| Respuesta abierta | Texto de más de 20 caracteres |

**Pasos:**
1. Acceder al módulo de Gestión de Trabajos.
2. Seleccionar la actividad correspondiente.
3. Adjuntar archivo `.txt` válido.
4. Escribir respuesta abierta de más de 20 caracteres.
5. Hacer clic en "Enviar Trabajo".

**Resultado esperado:** La entrega se registra correctamente y el sistema calcula la puntuación inicial de IA.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

#### CP-06: Validación de respuesta abierta — límite inferior

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-06 |
| **Módulo** | Gestión de Trabajos |
| **Técnica** | Análisis de valores límite |
| **Precondición** | Sesión iniciada como estudiante, actividad disponible |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Archivo | `trabajo.txt` |
| Respuesta abierta | `Esta es respuest` (19 caracteres) |

**Pasos:**
1. Completar el formulario de entrega.
2. Ingresar una respuesta abierta de exactamente 19 caracteres.
3. Hacer clic en "Enviar Trabajo".

**Resultado esperado:** El sistema rechaza la entrega e informa el rango permitido (20 – 2000 caracteres).

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

#### CP-07: Validación de extensión de archivo inválida

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-07 |
| **Módulo** | Gestión de Trabajos |
| **Técnica** | Partición de equivalencia |
| **Precondición** | Sesión iniciada como estudiante, actividad disponible |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Archivo | `imagen.jpg` / `programa.exe` |
| Respuesta abierta | Texto válido (>20 caracteres) |

**Pasos:**
1. Intentar adjuntar un archivo con extensión `.jpg` o `.exe`.
2. Completar la respuesta abierta con texto válido.
3. Hacer clic en "Enviar Trabajo".

**Resultado esperado:** El sistema rechaza la carga e informa las extensiones permitidas (`.pdf`, `.doc`, `.docx`, `.txt`).

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

### Suite 4 — Calificaciones (Docente)

#### CP-08: Registro de calificación manual por docente

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-08 |
| **Módulo** | Calificaciones |
| **Técnica** | Camino feliz |
| **Precondición** | Sesión iniciada como docente, entrega del estudiante disponible |

**Entradas:**

| Campo | Valor |
|-------|-------|
| Nota manual | `92` |
| Retroalimentación | Texto válido (>5 caracteres) |

**Pasos:**
1. Iniciar sesión como docente.
2. Abrir la lista de entregas.
3. Seleccionar la entrega del estudiante.
4. Registrar nota `92` y retroalimentación válida.
5. Guardar cambios.

**Resultado esperado:** La calificación queda almacenada y es visible en el seguimiento académico del estudiante.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

### Suite 5 — Validación de Base de Datos

#### CP-09: Verificación de estructura de base de datos

| Atributo | Detalle |
|----------|---------|
| **ID** | CP-09 |
| **Módulo** | Módulo `db-tests` |
| **Técnica** | Validación estructural |
| **Precondición** | Acceso al módulo de pruebas de BD |

**Pasos:**
1. Acceder al módulo de "Pruebas de Estructura de Base de Datos".
2. Ejecutar la verificación automática.

**Resultado esperado:** El módulo muestra estado `OK` para tablas, restricciones `UNIQUE` y claves foráneas `FOREIGN KEY`.

**Resultado obtenido:** Conforme al esperado.

**Estado:** `APROBADO`

---

## 6. Resumen de Ejecución

| ID | Descripción | Técnica | Estado |
|----|-------------|---------|--------|
| CP-01 | Registro válido de estudiante | Camino feliz | APROBADO |
| CP-02 | Nombre con 4 caracteres (límite inferior) | Valor límite | APROBADO |
| CP-03 | Email duplicado | Partición equivalencia | APROBADO |
| CP-04 | Login válido de estudiante | Camino feliz | APROBADO |
| CP-05 | Entrega válida con archivo `.txt` | Camino feliz | APROBADO |
| CP-06 | Respuesta abierta de 19 caracteres | Valor límite | APROBADO |
| CP-07 | Archivo con extensión inválida `.jpg`/`.exe` | Partición equivalencia | APROBADO |
| CP-08 | Registro de nota manual por docente | Camino feliz | APROBADO |
| CP-09 | Validación de estructura de BD | Estructural | APROBADO |

**Total:** 9 casos de prueba — **9 Aprobados / 0 Fallidos**

---

## 7. Reporte de Incidencias

### INC-01: Labels del dashboard en inglés

| Atributo | Detalle |
|----------|---------|
| **ID** | INC-01 |
| **Módulo** | Dashboard |
| **Severidad** | Baja |
| **Estado** | Abierto |

**Descripción:** Las métricas del panel principal se muestran con etiquetas en inglés: *"Users"*, *"Assignments"*, *"Submissions"*, *"Graded"*, generando una inconsistencia de idioma en una plataforma cuya interfaz está en español.

**Impacto:** Afecta la experiencia del usuario pero no compromete la funcionalidad del sistema.

**Recomendación:** Traducir las etiquetas al español para mantener coherencia en toda la interfaz.

---

### INC-02: Token de recuperación de contraseña visible en pantalla

| Atributo | Detalle |
|----------|---------|
| **ID** | INC-02 |
| **Módulo** | Recuperación de Contraseña |
| **Severidad** | Media |
| **Estado** | Abierto |

**Descripción:** El módulo de recuperación de contraseña muestra el token directamente en pantalla. Si bien es útil para el entorno de pruebas, este comportamiento no es apropiado para un entorno de producción.

**Impacto:** Representa un riesgo de seguridad en producción, ya que el token podría ser interceptado visualmente.

**Recomendación:** En producción, el token debe enviarse únicamente al correo registrado del usuario, nunca mostrarse en pantalla.

---

### INC-03: Tamaño máximo de archivo no informado antes de la carga

| Atributo | Detalle |
|----------|---------|
| **ID** | INC-03 |
| **Módulo** | Gestión de Trabajos |
| **Severidad** | Baja |
| **Estado** | Abierto |

**Descripción:** El sistema valida la extensión del archivo pero no informa al usuario el tamaño máximo permitido (2 MB) antes de intentar la carga. El error solo aparece después de intentar subir un archivo que supera el límite.

**Impacto:** Afecta la usabilidad, especialmente para usuarios que cargan archivos pesados sin conocer la restricción.

**Recomendación:** Mostrar la restricción de tamaño máximo junto al campo de carga, antes de que el usuario intente subir el archivo.

---

## 8. Conclusiones

1. Los **9 casos de prueba ejecutados** resultaron aprobados, lo que indica que los módulos principales de **academic_platform** responden correctamente ante entradas válidas e inválidas.

2. Las **técnicas de partición de equivalencia y análisis de valores límite** permitieron identificar el comportamiento del sistema en los bordes de los rangos permitidos, verificando que las validaciones están correctamente implementadas.

3. La **validación estructural de la base de datos** (CP-09) confirma la integridad del esquema: tablas existentes, restricciones `UNIQUE` activas y relaciones `FOREIGN KEY` correctamente definidas.

4. Se identificaron **3 incidencias abiertas** (INC-01, INC-02, INC-03) de severidad baja a media, ninguna de las cuales bloquea el uso del sistema, pero deben ser atendidas antes del despliegue en producción.

5. El sistema cumple los **criterios básicos de calidad** para su uso académico institucional, con las observaciones documentadas en los reportes de incidencia.

---

## 9. Recomendaciones

- Corregir las etiquetas del dashboard al español (INC-01).
- Implementar el envío del token de recuperación únicamente por correo electrónico en producción (INC-02).
- Agregar indicación visible del tamaño máximo de archivo permitido en el formulario de entrega (INC-03).
- Ampliar la cobertura de pruebas para incluir casos de límite superior (nombre de 81 caracteres, respuesta de 2001 caracteres, archivo de 2.1 MB).
- Considerar pruebas de seguridad básicas: inyección SQL, XSS en campos de texto, y control de acceso entre roles.

---

## 10. Referencias

- Selenium IDE — [https://www.selenium.dev/selenium-ide/](https://www.selenium.dev/selenium-ide/)
- ISTQB Foundation Level Syllabus — Técnicas de diseño de pruebas de caja negra
- PHP 8.x Documentation — [https://www.php.net/docs.php](https://www.php.net/docs.php)
- Chromium Browser — Versión 144

---

*Documento generado a partir del análisis de pruebas dinámicas sobre academic_platform.*
