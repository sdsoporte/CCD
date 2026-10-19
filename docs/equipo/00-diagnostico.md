# Diagnóstico de equipo (medido, 2026-10-19)

Todo lo de abajo se midió en la máquina, no se estimó.

## Hardware

| Componente | Real | Veredicto para la carrera |
| --- | --- | --- |
| CPU | Intel Core i5-1235U (2 P-cores + 8 E-cores / 12 hilos) | **Suficiente**. 3–4 VMs de 2 vCPU conviven sin drama |
| RAM | 30 GiB | **Suficiente y sobrante**. El límite no es la RAM |
| GPU | Intel Iris Xe (integrada), sin GPU dedicada | **Limitante solo en la materia 27** (IA y ML, 3er año, 80 h introductorias). CPU + nube gratuita alcanzan |
| Disco | WD SN530 NVMe 512 GB → 475,9 GB útiles, una sola partición raíz ext4 | **El cuello de botella real**. 196 GB libres |
| Red | Ethernet I219-LM (cableada, real) + Intel CNVi WiFi | **Buenas noticias**: hay NIC cableada. Permite segmentos aislados de verdad |
| Puertos | 2× USB-C (Power Delivery) | Alcanza para almacenamiento externo rápido |
| Swap | archivo de 8 GB | Adecuado |
| Cifrado | **Sin LUKS**: `/` es ext4 en claro | **Hueco real**, doble: higiene y materia de estudio |
| Virtualización | KVM, QEMU, libvirt (`libvirtd` activo), docker | **Base lista**, sin VMs definidas |
| SO | Ubuntu 26.04.1 LTS, kernel 7.0.0-38 | Ver `01-sistema-operativo.md` |

## Espacio: qué se puede recuperar hoy

Medido, antes de comprar nada:

| Origen | Ocupa | Recuperable |
| --- | --- | --- |
| Caché de build de Docker | 41,6 GB | **22,3 GB** |
| Imágenes Docker (46) | 34,6 GB | 7,0 GB |
| Volúmenes Docker (139) | 19 GB | 10,3 GB |
| Revisiones viejas de snaps | 7,1 GB | ~4 GB |
| Journal de systemd | 2,7 GB | ~2 GB |
| `~/.cache` | 18 GB | depende del contenido |
| **Total rápido** | | **≈ 45–60 GB** |

Con eso, los 196 GB libres pasan a **≈ 250 GB**. Sigue sin alcanzar para un
laboratorio completo con imágenes forenses, pero cambia la conversación: alcanza
para arrancar el primer año sin comprar nada.

## Las tres carencias que sí importan

1. **Disco.** Un laboratorio de la carrera consume, estimado con margen:
   Kali 25 GB, Windows 11 eval 30 GB, servidor Linux 8 GB, REMnux 15 GB,
   snapshots ×1,5, imágenes forenses 10–40 GB, datasets de ML 5–20 GB.
   Total realista: **250–400 GB**. No entra en el disco interno junto con todo
   lo que ya usás para trabajar.
2. **Cifrado.** Un estudiante de ciberdefensa con el disco en claro es una
   contradicción, y además el cifrado es materia (Criptografía Aplicada, 4to año;
   Forense, 3ro). No requiere reinstalar: se cifra un volumen dedicado al
   laboratorio.
3. **Nada más.** CPU, RAM y red alcanzan. No hay que cambiar hardware por
   rendimiento.

## Compras, en orden de retorno

| Prioridad | Qué | Por qué | Orden de precio |
| --- | --- | --- | --- |
| 1 | SSD NVMe **interno 2 TB (2230 o 2280 según el formato del actual)** | Reemplaza al SN530 de 512 GB: 2 TB a velocidad nativa. **No hay segundo slot** (verificado) | US$ 100–160 |
| 2 | Carcasa USB-C para NVMe de 2242/2280 | El SN530 que sale del equipo pasa a ser respaldo y disco de trabajo forense | US$ 15–25 |
| 3 | SSD NVMe **externo** USB-C 1–2 TB | Alternativa completa a (1): cero riesgo sobre el sistema, ~1 GB/s por USB 3.2 Gen2 | US$ 80–130 |
| 4 | (opcional) switch administrable de 5 puertos | VLANs reales con la NIC cableada; se puede reemplazar por redes virtuales | US$ 30–50 |
| — | GPU dedicada | **No comprar.** Materia introductoria de 80 h; la nube gratuita cubre el resto | — |
| — | RAM | **No comprar.** 30 GiB sobran | — |

Antes de comprar, la pregunta del segundo slot ya está resuelta: **no existe**.
Ver `01-sistema-operativo.md`, sección *Almacenamiento*.

## Modelo exacto (verificado 2026-10-19)

Dell Latitude 5430, placa `01Y2TP`, BIOS 1.41.0 (2026-07-23).
Según el manual oficial *Setup and Specifications → Internal slots*:

| Slot | Cantidad | Estado en este equipo |
| --- | --- | --- |
| M.2 2230 para WiFi/Bluetooth | 1 | Ocupado (Intel CNVi) |
| M.2 2230/2280 para SSD | **1** | Ocupado (WD SN530 512 GB) |

No hay un tercer slot. El modelo *5430 Rugged* sí tiene un M.2 3042 para WWAN,
pero es otra máquina.

Confirmación en vivo, por topología PCIe:

| Root port | Ancho | Qué cuelga |
| --- | --- | --- |
| `00:1c.0` | x4 | SN530 NVMe — el único slot de SSD |
| `00:1d.0` | x1 | Lector de tarjetas Realtek RTS525A |
| `00:07.0/07.1` | — | Thunderbolt 4, sin dispositivos |

No queda ningún root port libre: no hay un segundo slot cableado, aunque se
quisiera usar el de WWAN (que además en este modelo no es un M.2 estándar y
Dell no soporta SSD ahí).
