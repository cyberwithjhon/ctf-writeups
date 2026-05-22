# Aliens Box - HackRocks CTF

> ⚠️ Work in progress

## Descripción
Se nos proporciona una imagen aparentemente normal. 
El reto consiste en encontrar un mensaje oculto dentro de ella.

**Categoría:** Esteganografía  
**Plataforma:** HackRocks  
**Dificultad:** Easy 
**Flag:** ``

---

## Herramientas utilizadas
- `exiftool` → análisis de metadatos
- `binwalk` → detección de archivos ocultos
- `zsteg` → detección de esteganografía en PNG

---

## Proceso

### Paso 1 - Análisis de metadatos
Lo primero siempre es analizar los metadatos de la imagen:

```bash
exiftool intercepted_transmission.png
```

**Resultado:** No se encontró nada relevante en los metadatos.

### Paso 2 - Búsqueda de archivos ocultos
```bash
binwalk intercepted_transmission.png
```

**Resultado:** Solo se detectó la estructura normal del PNG. Nada oculto.


### Paso 3 - Esteganografía LSB
Al no encontrar nada con los métodos anteriores, probé con zsteg 
que analiza los bits menos significativos de los píxeles:

```bash
zsteg -a intercepted_transmission.png
```

**Resultado:** La flag apareció oculta en los bits LSB del canal RGB.

---

## Explicación técnica

La técnica utilizada se llama **LSB (Least Significant Bit)**.

Cada píxel de una imagen PNG contiene colores en formato RGB. 
Cada canal de color se representa con 8 bits. 
El último bit (el menos significativo) apenas afecta 
al color visualmente.

La diferencia es imperceptible para el ojo humano, pero 
concatenando todos esos bits ocultos se puede reconstruir 
un mensaje completo.

---

## Lección aprendida

Cuando una imagen no revela nada en metadatos ni en análisis 
superficial, la esteganografía LSB es el siguiente paso.

La metodología correcta para retos de este tipo es:
1. Metadatos → `exiftool`
2. Archivos ocultos → `binwalk`
3. Esteganografía → `zsteg`, `steghide`

---

## Referencias
- [zsteg GitHub](https://github.com/zed-0xff/zsteg)
- [Steganography - Wikipedia](https://en.wikipedia.org/wiki/Steganography)
