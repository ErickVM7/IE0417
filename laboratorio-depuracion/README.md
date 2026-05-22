# Laboratorio: Depuración con gdb, valgrind y sanitizers

## Estudiante

Erick Vargas Monge C08215 

---

## Descripción

Este laboratorio tiene como objetivo practicar técnicas básicas de depuración en programas escritos en C++. Se trabajan errores de sintaxis, errores lógicos, errores de tiempo de ejecución, análisis de memoria, sanitizers y problemas de concurrencia en programas multihilo.

Durante el laboratorio se utilizan herramientas como `g++`, `gdb`, `valgrind`, AddressSanitizer, ThreadSanitizer y Helgrind. El propósito principal es aprender a observar el comportamiento de un programa, identificar errores, corregirlos y documentar el proceso de depuración de forma ordenada.

---

## Herramientas utilizadas

- `g++`
- `gdb`
- `valgrind`
- AddressSanitizer
- ThreadSanitizer
- Helgrind
- Linux / Ubuntu
- Git
- Markdown

---

## Estructura del laboratorio

```text
laboratorio-depuracion/
├── README.md
├── parte1-instalacion.md
├── parte2-tipos-errores.md
├── parte3-gdb-basico.md
├── parte4-gdb-funciones.md
├── parte5-valgrind.md
├── parte6-asan.md
├── parte7-hilos.md
├── parte8-reto-y-reflexion.md
├── images/
│   ├── 1.png
│   ├── 2.png
│   ├── 3.png
│   ├── 4.png
│   ├── 5.png
│   ├── 6.png
│   ├── 7.png
│   ├── 8.png
│   ├── 9.png
│   └── 10.png
└── codigo/
    ├── prueba.cpp
    ├── 01_error_sintaxis.cpp
    ├── 02_error_logico.cpp
    ├── 03_gdb_basico.cpp
    ├── 04_gdb_funciones.cpp
    ├── 05_memory_leak.cpp
    ├── 06_invalid_access.cpp
    ├── 07_use_after_free.cpp
    ├── 08_race_condition.cpp
    ├── 09_race_condition_corregido.cpp
    └── 10_reto.cpp
```

---

## Índice del laboratorio

### [Parte 1: Instalación y verificación del ambiente](parte1-instalacion.md)

En esta sección se verifica que el ambiente de trabajo cuenta con las herramientas necesarias para compilar, ejecutar y depurar programas en C++.

Se documentan las versiones de:

- `g++`
- `gdb`
- `valgrind`

También se compila y ejecuta un programa de prueba para confirmar que el ambiente funciona correctamente.

---

### [Parte 2: Tipos de errores](parte2-tipos-errores.md)

En esta sección se analizan dos tipos de errores:

1. Error de sintaxis.
2. Error lógico.

El primer ejercicio muestra cómo un error de sintaxis impide que el programa compile correctamente. El segundo ejercicio muestra que un programa puede compilar y ejecutarse, pero producir un resultado incorrecto debido a un error en la lógica del cálculo.

---

### [Parte 3: Introducción a gdb](parte3-gdb-basico.md)

En esta sección se usa `gdb` para ejecutar un programa paso a paso.

Se practican comandos básicos como:

```gdb
break main
run
next
print
continue
quit
```

También se inspeccionan variables durante la ejecución del programa.

---

### [Parte 4: step, next y backtrace](parte4-gdb-funciones.md)

En esta sección se analiza un programa que produce una división entre cero.

Se usa `gdb` para entrar dentro de funciones mediante `step`, avanzar con `next`, inspeccionar variables y revisar la pila de llamadas con:

```gdb
backtrace
```

La corrección consiste en validar que no se divida entre cero.

---

### [Parte 5: Análisis de memoria con valgrind](parte5-valgrind.md)

En esta sección se usa `valgrind` para detectar errores de memoria.

Se trabajan dos ejercicios:

1. Pérdida de memoria.
2. Acceso fuera de límites.

En el primer caso, se corrige una fuga de memoria usando `delete[]`. En el segundo caso, se corrige un acceso fuera del arreglo modificando la condición del ciclo.

