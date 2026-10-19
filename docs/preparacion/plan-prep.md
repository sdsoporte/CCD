# Plan de preparación — 2026-10-20 → 2027-03-10

Objetivo: llegar al primer cuatrimestre de 2027 con las tres materias que más
gente filtra ya resueltas en la práctica, sin inventar tiempo que no existe.

## La cuenta de horas, antes de cualquier contenido

| Recurso | Valor |
| --- | --- |
| Ventana de preparación | 20 semanas (2026-10-20 → 2027-03-10) |
| Compromiso semanal ya tomado (Coach, semestre 1) | 16 h/semana |
| Horas que se pueden sumar sin romper lo actual | **4 h/semana** |
| Presupuesto total de preparación | **≈ 80 h** |

No hay margen para 10 h/semana: el semestre de AI corre hasta el 2027-03-11 y
está contratado a 16 h. La preparación se financia con disciplina, no con
optimismo. Si aparece un receso real de verano, ahí sí hay un tramo extra
opcional (ver *Tramo B*).

## Regla de asignación

Todo lo que se estudie antes de marzo tiene que caer en una de estas dos
categorías, o no se estudia:

1. **Materia del primer año con alta tasa de recursado** → Análisis Matemático I,
   Álgebra I, Sistemas Operativos I, Infraestructura de Telecomunicaciones.
2. **Herramienta que se usa en 4 años de materias prácticas** → Linux, redes, SQL.

Estudiar *Ciberseguridad aplicada*, *Forense* o *Criptografía* ahora es tirar
horas: llegan en segundo, tercer y cuarto año, y sin base se olvidan.

## Reparto de las 80 horas

| Bloque | Horas | Por qué |
| --- | --- | --- |
| 0. Diagnóstico | 3 | Calibrar dónde estás parado antes de elegir textos |
| 1. Matemática (Análisis I + Álgebra I) | 40 | El filtro del primer año; alimenta Análisis II, Probabilidad, Investigación Operativa y Criptografía |
| 2. Redes e infraestructura de telecomunicaciones | 20 | Materia 7 y base de Programación Segura (16), PCI (21) e IoT (18) |
| 3. Linux a nivel sistema operativo | 10 | Materias 5 y 9; base de Forense (25) y de todo el laboratorio |
| 4. SQL y modelo de datos | 7 | Materia 6, con correlativa directa de 3 |
| **Total** | **80** | |

Nada de inglés: ya está cubierto por el track *English for AI Architects*
(480 min/semana), que sirve igual para Inglés I y II. Ese es el primer punto
donde los dos programas se solapan y no se suman.

## Bloque 0 — Diagnóstico (3 h, semana 1)

Se hace una sola vez, y define si el reparto de arriba se mueve.

- **Matemática (2 h)**: resolver sin mirar apuntes — límite con indeterminación,
  derivada de función compuesta, sistema de 3×3 por Gauss, autovalores de una
  matriz 2×2. Si salen cuatro de cuatro, se recortan 15 h de matemática y se
  mueven a redes y Linux.
- **Redes (1 h)**: dado `192.168.10.0/24`, dividir en 6 subredes y decir rangos
  utilizables. Si no sale, redes arranca por subnetting puro.

Criterio de salida: saber exactamente cuántas horas van a cada bloque.

## Bloque 1 — Matemática (40 h)

Formato: 2 sesiones de 2 h por semana, con ejercicios escritos a mano. Sin
ejercicios no hay matemática, hay lectura.

**Análisis Matemático I (22 h)**
- Funciones: dominio, imagen, composición, inversa, exponencial y logaritmo.
- Límites y continuidad: definición, indeterminaciones, asíntotas.
- Derivada: reglas, regla de la cadena, derivación implícita.
- Aplicaciones: recta tangente, crecimiento, extremos, optimización, L'Hôpital.
- Teoremas del valor medio (Rolle, Lagrange) y serie de Taylor.

**Álgebra I (18 h)**
- Matrices, operaciones, determinantes, inversa.
- Sistemas lineales: eliminación de Gauss, Gauss-Jordan, clasificación.
- Espacios vectoriales, base y dimensión.
- Transformaciones lineales y matrices asociadas.
- Autovalores y autovectores, diagonalización.
- Números complejos: forma binómica, polar, raíces.

**Material**: *Cálculo* de Stewart (tomo 1) o *Cálculo* de Piskunov para el
enfoque clásico; MIT OCW 18.01 y 18.06 para clases en video y listas de
ejercicios; *Álgebra Lineal* de Grossman o Lay para práctica adicional.
Para intuición previa, la serie *Essence of Calculus* / *Essence of Linear
Algebra* de 3Blue1Brown (no reemplaza ejercicios).

**Criterio de salida**: resolver un parcial de Análisis I y uno de Álgebra I en
tiempo, sin apuntes.

