### 1. Rendimiento de Redes (Retardos y Capacidad)

Estas fórmulas se utilizan para evaluar el rendimiento básico de un enlace o red.

- *Retardo de propagación:* $R_{prop} = \frac{\text{Distancia}}{V_l}$
    - Dónde: $V_l$ es la velocidad de la luz en el medio ($2,997\cdot 10^8$ m/s en el aire, $2,3\cdot 10^8$ m/s en cable, $2,0\cdot 10^8$ m/s en fibra óptica).
- *Retardo de transmisión:* $R_{tx} = \frac{\text{Tamaño del paquete}}{V_t}$
    - Dónde: $V_t$ es la capacidad o velocidad de transmisión del enlace (bps).
- *Retardo total:* $R_{total} = \text{Retardo de propagación} + \text{Retardo de transmisión} + \text{Retardo de colas}$.
- *Round Trip Time (RTT):* $RTT = 2 \times \text{Retardo total}$.
- *Cantidad de bits en la red:* $\text{Bits} = \text{Retardo de propagación} \times V_t$.

### 2. Señales y Análisis de Fourier

Fórmulas empleadas para descomponer y analizar señales físicas periódicas.

- *Condición de señal periódica:* $s(t) = s(t + iT_0)$.
- *Frecuencia fundamental:* $f = \frac{1}{T_0}$ (en Hz), y su frecuencia angular $\omega = \frac{2\pi}{T_0}$.
- *Serie de Fourier:* $s(t) = \frac{1}{2}a_0 + \sum_{n=1}^{\infty} a_n \cos(2\pi nft) + \sum_{n=1}^{\infty} b_n \sin(2\pi nft)$.
- *Cálculo de los coeficientes de Fourier:*
    - $a_0 = \frac{2}{T_0} \int_0^{T_0} s(t) dt$.
    - $a_n = \frac{2}{T_0} \int_0^{T_0} s(t) \cos(2\pi nft) dt$.
    - $b_n = \frac{2}{T_0} \int_0^{T_0} s(t) \sin(2\pi nft) dt$.
- *Potencia media total de la señal:* $P = \frac{1}{T_0} \int_0^{T_0} [s(t)]^2 dt$.
- *Potencia acumulada de los $j$ primeros armónicos:* $P = \frac{1}{4} a_0^2 + \frac{1}{2} \sum_{n=1}^{j} [C_n]^2$.
    - Dónde la amplitud del armónico es: $C_n = \sqrt{a_n^2 + b_n^2}$.

### 3. Rendimiento de Sistemas de Transmisión (Atenuación)

Cálculos para medir la pérdida o ganancia de una señal expresada en decibelios (dB).

- *Rendimiento en base a la Potencia:* $R_{dB} = 10 \cdot \log_{10}\left(\frac{P_s}{P_e}\right)$.
    - Dónde: $P_s$ es la potencia de salida y $P_e$ la potencia de entrada.
- *Rendimiento en base al Voltaje:* $R_{dB} = 20 \cdot \log_{10}\left(\frac{V_s}{V_e}\right)$.
- *Relación Señal/Ruido (S/N) en dB:* $(S/N){dB} = 10 \cdot \log{10}\left(\frac{P_s}{P_n}\right)$.

### 4. Capacidad del Canal y Digitalización

Fórmulas teóricas absolutas para la velocidad de transmisión (Teoremas).

- *Frecuencia mínima de Muestreo:* $f_{mostreig} \ge 2W$.
- *Teorema de Nyquist (canal ideal sin ruido):* $v = 2 \cdot W \cdot \log_2(N)$.
    - Dónde: $v$ es la velocidad (bps), $W$ el ancho de banda (Hz) y $N$ el número de niveles de cuantificación.
- *Teorema de Shannon (canal real con ruido):* $C = W \cdot \log_2(1 + S/N)$.
    - Dónde: $C$ es la capacidad máxima (bps) y $S/N$ es la relación señal a ruido en su valor absoluto (no en dB).

### 5. Medios de Transmisión Inalámbricos (Antenas y Satélites)

Fórmulas sobre radiación electromagnética y enlaces orbitales.

- *Longitud de onda:* $\lambda = \frac{c}{f}$.
- *Pérdidas de propagación en el espacio libre:* $L = \left(\frac{4\pi d}{\lambda}\right)^2$.
    - En decibelios: $L_{dB} = 20 \cdot \log_{10}\left(\frac{4\pi d}{\lambda}\right)$.
- *Ganancia de una antena parabólica:* $G_{dBi} \approx 10 \cdot \log_{10}\left(0.5 \left(\frac{\pi D}{\lambda}\right)^2\right)$.
- *Dinámica orbital de un satélite:* $d^3 = \frac{G \cdot m_t}{\omega^2}$.
- *Rendimiento total de un enlace por satélite:* $R = G_{t,up|dB} - L_{up|dB} + G_{s,up|dB} + A_{|dB} + G_{s,down|dB} - L_{down|dB} + G_{t,down|dB}$.

### 6. Fibra Óptica (Leyes Ópticas)

Fórmulas de refracción de la luz en medios guiados.

- *Índice de refracción:* $n = \frac{c}{v}$.
- *Ley de Snell:* $n_1 \cdot \sin(\theta_1) = n_2 \cdot \sin(\theta_2)$.
- *Ángulo crítico:* $\theta_c = \sin^{-1}\left(\frac{n_2}{n_1}\right)$ (solo aplicable cuando $n_1 > n_2$).

### 7. Codificación de Línea y Velocidades

Relación entre bits de información y los pulsos físicos transmitidos al medio.

- *Velocidad de transmisión:* $V_t = \frac{1}{T_b}$ (medido en bps, donde $T_b$ es el tiempo de bit).
- *Velocidad de modulación (Tasa de Baudios):* $V_m = \frac{V_t}{n}$.
    - Dónde: $n$ depende de la eficiencia de la codificación (Ej. Manchester $n = 1/2$, 4B5B $n = 4/5$, 8B6T $n = 8/6$).

### 8. Control de Enlace (Protocolo ARQ - Ventana Deslizante)

Reglas matemáticas de estados para garantizar la fiabilidad del protocolo de ventana deslizante.

- *Condición estricta del Transmisor:* $LFS - LAR \le SWS$.
    - ($LFS$: Última trama enviada, $LAR$: Último ACK recibido, $SWS$: Tamaño de ventana del transmisor).
- *Asignación de ventana en el Receptor:* $LAF = LFA + RWS$.
    - ($LAF$: Trama máxima aceptable, $LFA$: Último ACK enviado, $RWS$: Tamaño de ventana del receptor).
- *Condición de aceptación de una trama en el Receptor:* $LFA < \text{Num. Seq.} \le LAF$.
- *Límite máximo del tamaño de ventana para evitar secuencias duplicadas:* $RWS = SWS \le \frac{\text{Num. Seq. Max} + 1}{2}$.