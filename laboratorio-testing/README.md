# Laboratorio de testing en C++

## Estudiante

 Erick Vargas Monge  
 C08215

---

## Descripción

Este laboratorio desarrolla conceptos básicos de software testing aplicado a proyectos en C++.

Se trabajó con pruebas unitarias usando Google Test, pruebas funcionales, casos borde, pruebas fallidas, cobertura de código con LCOV e integración continua con GitHub Actions.

El objetivo principal fue comprobar el comportamiento de funciones individuales, detectar errores mediante pruebas automatizadas y ejecutar dichas pruebas tanto de forma local como automáticamente en GitHub.

---

## Herramientas utilizadas

- C++
- CMake
- Google Test
- CTest
- LCOV
- GitHub Actions
- Git
- Ubuntu/Linux

---

## Estructura del proyecto

```text
laboratorio-testing/
├── .github/
│   └── workflows/
│       └── testing.yml
├── docs/
│   ├── parte1-plan-pruebas.md
│   ├── parte2-pruebas-unitarias.md
│   ├── parte3-pruebas-fallidas.md
│   ├── parte4-casos-borde.md
│   ├── parte5-cobertura.md
│   ├── parte6-github-actions.md
│   └── reflexion-final.md
├── images/
│   └── capturas del laboratorio
├── include/
│   ├── calculator.h
│   ├── string_utils.h
│   └── grade_utils.h
├── src/
│   ├── calculator.cpp
│   ├── string_utils.cpp
│   └── grade_utils.cpp
├── tests/
│   ├── test_calculator.cpp
│   ├── test_string_utils.cpp
│   └── test_grade_utils.cpp
├── CMakeLists.txt
└── README.md
```

---

## Módulos implementados

### `calculator`

Incluye funciones matemáticas básicas:

- Suma
- Resta
- Multiplicación
- División
- Verificación de números pares

### `string_utils`

Incluye funciones para trabajar con cadenas de texto:

- Convertir texto a mayúsculas
- Verificar palíndromos
- Contar vocales

### `grade_utils`

Incluye funciones relacionadas con notas:

- Calcular promedio
- Verificar si una nota es aprobada
- Convertir nota numérica a letra
- Validar si una nota está dentro del rango permitido

---

## Comandos principales

Para compilar y ejecutar las pruebas localmente:

```bash
cd laboratorio-testing
mkdir build
cd build
cmake ..
make
./run_tests
```

También se pueden ejecutar las pruebas con CTest:

```bash
ctest --output-on-failure
```

---

## Comandos para cobertura de código

Para generar el reporte de cobertura:

```bash
cd laboratorio-testing
rm -rf build
mkdir build
cd build
cmake -DENABLE_COVERAGE=ON ..
make
./run_tests
lcov --capture --directory CMakeFiles/project_lib.dir/src --output-file coverage.info --ignore-errors mismatch,gcov
genhtml coverage.info --output-directory coverage_report
xdg-open coverage_report/index.html
```

---

## Índice de documentación

- [Parte 1: Plan de pruebas](docs/parte1-plan-pruebas.md)
- [Parte 2: Pruebas unitarias](docs/parte2-pruebas-unitarias.md)
- [Parte 3: Pruebas fallidas](docs/parte3-pruebas-fallidas.md)
- [Parte 4: Casos borde y semillas](docs/parte4-casos-borde.md)
- [Parte 5: Cobertura de código](docs/parte5-cobertura.md)
- [Parte 6: GitHub Actions](docs/parte6-github-actions.md)
- [Reflexión final](docs/reflexion-final.md)

---

## Resumen de resultados

Durante el laboratorio se implementaron y ejecutaron pruebas automatizadas para los módulos del proyecto.

Se verificaron casos normales, casos borde y casos inválidos. También se provocaron fallos intencionales para observar cómo Google Test y GitHub Actions reportan errores.

Al final del laboratorio, las pruebas pasaron correctamente de forma local y también en GitHub Actions.

---

## GitHub Actions

Se configuró un workflow en:

```text
.github/workflows/testing.yml
```

Este workflow se ejecuta automáticamente cuando se realiza un `push` o un `pull request` hacia la rama `main`.

El workflow realiza los siguientes pasos:

1. Descarga el repositorio.
2. Instala dependencias.
3. Configura el proyecto con CMake.
4. Compila el proyecto.
5. Ejecuta las pruebas con `./run_tests`.
6. Ejecuta las pruebas con `ctest --output-on-failure`.

Esto permite validar automáticamente que el proyecto compile y que las pruebas pasen correctamente.

---

## Evidencia

Las capturas utilizadas como evidencia del laboratorio se encuentran en la carpeta:

```text
images/
```

Estas imágenes muestran la ejecución de comandos, resultados de pruebas, reporte de cobertura y ejecuciones de GitHub Actions.

---

## Conclusión

Este laboratorio permitió aplicar conceptos importantes de testing en C++.

Se aprendió a diseñar pruebas unitarias y funcionales, identificar casos borde, interpretar pruebas fallidas, medir cobertura de código y automatizar la ejecución de pruebas con GitHub Actions.

El uso de pruebas automatizadas facilita detectar errores temprano, mantener el código más confiable y mejorar el proceso de desarrollo de software.
