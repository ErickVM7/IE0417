# Auditoría de diseño de software del sistema EIEInfo

## Entrega 1: Levantamiento del sistema y diagnóstico inicial

**Curso:** IE0417 - Diseño de Software para Ingeniería  
**Sistema auditado:** EIEInfo  
**Tipo de entrega:** Levantamiento del sistema y diagnóstico inicial  
**Estudiante:** Erick Vargas Monge  
**Carné:** C08215  
**Fecha:** 8 de junio de 2026  

---

## 1. Introducción

Este documento presenta un levantamiento inicial del sistema EIEInfo a partir de evidencia verificable encontrada en el repositorio. El análisis no se basa únicamente en el README, sino también en archivos de configuración, rutas, modelos, pruebas, scripts Docker, pipeline CI/CD y estructura general del proyecto.

La finalidad de esta entrega es responder qué sistema se tiene enfrente, cómo está organizado técnicamente y cuáles son sus principales riesgos preliminares desde una perspectiva de diseño de software. Los hallazgos documentados corresponden a observaciones iniciales y pueden ser refinados en etapas posteriores de auditoría.

Este levantamiento sigue el marco de evaluación del curso IE0417 (Diseño de Software para Ingeniería), enfocándose en las dimensiones de arquitectura general, modularidad, modelo de dominio, calidad del código, mantenibilidad, pruebas, seguridad observable y operación.

---

## 2. Metodología de levantamiento

El levantamiento se realizó mediante inspección estática del repositorio. Se revisaron los siguientes elementos:

- Estructura general de carpetas bajo `src/server`.
- Configuración principal de Django en `src/server/eieinfo/settings.py`.
- Ruteo principal y rutas por módulo en archivos `urls.py`.
- Modelos Django definidos en archivos `models.py`.
- Carpetas `migrations/`.
- Archivos de pruebas `tests.py`.
- Dependencias en `requirements.txt` y `docker/django/requirements.txt`.
- Configuración Docker en `docker-compose.yml` y scripts bajo `docker/django/`.
- Pipeline CI/CD en `.drone.yml`.

Las afirmaciones técnicas se documentan con rutas y líneas cuando existe evidencia directa. Cuando la evidencia corresponde a ausencia de archivos, se indica explícitamente.

---

## 3. Ficha técnica del sistema

| Aspecto | Descripción | Evidencia |
|---|---|---|
| Tipo de sistema | Aplicación web institucional monolítica con modularización por apps Django. | `src/server/eieinfo/settings.py:44` |
| Framework principal | Django 4.1.3. | `requirements.txt:2` |
| Base de datos | MariaDB/MySQL. | `requirements.txt:27`, `docker-compose.yml:6` |
| Módulos principales | `estudiantes`, `profesores`, `administrativos`, `cursos`, `inventario`, `firma_digital`, `postulaciones`, `trabajo_final_de_graduacion`, entre otros (23 apps registradas). | `src/server/eieinfo/settings.py:44-93` |
| Servidor de aplicación | Gunicorn dentro del servicio Docker de la aplicación. | `docker-compose.yml:67` |
| Servidor web | nginx como servicio separado. | `docker-compose.yml:31` |
| CI/CD | Pipeline Drone con pasos de base de datos, migraciones, pruebas/cobertura y despliegue SSH. | `.drone.yml:1`, `.drone.yml:273`, `.drone.yml:344` |
| Pruebas | Existen pruebas Django distribuidas en varios módulos. | `src/server/trabajo_final_de_graduacion/tests.py:43` |

---

## 4. Mapa general del sistema

El sistema se organiza como una aplicación web Django con múltiples apps internas. La entrada HTTP se canaliza mediante `eieinfo/urls.py`, que delega rutas hacia módulos funcionales. La aplicación se despliega con nginx, Gunicorn y MariaDB mediante Docker Compose.

### 4.1 Diagrama de contexto

