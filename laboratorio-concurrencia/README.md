# Laboratorio de programación concurrente y paralela en C++

## Estudiante

**Nombre:** Erick Vargas Monge  
**Carné:** C08215  

---

## Descripción

Este laboratorio explora conceptos básicos de programación concurrente y paralela usando C++. Se trabajan hilos, condiciones de carrera, mutex, benchmarks, cantidad de hilos versus rendimiento y deadlocks.

El objetivo principal es observar de forma práctica cómo se comporta un programa cuando utiliza varios hilos, qué problemas pueden aparecer al compartir datos entre ellos y cómo se pueden corregir mediante mecanismos de sincronización.

---

## Índice

- [Requisitos](#requisitos)
- [Estructura del proyecto](#estructura-del-proyecto)
- [Compilación y ejecución](#compilación-y-ejecución)
  - [Parte 1: Creación básica de hilos](#parte-1-creación-básica-de-hilos)
  - [Parte 2: Condición de carrera](#parte-2-condición-de-carrera)
  - [Parte 3: Corrección usando mutex](#parte-3-corrección-usando-mutex)
  - [Parte 4: Mini benchmark secuencial vs. paralelo](#parte-4-mini-benchmark-secuencial-vs-paralelo)
  - [Parte 5: Cantidad de hilos vs. rendimiento](#parte-5-cantidad-de-hilos-vs-rendimiento)
  - [Parte 6: Ejemplo simple de deadlock](#parte-6-ejemplo-simple-de-deadlock)
  - [Parte 6 corregida: Solución del deadlock](#parte-6-corregida-solución-del-deadlock)
- [Resultados y análisis](#resultados-y-análisis)
- [Resumen de temas trabajados](#resumen-de-temas-trabajados)
- [Conclusión](#conclusión)

---

## Requisitos

Para compilar y ejecutar los programas de este laboratorio se requiere:

- `g++`
- C++17 o superior
- Sistema operativo Linux, macOS o Windows con soporte para compilación C++
- Terminal
- Git

Para verificar la versión de `g++`, se puede usar:

```bash
g++ --version
```

---

## Estructura del proyecto

```text
laboratorio-concurrencia/
├── README.md
├── src/
│   ├── parte1_threads.cpp
│   ├── parte2_race_condition.cpp
│   ├── parte3_mutex.cpp
│   ├── parte4_benchmark.cpp
│   ├── parte5_hilos_vs_rendimiento.cpp
│   ├── parte6_deadlock.cpp
│   └── parte6_deadlock_corregido.cpp
└── resultados/
    ├── analisis.md
    └── images/
```

---

## Compilación y ejecución

Todos los comandos deben ejecutarse desde la carpeta principal del laboratorio:

```bash
laboratorio-concurrencia/
```

---

## Parte 1: Creación básica de hilos

### Archivo

```text
src/parte1_threads.cpp
```

### Compilación

```bash
g++ -std=c++17 -Wall -Wextra -pthread src/parte1_threads.cpp -o parte1
```

### Ejecución

```bash
./parte1
```

### Descripción

En esta parte se crean varios hilos usando `std::thread`. Cada hilo ejecuta una función que imprime un mensaje con su identificador. El programa principal espera a que todos los hilos finalicen usando `join()`.

---

## Parte 2: Condición de carrera

### Archivo

```text
src/parte2_race_condition.cpp
```

### Compilación

```bash
g++ -std=c++17 -Wall -Wextra -pthread src/parte2_race_condition.cpp -o parte2
```

### Ejecución

```bash
./parte2
```

### Descripción

En esta parte varios hilos incrementan una variable global compartida llamada `contador`. Como no se utiliza ningún mecanismo de sincronización, se puede observar una condición de carrera.

---

## Parte 3: Corrección usando mutex

### Archivo

```text
src/parte3_mutex.cpp
```

### Compilación

```bash
g++ -std=c++17 -Wall -Wextra -pthread src/parte3_mutex.cpp -o parte3
```

### Ejecución

```bash
./parte3
```

### Descripción

Esta parte corrige la condición de carrera de la Parte 2 usando `std::mutex` y `std::lock_guard`. Con esto se protege la sección crítica donde se modifica el contador compartido.

---

## Parte 4: Mini benchmark secuencial vs. paralelo

### Archivo

```text
src/parte4_benchmark.cpp
```

### Compilación

```bash
g++ -std=c++17 -O2 -Wall -Wextra -pthread src/parte4_benchmark.cpp -o parte4
```

### Ejecución

```bash
./parte4
```

### Descripción

En esta parte se compara el tiempo de ejecución de una suma secuencial contra una suma paralela. El vector se divide en bloques y cada hilo calcula una suma parcial. Luego se combinan los resultados.

Se utiliza la opción `-O2` para permitir optimizaciones del compilador.

---

## Parte 5: Cantidad de hilos vs. rendimiento

### Archivo

```text
src/parte5_hilos_vs_rendimiento.cpp
```

### Compilación

```bash
g++ -std=c++17 -O2 -Wall -Wextra -pthread src/parte5_hilos_vs_rendimiento.cpp -o parte5
```

### Ejecución

```bash
./parte5
```

### Descripción

Esta parte prueba la suma paralela usando diferentes cantidades de hilos:

```text
1, 2, 4, 8 y 16 hilos
```

El objetivo es observar que aumentar la cantidad de hilos no siempre mejora el rendimiento.

Para conocer la cantidad de núcleos disponibles en Linux, se puede usar:

```bash
nproc
```

También se puede usar:

```bash
lscpu
```

---

## Parte 6: Ejemplo simple de deadlock

### Archivo

```text
src/parte6_deadlock.cpp
```

### Compilación

```bash
g++ -std=c++17 -Wall -Wextra -pthread src/parte6_deadlock.cpp -o parte6
```

### Ejecución

```bash
./parte6
```

### Descripción

Este programa muestra un ejemplo simple de deadlock. Dos hilos intentan tomar dos recursos en diferente orden. Como cada hilo obtiene un recurso y luego espera por el otro, el programa queda bloqueado.

Este programa está diseñado para no terminar normalmente. Para detenerlo manualmente se usa:

```text
Ctrl + C
```

---

## Parte 6 corregida: Solución del deadlock

### Archivo

```text
src/parte6_deadlock_corregido.cpp
```

### Compilación

```bash
g++ -std=c++17 -Wall -Wextra -pthread src/parte6_deadlock_corregido.cpp -o parte6_corregido
```

### Ejecución

```bash
./parte6_corregido
```

### Descripción

Esta versión corrige el problema de deadlock usando `std::scoped_lock`, el cual permite bloquear varios mutex de forma segura.

A diferencia del programa anterior, esta versión sí debe terminar normalmente.

---

## Resultados y análisis

Las respuestas a las preguntas de análisis, tablas de resultados, observaciones y reflexión final se encuentran en:

```text
resultados/analisis.md
```

Ese archivo contiene el desarrollo de:

- Parte 1: análisis del orden de ejecución de hilos.
- Parte 2: resultados de la condición de carrera.
- Parte 3: corrección usando mutex.
- Parte 4: comparación secuencial vs. paralela.
- Parte 5: análisis de cantidad de hilos vs. rendimiento.
- Parte 6: deadlock y versión corregida.
- Parte 7: reflexión final del laboratorio.

---

## Resumen de temas trabajados

Durante el laboratorio se trabajaron los siguientes temas:

- Creación de hilos con `std::thread`.
- Uso de `join()` para esperar la finalización de hilos.
- Condiciones de carrera.
- Variables compartidas.
- Uso de `std::mutex`.
- Uso de `std::lock_guard`.
- Benchmark básico con `std::chrono`.
- Comparación entre ejecución secuencial y paralela.
- Impacto de la cantidad de hilos en el rendimiento.
- Context switching.
- Deadlocks.
- Corrección de deadlock con `std::scoped_lock`.

---

## Conclusión

Este laboratorio permitió observar de forma práctica cómo funciona la programación concurrente y paralela en C++. Al crear hilos con `std::thread`, se pudo ver que el orden de ejecución no siempre es predecible.

También se observó que compartir datos entre varios hilos puede producir errores si no se usa sincronización. La condición de carrera de la Parte 2 fue corregida en la Parte 3 mediante `std::mutex` y `std::lock_guard`.

Además, los benchmarks mostraron que el paralelismo puede mejorar el rendimiento en tareas grandes, pero también que usar más hilos no siempre produce mejores tiempos. Finalmente, el ejemplo de deadlock permitió entender que la sincronización debe aplicarse con cuidado, ya que un mal orden al tomar recursos puede bloquear completamente el programa.