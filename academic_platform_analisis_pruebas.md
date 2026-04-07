# Análisis del Documento — academic_platform

El sistema es **academic_platform** — una plataforma web académica en PHP 8.x. El documento cubre **9 casos de prueba** con Selenium IDE en Chromium 144.

---

## Pruebas que haría

### Suite 1: Registro de Usuarios

| CP | Escenario | Técnica |
|----|-----------|---------|
| CP-01 | Registro válido como estudiante | Camino feliz |
| CP-02 | Nombre con 4 caracteres (límite inferior) | Valor límite |
| CP-03 | Email duplicado `estudiante@demo.com` | Partición equivalencia |

### Suite 2: Autenticación

| CP | Escenario | Técnica |
|----|-----------|---------|
| CP-04 | Login válido como estudiante | Camino feliz |

### Suite 3: Gestión de Trabajos

| CP | Escenario | Técnica |
|----|-----------|---------|
| CP-05 | Entrega válida con archivo `.txt` + respuesta >20 chars | Camino feliz |
| CP-06 | Respuesta con 19 caracteres (límite inferior) | Valor límite |
| CP-07 | Archivo con extensión `.jpg` o `.exe` | Partición equivalencia |

### Suite 4: Calificaciones (Docente)

| CP | Escenario | Técnica |
|----|-----------|---------|
| CP-08 | Registro de nota manual 92 + feedback válido | Camino feliz |

### Suite 5: Validación BD

| CP | Escenario | Técnica |
|----|-----------|---------|
| CP-09 | Verificación de tablas, UNIQUE y FOREIGN KEYS | Estructural |

---

## Flujo de ejecución

```
1. SETUP
   └── Abrir Chromium → URL del servidor local

2. SUITE 1 — Registro
   ├── CP-01: Datos válidos → assertText "éxito"
   ├── CP-02: Nombre 4 chars → assertText "entre 5 y 80"
   └── CP-03: Email duplicado → assertText "ya está registrado"

3. SUITE 2 — Login
   └── CP-04: Login estudiante → assertElementPresent dashboard

4. SUITE 3 — Entregas (requiere sesión CP-04)
   ├── CP-05: Archivo .txt + respuesta válida → assertText "entregado"
   ├── CP-06: Respuesta 19 chars → assertText rango permitido
   └── CP-07: Archivo .jpg/.exe → assertText extensión inválida

5. SUITE 4 — Calificaciones (requiere login docente)
   └── CP-08: Nota 92 + feedback → assertText visible en seguimiento

6. SUITE 5 — BD
   └── CP-09: Módulo db-tests → assertText "OK"
```

---

## Datos de prueba clave

```
Email válido:       estudiante@demo.com
Password válido:    Mínimo 8 chars, mayúscula + número + símbolo
Nombre límite:      4 chars (inválido) / 5 chars (válido)
Respuesta límite:   19 chars (inválido) / 20 chars (válido)
Nota manual:        92 (dentro de 0-100)
Archivos válidos:   .txt, .pdf, .doc, .docx
Archivos inválidos: .jpg, .exe
```

---

## Incidencias a documentar

| ID | Descripción | Tipo |
|----|-------------|------|
| INC-01 | Labels del dashboard en inglés | Inconsistencia de idioma |
| INC-02 | Token de recuperación visible en pantalla | Problema de seguridad |
| INC-03 | No informa el tamaño máximo de archivo antes de subir | Usabilidad |