## Bloque 2 — Redes (20 h)

**Contenido**
- Modelo OSI y TCP/IP: qué hace cada capa y qué se rompe en cada una.
- Direccionamiento IPv4, subnetting CIDR y VLSM (mucho ejercicio).
- Ethernet, switching, VLANs, ARP.
- Routing: tabla de rutas, estático, concepto de protocolo dinámico.
- TCP vs UDP, puertos, NAT, firewall básico.
- Captura y lectura: `tcpdump` y Wireshark sobre tráfico propio.

**Material**: *Computer Networking: A Top-Down Approach* (Kurose & Ross),
capítulos 1 a 4. Práctica: subnetting a mano todos los días 10 minutos;
laboratorio con máquinas virtuales (ver `../equipo/02-laboratorio.md`), no solo
Packet Tracer.

**Criterio de salida**: explicar por qué un paquete no llega, capa por capa, y
resolver un esquema de subredes de un examen típico.

## Bloque 3 — Linux como sistema operativo (10 h)

No es "usar la terminal": es entender qué hace el kernel.

- Procesos: `fork`/`exec`, estados, señales, `ps`, prioridades, `nohup`.
- Sistema de archivos: inodos, permisos, ACL, enlaces, `mount`, LVM.
- Memoria: `free`, swap, page cache, OOM killer, `/proc/meminfo`.
- Arranque y servicios: systemd, unidades, `journalctl`, targets.
- Aislamiento: namespaces y cgroups (la base de contenedores y de VMs).
- Shell scripting serio: `set -euo pipefail`, `find`, `xargs`, `awk`, `sed`.

**Material**: *Operating Systems: Three Easy Pieces* (gratuito, OSTEP) para la
parte conceptual; *The Linux Command Line* de Shotts para la práctica; `man` y
`/proc` como fuente primaria.

**Criterio de salida**: dado un proceso colgado o un servicio que no arranca,
diagnosticarlo con herramientas del sistema, sin buscar la respuesta en internet.

## Bloque 4 — SQL y datos (7 h)

- Modelo relacional, claves, integridad referencial.
- SQL: `SELECT`, joins, agregación, subconsultas.
- Índices: qué aceleran y qué cuestan.
- Transacciones y normalización básica.

**Material**: SQLBolt para práctica guiada, tutorial oficial de PostgreSQL,
*Use The Index, Luke* para índices. Motor local: PostgreSQL en contenedor, para
no ensuciar el sistema.

**Criterio de salida**: modelar y consultar una base chica de un dominio real
(por ejemplo, inventario de equipos del laboratorio).

## Tramo B — verano, solo si hay receso real (opcional)

Solo si en enero/febrero hay dos semanas sin trabajo ni cursada: 20 h extra a
**matemática aplicada al programa**, no a temas nuevos de seguridad. Temas:
sucesiones y series, introducción a probabilidad (monedas, urnas, variable
aleatoria, esperanza), y aritmética modular módulo *n* — que es exactamente el
puente hacia Criptografía Aplicada en cuarto año.

Si no hay receso, el tramo no existe y no se compensa robándole horas al
semestre de AI.

## Trabajo de equipo, en paralelo (una sola vez, ≈ 12 h totales)

| Cuándo | Qué | Horas |
| --- | --- | --- |
| Noviembre 2026 | Decidir y comprar almacenamiento externo para el laboratorio | 1 |
| Noviembre 2026 | Montar volumen cifrado (LUKS) para el laboratorio | 3 |
| Diciembre 2026 | Red virtual aislada en libvirt + plantillas de VM | 4 |
| Enero 2027 | Primeras VMs del set (Linux server, Kali, Windows eval) | 4 |

Detalle en `../equipo/01-sistema-operativo.md` y `../equipo/02-laboratorio.md`. Estas
12 h no salen del presupuesto de 80: son instalación, no estudio, y se pueden
hacer un fin de semana.

## Cadencia de control

- **Semanal**: una sesión de 2 h de matemática + una de 2 h de redes/Linux.
  Registrar la ejecución en Coach (no en este repo).
- **Mensual**: un ejercicio de parcial completo, cronometrado, sin apuntes.
  Si el resultado empeora, se corta el bloque y se vuelve atrás.
- **2027-02-15**: checkpoint de decisión. Con los números en la mano, decidir
  qué pasa con el programa de AI cuando arranque la cursada (ver
  `../integracion-coach.md`, sección *Conflicto de capacidad*).

## Señales de que el plan está mal

- Se acumulan tres semanas sin tocar matemática → el bloque 1 está mal
  dimensionado o el compromiso semanal es irreal. Se recorta contenido, no
  frecuencia.
- Se está leyendo seguridad ofensiva antes de marzo → se está evitando el filtro
  real del primer año.
- El laboratorio crece más rápido que los apuntes → el laboratorio es una
  herramienta, no el objetivo.