```mermaid
flowchart LR
    U1[Visitantes públicos] --> N[Nginx]
    U2[Estudiantes] --> N
    U3[Profesores] --> N
    U4[Administrativos] --> N

    N --> A[EIEInfo<br/>Aplicación Django]
    A --> DB[(MariaDB / MySQL)]
    A --> FS[(Archivos media / static)]
    A --> SMTP[Servidor SMTP<br/>smtp.eie.ucr.ac.cr]
    A --> CI[Pipeline Drone CI/CD]

    CI --> A
````

### 4.2 Diagrama de módulos y contenedores

```mermaid
flowchart TB
    subgraph Infraestructura_Docker["Infraestructura Docker"]
        N[Nginx<br/>docker-compose.yml:31]
        APP[EIEInfo App<br/>Django + Gunicorn<br/>docker-compose.yml:48,67]
        DB[(MariaDB<br/>docker-compose.yml:6)]
        VOL1[(static_volume)]
        VOL2[(media_volume)]
    end

    subgraph Proyecto_Django["Proyecto Django: 23 Apps"]
        URLS[eieinfo/urls.py<br/>Ruteo principal]
        WEB[webpage]
        EST[estudiantes]
        PROF[profesores]
        ADM[administrativos]
        CUR[cursos]
        INV[inventario]
        FD[firma_digital]
        POST[postulaciones]
        TFG[trabajo_final_de_graduacion]
        LAB[laboratorios]
        PRY[proyectos]
        OTROS["+ 11 apps más"]
    end

    N --> APP
    APP --> DB
    APP --> VOL1
    APP --> VOL2
    APP --> URLS

    URLS --> WEB
    URLS --> EST
    URLS --> PROF
    URLS --> ADM
    URLS --> CUR
    URLS --> INV
    URLS --> FD
    URLS --> POST
    URLS --> TFG
    URLS --> LAB
    URLS --> PRY
    URLS --> OTROS
