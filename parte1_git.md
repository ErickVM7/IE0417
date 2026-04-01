
## Laboratorio 1 — Control de versiones (Git)  
Curso: IE0417 – Diseño de Software para Ingeniería  
Erick Vargas Monge C08215 
Profesor: Rafael Esteban Badilla Alvarado  
I-2026  

---

# 1. Introducción

En este laboratorio se utilizaron de manera práctica los comandos fundamentales del sistema de control de versiones **Git**, mediante la plataforma interactiva **Learn Git Branching**.El objetivo fue comprender cómo Git maneja el historial, las ramas, los merges y las operaciones avanzadas como *rebase*, *reset* y *cherry-pick*, aplicadas en escenarios simulados. Estos escenarios se hacen de manera interactiva y visualmente intutivas para observar como funciona el git.


`![Main 1](./imagenes/intro.png)` 


---

# 2. Miveles completados

Se realizaron todos los niveles de la página. En un inicio no se logró realizar la solución más optima, por lo que después se repitió los niveles para lograrlo.

`![Main 1](./imagenes/All1.png)` 

`![Main 1](./imagenes/All2.png)` 

## 2.1. Sección Main

Incluye:
- Introducción a commits  
- Uso de *branch* y *checkout*  
- Merges  
- Rebase  
- Cherry-pick  
- Reset / Revert  
- Estado *detached HEAD*

`![Main 1](./imagenes/1-1.png)`
`![Main 1](./imagenes/1-2.png)`
`![Main 1](./imagenes/1-3.png)`
`![Main 1](./imagenes/1-4.png)`
`![Main 1](./imagenes/1-5.png)`
 

---

## 2.2. Sección Remote

Incluye:
- clone  
- remote add  
- push  
- pull  
- manejo de origin  
- operaciones avanzadas con repos remotos

`![Main 1](./imagenes/2-1.png)`

---

# 3. Conceptos aprendidos

### ✔ Commit
Un commit representa un punto en la historia del proyecto.  
Aprendí a crear commits, moverme entre ellos con `checkout` y usarlos como referencia para otras operaciones.

### ✔ Branch
Las ramas permiten desarrollar nuevas funcionalidades sin afectar la rama principal.  
Aprendí a crear ramas, cambiar entre ellas y visualizar cómo divergen.

### ✔ Merge
Fusiona el trabajo de dos ramas. Aprendí la diferencia entre:
- *Fast-forward*
- *Merge con commit de fusión*

### ✔ Rebase
Reescribe la historia colocando commits encima de otra rama.  
Es útil para mantener una historia limpia y lineal.

### ✔ Cherry-pick
Permite traer commits específicos desde otra rama sin fusionar toda la rama completa.

### ✔ Reset / Revert
- **Reset:** mueve el puntero de la rama hacia un commit anterior (reescribe la historia).  
- **Revert:** crea un commit nuevo que deshace cambios pasados (seguro para repos compartidos).

### ✔ HEAD y Detached HEAD
- **HEAD** apunta a la rama activa.  
- El estado *detached HEAD* ocurre cuando se trabaja directamente sobre un commit y no sobre una rama.

### ✔ Manejo de remotos
Aprendí a clonar repositorios, añadir remotos, subir cambios (`push`) y descargar cambios (`pull`).  
También comprendí la relación entre ramas locales y remotas (upstream tracking).

---

# 4. Dificultades encontradas

A pesar de que ya tenia experiencia con Git, durante la práctica surgieron algunos desafíos, entre ellos:

- Diferenciar cuándo es mejor usar **merge** y cuándo **rebase**.
- Comprender el estado **detached HEAD** y cómo volver a una rama activa.
- Resolver niveles que requerían una combinación de comandos avanzados (reset + rebase + cherry-pick).
- Resolver los problemas con la mejor solución.

Esto se debe a que esos comandos son algo más avanzados a los que estoy acostumbrado de usar. 

---

# 5. ¿Por qué Git es importante en proyectos reales?

En los proyectos actuales Git es fundamental para lograr un buen desarrollo del mismo. Permite trabajar en equipo sin sobrescribir código o "caerle" encima de otra persona. Mantiene un historial claro y detallado, lo que permite tener un trabajo organizado. Justamente eso ultimo, facilita revertir errores y recuperar versiones. Permite múltiples líneas de desarrollo mediante ramas. Soporta integración continua y despliegues modernos. A día de hoy, es estándar en la industria. En proyectos reales Git garantiza orden, trazabilidad, colaboración eficiente y un flujo de trabajo seguro.