---

### [Parte 6: AddressSanitizer](parte6-asan.md)

En esta sección se usa AddressSanitizer para detectar un error de tipo `heap-use-after-free`.

El programa original libera memoria con `delete` y luego intenta usar esa memoria. La corrección consiste en mover el `delete` después de utilizar el valor almacenado.

---

### [Parte 7: Análisis de hilos y condiciones de carrera](parte7-hilos.md)

En esta sección se analiza una condición de carrera en un programa multihilo.

Se usan dos hilos que incrementan una variable global compartida. El programa original puede producir resultados inconsistentes porque ambos hilos modifican `contador` sin sincronización.

La corrección se realiza usando:

```cpp
std::mutex
std::lock_guard
```

También se analiza el programa con Helgrind y ThreadSanitizer.

---

### [Parte 8: Mini reto y reflexión final](parte8-reto-y-reflexion.md)

En esta sección se aplica lo aprendido durante el laboratorio en un programa con varios errores:

- Error lógico en el cálculo del mayor.
- Acceso fuera de límites.
- Pérdida de memoria.
- Error en el cálculo del promedio.

Se utilizan herramientas como ejecución normal, `valgrind`, AddressSanitizer y `gdb`.

Al final se incluye una reflexión general sobre lo aprendido durante el laboratorio.

---

## Comandos generales útiles

### Compilar un programa con símbolos de depuración

```bash
g++ -g -o programa codigo/archivo.cpp
```

### Ejecutar un programa

```bash
./programa
```

### Abrir un programa con gdb

```bash
gdb ./programa
```

### Analizar memoria con valgrind

```bash
valgrind --leak-check=full ./programa
```

### Compilar con AddressSanitizer

```bash
g++ -g -fsanitize=address -o programa_asan codigo/archivo.cpp
```

### Compilar un programa con hilos

```bash
g++ -g -pthread -o programa codigo/archivo.cpp
```

### Analizar hilos con Helgrind

```bash
valgrind --tool=helgrind ./programa
```

### Compilar con ThreadSanitizer

```bash
g++ -g -fsanitize=thread -pthread -o programa_tsan codigo/archivo.cpp
```

---

## Resumen de aprendizajes

Durante este laboratorio se aprendió que un programa puede compilar correctamente y aun así tener errores. Por esta razón, es importante utilizar herramientas de depuración y análisis para verificar el comportamiento real del programa.

Se practicó el uso de `gdb` para inspeccionar variables, avanzar paso a paso y revisar la pila de llamadas. También se usó `valgrind` para detectar pérdidas de memoria y accesos inválidos. AddressSanitizer permitió encontrar errores de memoria durante la ejecución, mientras que Helgrind ayudó a identificar condiciones de carrera en programas con hilos.

En general, este laboratorio permitió desarrollar una forma más ordenada de analizar errores en programas escritos en C++.

---

## Estado del laboratorio

- [x] Parte 1: Instalación y verificación del ambiente.
- [x] Parte 2: Tipos de errores.
- [x] Parte 3: Introducción a gdb.
- [x] Parte 4: step, next y backtrace.
- [x] Parte 5: Análisis de memoria con valgrind.
- [x] Parte 6: AddressSanitizer.
- [x] Parte 7: Análisis de hilos y condiciones de carrera.
- [x] Parte 8: Mini reto de depuración.
- [x] Reflexión final.

---

## Conclusión

El laboratorio permitió practicar diferentes herramientas de depuración y análisis en C++. Se observó que los errores pueden aparecer en distintas etapas: compilación, ejecución, manejo de memoria o concurrencia.

También se comprobó que herramientas como `gdb`, `valgrind`, AddressSanitizer y Helgrind facilitan la identificación de problemas que no siempre son evidentes al ejecutar un programa normalmente.

La principal enseñanza del laboratorio es que depurar no consiste solo en corregir errores, sino en entender por qué ocurren, cómo encontrarlos y cómo verificar que la solución aplicada realmente funciona.