```


### 4.3 Componentes principales

| Capa | Componentes | Evidencia |
|---|---|---|
| Entrada HTTP | Archivo raíz de rutas `eieinfo/urls.py`. | `src/server/eieinfo/urls.py:23` |
| Portal público | `webpage`, `anuncios`, `eventos`, `proyectos`, `laboratorios`, `cursos`. | `src/server/eieinfo/urls.py:37-47`, `src/server/eieinfo/urls.py:62` |
| Usuarios estudiantes | Módulo `estudiantes` con subrutas para home, cursos, asistencias, trámites, bodega, proyecto eléctrico y práctica profesional. | `src/server/estudiantes/urls.py:10-45` |
| Usuarios profesores | Módulo `profesores` con subrutas por áreas funcionales. | `src/server/profesores/urlpatterns/cursos.py:8`, `src/server/profesores/urlpatterns/consejo_asesor.py:8` |
| Administración académica | `administrativos`, `cursos`, `proyectos`, `laboratorios`, `trabajo_final_de_graduacion`. | `src/server/eieinfo/urls.py:33`, `src/server/eieinfo/urls.py:47`, `src/server/eieinfo/urls.py:68` |
| Persistencia | Modelos Django distribuidos por app (8 clases en estudiantes, 7 en trabajo_final_de_graduacion). | `src/server/estudiantes/models.py:109`, `src/server/trabajo_final_de_graduacion/models.py:124` |
| Infraestructura | Docker Compose con MariaDB, nginx y aplicación Django/Gunicorn. | `docker-compose.yml:6`, `docker-compose.yml:31`, `docker-compose.yml:48` |

---

## 5. Inventario funcional

### 5.1 Tipos de usuarios y actores

| Actor | Descripción | Áreas funcionales |
|---|---|---|
| Visitante público | Usuario sin autenticación en el portal institucional. | Consulta de información, eventos, noticias, laboratorios, cursos públicos. |
| Estudiante | Usuario autenticado del programa de Ingeniería Eléctrica. | Portal personal, cursos inscritos, asistencias, trámites, bodega, proyecto eléctrico, práctica profesional. |
| Profesor | Docente de la Escuela de Ingeniería Eléctrica. | Cátedras, calificaciones, asistencias, proyectos, publicaciones, laboratorios, consejo asesor. |
| Administrativo | Personal administrativo de la institución. | Gestión de estudiantes, profesores, cursos, ciclos, horarios, lugares. |

### 5.2 Áreas funcionales principales

| Área funcional | Funciones observadas | Módulos involucrados | Evidencia |
|---|---|---|---|
| Portal institucional | Inicio, búsqueda, contacto, publicaciones, personal, estudios, recursos y páginas estáticas. | `webpage`, `anuncios`, `eventos` | `src/server/webpage/urls.py:10-56` |
| Gestión de cursos y planes | Listado de cursos, detalle, planes de estudio y horarios por ciclo. | `cursos`, `administrativos` | `src/server/cursos/urls.py:8-18` |
| Portal de estudiantes | Portal personal, cursos inscritos, asistencias, trámites, bodega, proyecto eléctrico y práctica profesional. | `estudiantes`, `asistencias`, `inventario`, `proyecto_electrico`, `practica_profesional` | `src/server/estudiantes/urls.py:10-45` |
| Portal de profesores | Gestión de cátedras, cursos, proyectos, publicaciones, laboratorios, consejo asesor y asistencias. | `profesores`, `proyectos`, `laboratorios` | `src/server/profesores/urlpatterns/cursos.py:13-43` |
| Inventario y bodega | Gestión de bodega, grupos, funcionarios y préstamos de equipo. | `inventario` | `src/server/inventario/urls.py:6-29` |
| Firma digital | Solicitudes de firma, carga de PDF, documentos firmados y operaciones AJAX. | `firma_digital` | `src/server/firma_digital/urls.py:7-50` |
| Postulaciones | Formularios de postulación, listado de postulantes, detalle y exportación. | `postulaciones` | `src/server/postulaciones/urls.py` |
| Trabajos finales de graduación | TFG, comités, revisiones, defensas, documentos complementarios. | `trabajo_final_de_graduacion`, `trabajos_finales` | `src/server/trabajo_final_de_graduacion/models.py:86-280` |

### 5.3 Módulos críticos para la operación del sistema

Los siguientes módulos son considerados críticos porque:
1. **`estudiantes`** y **`profesores`**: Contienen los portales principales de los usuarios finales. Cualquier indisponibilidad afecta directamente a estudiantes y docentes.
2. **`cursos`**: Base de datos de la oferta académica. Su integridad es fundamental para la operación académica.
3. **`administrativos`**: Gestión de datos maestros (ciclos, horarios, lugares). Afecta a múltiples módulos.
4. **`firma_digital`**: Crítico para procesos de firma de documentos (trabajos finales, autorizaciones).
5. **`trabajo_final_de_graduacion`**: Gestiona graduaciones. Su indisponibilidad impide procesos de graduación.

---

## 6. Hallazgos iniciales

| ID | Hallazgo | Descripción | Evidencia | Impacto | Criticidad |
|---|---|---|---|---|---|
| H01 | Monolito Django modular | El sistema está organizado como un único proyecto Django con múltiples apps funcionales registradas centralmente. | `src/server/eieinfo/settings.py:44-93` (23 apps) | Cambios globales de configuración o despliegue pueden afectar a todos los módulos. | Media |
| H02 | Acoplamiento entre dominios | Varias apps importan directamente modelos de otras apps, lo que reduce independencia modular. | `src/server/cursos/models.py:3-5` (importa de administrativos, profesores, webpage), `src/server/profesores/views/consejo_asesor.py:9-20` | Aumenta el riesgo de regresiones cruzadas y dificulta evolución por módulos. | Alta |
| H03 | Ruteo raíz extenso | El archivo raíz de rutas concentra inclusiones de muchas áreas funcionales. | `src/server/eieinfo/urls.py:23-68` | El ruteo principal se convierte en un punto sensible de mantenimiento. | Media |
| H04 | Submodularización interna en apps grandes | Apps como `estudiantes` y `profesores` dividen rutas por subáreas funcionales. | `src/server/estudiantes/urls.py:10-45`, `src/server/profesores/urlpatterns/cursos.py:8` | Mejora la organización local, aunque no elimina acoplamiento entre dominios. | Baja |
| H05 | Ausencia de migraciones versionadas reales | Las carpetas `migrations/` contienen únicamente `__init__.py`; no se encontraron archivos `0001_initial.py` o `0002_*.py`. Esta evidencia corresponde a ausencia de archivos. | Evidencia por ausencia en `src/server/*/migrations/`; modelos existentes en `src/server/estudiantes/models.py:109`, `src/server/trabajo_final_de_graduacion/models.py:124` | Compromete trazabilidad, reproducibilidad y versionado del esquema de base de datos. | Alta |
| H06 | Eliminación y regeneración dinámica de migraciones | Scripts y pipeline **eliminan todas las migraciones versionadas** antes de regenerarlas automáticamente durante ejecución. | `.drone.yml:99` (find . -path "./src/server/*/migrations/*.py" -not -name "__init__.py" -delete), `.drone.yml:100` (python manage.py makemigrations), `docker/django/migraciones.sh:5-7` | Mezcla generación/evolución de esquema con ejecución operativa; impide reproducir estados históricos del esquema; aumenta riesgo de divergencia entre ambientes. | Alta |
| H07 | Configuración sensible visible en código | Contraseña de SMTP y posibles secretos adicionales están visibles o referenciados en archivos del repositorio sin ofuscación. | `src/server/eieinfo/settings.py:344` contiene explícitamente: `EMAIL_HOST_PASSWORD = 'lyZB6A8fdx'`; `src/server/eieinfo/settings.py:525`: `from eieinfo.secret.secret_credentials import *` | Exposición crítica de credenciales; riesgo de compromiso de SMTP; dificultad de rotación segura de secretos. | **Crítica** |
| H08 | Configuración dependiente del hostname | La configuración cambia a modo desarrollo si el hostname del servidor no es exactamente `faraday`. | `src/server/eieinfo/settings.py:480-490`: if socket.gethostname() == 'faraday' → DEBUG=False; else → DEBUG=True y ALLOWED_HOSTS=['*'] | Riesgo alto de despliegue con `DEBUG=True` y `ALLOWED_HOSTS=['*']` en cualquier servidor que no sea `faraday`; exposición no intencional de información de depuración. | **Crítica** |
| H09 | Cobertura CI parcial | El pipeline ejecuta cobertura sobre un subconjunto de apps, no sobre todos los módulos registrados. | `.drone.yml:273`: coverage sobre profesores, administrativos, estudiantes, cursos, webpage, trabajo_final_de_graduacion, postulaciones (7 de 23 apps) | Regresiones en módulos fuera del conjunto probado pueden pasar inadvertidas; falsos positivos de confianza en cambios. | Media |
| H10 | Uso de `csrf_exempt` en múltiples vistas | Varias vistas desactivan protección CSRF, potencialmente exponiendo acciones sensibles. | `src/server/webpage/views.py:375` (@csrf_exempt), `src/server/conferencias/views.py:43` (@csrf_exempt), `src/server/firma_digital/views.py:410` (@method_decorator(csrf_exempt, name='dispatch')) | Posible superficie de ataque si las vistas procesan acciones sensibles (firma de documentos, cambios de inventario, etc.). | Alta |
| H11 | Dependencias divergentes entre ambientes | `requirements.txt` y `docker/django/requirements.txt` usan referencias distintas para paquetes críticos. | `requirements.txt:46`: git+https://github.com/rodrigo-garcia-leon/django-wiki@main; `docker/django/requirements.txt:52`: git+https://github.com/django-wiki/django-wiki.git@releases/0.10.1 | Riesgo de diferencias entre ambientes local, Docker y CI; incompatibilidad silenciosa entre versiones. | Media |
| H12 | Deuda técnica histórica visible | Hay señales de compatibilidad antigua, comandos/rutas comentadas y referencias obsoletas. | `src/server/eieinfo/settings.py:4` (referencia a Django 1.9.1), `src/server/estudiantes/urls.py:32-33` (rutas de TFG comentadas), `.drone.yml:366-383` (múltiples comandos de deploy comentados) | Aumenta costo de mantenimiento y requiere distinguir deuda activa de comentarios obsoletos; dificulta comprensión clara del estado operativo. | Media |

---

## 7. Matriz preliminar de riesgos

| Riesgo identificado | Posible causa | Impacto | Probabilidad | Prioridad preliminar | Hallazgos asociados |
|---|---|---|---|---|---|
| **Exposición crítica de credenciales** | Configuración sensible (contraseñas) codificada directamente en archivo de settings sin ofuscación o versionado seguro. | **Crítico** | **Alta** | **INMEDIATA** | H07 |
| **Despliegue accidental en modo inseguro** | Activación de DEBUG=True y ALLOWED_HOSTS=['*'] basada en comparación de hostname que puede fallar. | **Crítico** | **Media** | **INMEDIATA** | H08 |
| **Base de datos no reproducible desde el repositorio** | Ausencia de migraciones versionadas y eliminación automática de las mismas durante ejecución/preparación. | Alto | Alta | **Alta** | H05, H06 |
| **Regresiones cruzadas entre módulos** | Acoplamiento directo entre apps mediante importaciones de modelos y ruteo centralizado extenso. | Medio | Alta | **Alta** | H02, H03 |
| **Fallos no detectados por pruebas automatizadas** | Cobertura CI concentrada en un subconjunto de apps (7 de 23 módulos). | Medio | Media | Media | H09 |
| **Riesgo de ataques CSRF en vistas específicas** | Uso de `csrf_exempt` en varias vistas, posiblemente sin justificación documentada. | Alto | Media | **Alta** | H10 |
| **Diferencias entre ambientes de ejecución** | Dependencias divergentes entre archivos de requerimientos; referencias a ramas distintas de repositorios externos. | Medio | Media | Media | H11 |
| **Dificultad de mantenimiento evolutivo** | Deuda técnica histórica, compatibilidad antigua, comandos/rutas comentadas sin documentación de estado. | Medio | Media | Media | H12 |
| **Complejidad para modificar dominios funcionales** | Monolito modular con acoplamientos entre apps; cambios en dominios requieren coordinación cruzada. | Medio | Media | Media | H01, H02, H04 |

---

## 8. Respuestas a preguntas orientadoras

### 8.1 ¿Qué tan centralizado o fragmentado está el sistema?

El sistema presenta una combinación de centralización y fragmentación. Por un lado, está centralizado porque funciona como un único proyecto Django, con configuración principal en `src/server/eieinfo/settings.py` y ruteo raíz en `src/server/eieinfo/urls.py`. Esta estructura confirma una arquitectura monolítica, donde varios dominios funcionales comparten el mismo proyecto, configuración, despliegue y base de datos.

Por otro lado, también existe fragmentación funcional, ya que el sistema está dividido en múltiples apps Django como `estudiantes`, `profesores`, `cursos`, `inventario`, `firma_digital`, `postulaciones` y `trabajo_final_de_graduacion`. Esta modularización ayuda a separar áreas funcionales, pero la presencia de importaciones directas entre apps muestra que la separación no es completamente independiente.

### 8.2 ¿Cuáles módulos concentran más responsabilidad?

Los módulos que parecen concentrar más responsabilidad son `estudiantes`, `profesores`, `cursos`, `firma_digital`, `inventario` y `trabajo_final_de_graduacion`. Esto se observa por la cantidad de rutas, subrutas, modelos y funciones asociadas a cada uno.

El módulo `estudiantes` concentra funcionalidades relacionadas con portal de estudiante, cursos, asistencias, trámites, bodega, proyecto eléctrico y práctica profesional. El módulo `profesores` concentra cursos, cátedras, proyectos, publicaciones, laboratorios, consejo asesor y asistencias. `trabajo_final_de_graduacion` también representa un dominio importante porque contiene modelos asociados a TFG, comités, revisiones, defensas y documentos complementarios.

### 8.3 ¿Qué partes parecen más activas y cuáles más legadas?

Las partes más activas parecen ser las relacionadas con módulos funcionales principales: rutas de estudiantes, profesores, firma digital, inventario, postulaciones y trabajos finales. Estas áreas tienen rutas, vistas, modelos y funcionalidades observables dentro del repositorio.

Las partes más legadas se observan en señales de compatibilidad histórica y comentarios dentro del código. Por ejemplo, `src/server/eieinfo/settings.py` conserva referencias históricas asociadas a Django 1.9.1 (línea 4), mientras que existen rutas o comandos comentados en archivos como `src/server/estudiantes/urls.py` (líneas 32-33) y `.drone.yml` (líneas 366-383). Esto sugiere que el sistema ha evolucionado con el tiempo y conserva decisiones o fragmentos asociados a etapas anteriores del desarrollo.

### 8.4 ¿Qué señales hay de crecimiento orgánico y deuda técnica?

Una señal clara de crecimiento orgánico es la cantidad de apps funcionales registradas dentro del mismo proyecto Django (23 apps). El sistema parece haber incorporado nuevas áreas conforme surgieron necesidades institucionales, por ejemplo estudiantes, profesores, cursos, inventario, postulaciones, firma digital y trabajos finales.

También se observan señales de deuda técnica en el acoplamiento entre dominios, el ruteo raíz extenso, la ausencia de migraciones versionadas reales, la eliminación y regeneración dinámica de migraciones durante ejecución, dependencias divergentes entre ambientes y configuración dependiente del hostname. Estas condiciones no necesariamente impiden el funcionamiento del sistema, pero sí pueden aumentar el costo de mantenimiento y dificultar una evolución controlada.

### 8.5 ¿Dónde se observan los primeros riesgos de mantenimiento o evolución?

Los primeros riesgos de mantenimiento o evolución se observan principalmente en **cinco áreas críticas**:

1. **Seguridad (Crítico):** Contraseñas y credenciales visibles en el repositorio sin protección.
2. **Despliegue (Crítico):** El sistema puede iniciarse en modo inseguro si el hostname no es `faraday`.
3. **Base de datos:** No se encontraron migraciones versionadas reales; se eliminan y regeneran automáticamente durante ejecución.
4. **Confiabilidad:** Pruebas incompletas cubren solo 7 de 23 apps registradas.
5. **Mantenibilidad modular:** Existen importaciones directas entre apps y un ruteo raíz extenso, aumentando el riesgo de regresiones cruzadas.

Estos riesgos deben analizarse con mayor profundidad en la siguiente etapa de auditoría.

---

## 9. Conclusiones

EIEInfo es una aplicación web institucional amplia, construida como un proyecto Django monolítico con modularización por apps. La estructura permite separar áreas funcionales como estudiantes, profesores, cursos, inventario, postulaciones y firma digital. Sin embargo, la evidencia muestra acoplamientos frecuentes entre dominios mediante importaciones directas y una concentración importante de rutas en el archivo principal del proyecto.

Los riesgos más relevantes del diagnóstico inicial se concentran en **tres áreas de urgencia crítica**:

1. **Exposición de credenciales:** Contraseñas de SMTP y posibles secretos adicionales están codificados directamente en settings.py sin protección.
2. **Despliegue inseguro:** La configuración de DEBUG y ALLOWED_HOSTS depende de una comparación de hostname que puede fallar, exponiendo la aplicación en modo desarrollo.
3. **Reproducibilidad de base de datos:** La ausencia de migraciones versionadas junto con scripts que eliminan y regeneran migraciones durante ejecución o preparación constituye un punto crítico para la evolución controlada del modelo de datos.

Adicionalmente, la cobertura de pruebas incompleta, el acoplamiento entre módulos y la deuda técnica histórica constituyen riesgos importantes aunque de menor urgencia inmediata.

Para las siguientes fases de auditoría, conviene profundizar en:
- Dependencias entre módulos y análisis de impacto de cambios.
- Flujos de autenticación y autorización.
- Alcance real de las pruebas y cobertura por módulo.
- Manejo de configuración sensible y estrategia de rotación de secretos.
- Estrategia de migraciones, despliegue y rollback de cambios en base de datos.

---

## 10. Anexos

### Anexo A. Evidencia de migraciones

Se encontraron carpetas `migrations/` en las siguientes apps:

- `src/server/administrativos/migrations/`
- `src/server/alumni/migrations/`
- `src/server/anuncios/migrations/`
- `src/server/asistencias/migrations/`
- `src/server/atributos/migrations/`
- `src/server/conferencias/migrations/`
- `src/server/cursos/migrations/`
- `src/server/estudiantes/migrations/`
- `src/server/eventos/migrations/`
- `src/server/firma_digital/migrations/`
- `src/server/inventario/migrations/`
- `src/server/laboratorios/migrations/`
- `src/server/postulaciones/migrations/`
- `src/server/profesores/migrations/`
- `src/server/proyectos/migrations/`
- `src/server/practica_profesional/migrations/`
- `src/server/proyecto_electrico/migrations/`
- `src/server/trabajo_final_de_graduacion/migrations/`
- `src/server/trabajos_finales/migrations/`
- `src/server/webpage/migrations/`

Cada carpeta contiene únicamente `__init__.py`. No se encontraron archivos `0001_initial.py` ni `0002_*.py`. Esta es evidencia por ausencia de archivos reales de migración.

**Nota importante:** Se observa que `practica_profesional` y `proyecto_electrico` tienen carpetas `migrations/` pero **no están registradas en `INSTALLED_APPS`** en `settings.py`, lo que constituye una inconsistencia adicional.

### Anexo B. Comandos de migración observados

| Archivo | Línea | Comando | Contexto |
|---|---:|---|---|
| `.drone.yml` | 99 | `find . -path "./src/server/*/migrations/*.py" -not -name "__init__.py" -delete` | **Elimina todas las migraciones** |
| `.drone.yml` | 100 | `python src/server/manage.py makemigrations` | Regenera migraciones desde modelos |
| `.drone.yml` | 101 | `python src/server/manage.py showmigrations` | Muestra estado de migraciones |
| `docker/django/migraciones.sh` | 5-6 | `find . -path "./src/server/*/migrations/*.py" -not -name "__init__.py" -delete` | **Elimina todas las migraciones** |
| `docker/django/migraciones.sh` | 7 | `python src/server/manage.py makemigrations` | Regenera migraciones desde modelos |
| `docker/django/migraciones.sh` | 8 | `python src/server/manage.py showmigrations` | Muestra estado de migraciones |
| `docker/django/migraciones.sh` | 9 | `python src/server/manage.py migrate --noinput` | Aplica migraciones a la base de datos |
| `docker-compose.yml` | 65 | Ejecuta `/home/eieinfo/info/migraciones.sh` | Se ejecuta al iniciar el contenedor |

### Anexo C. Configuración sensible visible

En `src/server/eieinfo/settings.py` se encontró configuración sensible sin protección:

```python
# Línea 340-344: Configuración SMTP con credenciales en claro
EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
EMAIL_HOST = 'smtp.eie.ucr.ac.cr'
EMAIL_HOST_USER = 'eieinfo'
EMAIL_HOST_PASSWORD = 'lyZB6A8fdx'  # ⚠️ CONTRASEÑA EN CLARO
```


### Anexo D. Evidencia adicional de configuración sensible

Durante la revisión del archivo de configuración principal se observó que existen referencias a configuración sensible y a archivos externos de credenciales. Por seguridad, no se reproducen valores sensibles en este documento.

En `src/server/eieinfo/settings.py`, línea 525, se observa una importación hacia un archivo de credenciales:

```python
from eieinfo.secret.secret_credentials import *  # Posibles secretos adicionales
```

Esta evidencia refuerza el hallazgo H07, ya que muestra que parte de la configuración sensible del sistema se encuentra referenciada desde el código de configuración principal.

---

### Anexo E. Archivos principales revisados

Los principales archivos revisados durante el levantamiento inicial fueron:

- `src/server/eieinfo/settings.py`
- `src/server/eieinfo/urls.py`
- `src/server/estudiantes/urls.py`
- `src/server/estudiantes/models.py`
- `src/server/profesores/urlpatterns/`
- `src/server/trabajo_final_de_graduacion/models.py`
- `src/server/*/models.py`
- `src/server/*/tests.py`
- `src/server/*/views.py` para revisión de uso de `csrf_exempt`
- `requirements.txt`
- `docker/django/requirements.txt`
- `docker-compose.yml`
- `docker/django/migraciones.sh`
- `.drone.yml`

---

## 11. Notas metodológicas

Este levantamiento sigue el marco de evaluación establecido en el curso IE0417, Diseño de Software para Ingeniería, y busca:

- Construir una comprensión técnica sólida del sistema y responder: ¿qué sistema tenemos enfrente y cuáles son sus principales rasgos de diseño?
- Sustentar todas las afirmaciones importantes con evidencia verificable del repositorio.
- Separar hallazgos importantes de observaciones menores mediante criterio técnico.
- Priorizar riesgos de acuerdo con su impacto en mantenibilidad, seguridad y evolución futura.

En fases posteriores de la auditoría se profundizará en la evaluación por atributos de calidad, análisis modular, auditoría del dominio, cobertura de pruebas y propuesta de evolución estratégica del sistema.

---

## 12. Estructura del proyecto de auditoría

Este documento corresponde a la **Entrega 1: Levantamiento del sistema y diagnóstico inicial**.

La auditoría completa se desarrollará en tres entregas:

- **Entrega 1, semana 1:** Levantamiento del sistema y diagnóstico inicial. Este documento.
- **Entrega 2, semana 2:** Auditoría profunda de diseño, calidad y riesgos.
- **Entrega 3, semana 3:** Informe final de auditoría y propuesta de evolución.

Cada entrega se basa en la anterior, construyendo un análisis progresivo del sistema EIEInfo.
