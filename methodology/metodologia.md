# Metodología — Índice Equiponderado Chile 15 (IEC15)

**Versión 0.4** · 24 de septiembre de 2026

Este documento fija las reglas del índice antes de calcular cualquier resultado, para que el backtest no pueda ajustarse a posteriori. Es un índice educativo y no constituye asesoría de inversión.

## Historial de versiones

| Versión | Fecha | Cambio |
| --- | --- | --- |
| 0.4 | 24-sep-2026 | La presencia bursátil (1.000 UF) se reemplaza por la frecuencia de negociación de MSCI (80% nuevas, 70% vigentes, 3 meses), porque la validación contra casos oficiales mostró que el volumen de Yahoo no permite medir un umbral en UF (sección 3). Se definen el universo de candidatas, el calendario de días hábiles y la composición inicial, y se documentan las limitaciones de datos. Cambio decidido antes de calcular cualquier retorno del índice |
| 0.3 | 23-sep-2026 | El benchmark pasa a ser el S&P CLX IPSA diario (Investing.com), validado contra la serie oficial del Banco Central. Se precisa que el último día del backtest es el 15-jul-2026, porque el 16-jul fue feriado. No cambia ninguna regla de cálculo del índice, por lo que no requiere consulta |
| 0.2 | 22-sep-2026 | Se fija la fecha de lanzamiento, el fin del backtest (16-jul-2026), una regla de validación de datos y la fuente del benchmark. Cambio previo al cálculo en vivo, por lo que no aplica el plazo de consulta de la sección 7 |
| 0.1 | 22-sep-2026 | Primera versión de las reglas |

## 1. Objetivo y decisiones de diseño

El índice mide el rendimiento de las 15 acciones chilenas más líquidas, todas con el mismo peso, y se compara contra el IPSA. El objetivo es responder una pregunta simple: ¿qué pasa si cada empresa grande pesa lo mismo, en vez de dominar las de mayor capitalización?

| Parámetro | Valor | Base en una metodología real |
| --- | --- | --- |
| Nombre de trabajo | Índice Equiponderado Chile 15 (IEC15) | — |
| Tipo | Equiponderado (equal weight) | S&P 500 Equal Weight |
| N° de componentes | 15 (mínimo 12) | S&P/CLX Chile 15; universo líquido chileno reducido |
| Moneda | Pesos chilenos (CLP) | IPSA se calcula en CLP |
| Versión de retorno | Price Return en v0.1; Total Return en v0.2 | S&P/CLX publica PR, TR y NTR |
| Rebalanceo | Trimestral | S&P 500 Equal Weight |
| Fecha y nivel base | 31 de diciembre de 2019 = 1.000 puntos | Incluye el periodo COVID como prueba de estrés |
| Fecha de lanzamiento | 22 de septiembre de 2026 | Antes de esta fecha el rendimiento es hipotético; después, en vivo. Práctica de S&P DJI y MSCI |
| Benchmark | S&P IPSA hasta el 31-ago-2026; MSCI IPSA desde el 1-sep-2026 | Cambio de administrador verificado |

La serie del benchmark mezcla dos metodologías desde septiembre de 2026, así que toda comparación que cruce esa fecha lo declara. Para comparar, el benchmark se normaliza a 1.000 puntos en la fecha base.

## 2. Universo elegible y fuentes de datos

Son elegibles las acciones listadas en la Bolsa de Santiago (nuam) con al menos 6 meses de cotización. Se excluyen las AFP, igual que en el IPSA, los fondos de inversión, fondos mutuos, ETF y CFI, y los valores extranjeros del mercado internacional. Si una empresa tiene varias series de acciones, entra solo la más líquida.

**Universo de candidatas:** de 568 valores que el buscador de Yahoo Finance asocia a la bolsa de Santiago, quedan 90 candidatas. La lista completa, con la decisión y el motivo de cada exclusión, está en `data/reference/universo_candidatos.csv`.

**Calendario de días hábiles:** un día es hábil si el IPSA tuvo cierre en la serie diaria de Investing.com.

El universo líquido real es chico: en la consulta de S&P DJI de 2018, solo 175 de 1.179 valores listados tenían transacciones diarias relevantes. Se espera un universo útil de entre 20 y 40 acciones.

| Dato | Fuente | Estado y limitación |
| --- | --- | --- |
| Precios diarios de cierre | Yahoo Finance vía yfinance (sufijo .SN) | Solo uso personal y educativo. Datos válidos desde el 2-ene-2019 hasta el 15-jul-2026 |
| Precios (validación) | Bolsa de Santiago | Fuente oficial; restricciones de uso |
| Volumen transado diario | Yahoo Finance vía yfinance | Se usa para la frecuencia de negociación y el ranking de liquidez. Su nivel no es comparable entre años (ver limitaciones) |
| Montos transados mensuales (control) | API BDE del Banco Central, serie F022.MB4.FLU.Z.Z.Z.M | Solo para revisar la consistencia del volumen de Yahoo en el tiempo |
| Número de acciones y grupos empresariales | CMF | Público |
| Benchmark (IPSA) | S&P CLX IPSA diario en Investing.com (versión de precio) | Solo uso personal; se guarda en `data/raw/`. El ticker ^IPSA de Yahoo no entrega datos |
| Benchmark (control de calidad) | API BDE del Banco Central, serie F013.IBC.IND.N.7.LAC.CL.CLP.BLO.M | Gratis con registro. Es un promedio mensual, no un cierre, por lo que solo se usa para validar |
| Indicadores macro (fase 2) | API BDE del Banco Central | Gratis con registro |

**Regla de validación de datos:** un día con volumen cero y el mismo precio de cierre que el día anterior se trata como día sin dato, no como un precio válido.

**Resultado de la prueba del 22-sep-2026:** en 7 de 8 acciones probadas, 43 o 44 de los 46 días posteriores al 17-jul-2026 cumplían esa condición, es decir, yfinance repite el último precio. Banco de Chile (CHILE.SN) fue la excepción. Por eso el backtest termina el 15-jul-2026, último día hábil antes del quiebre (sección 6).

**Validación del benchmark (23-sep-2026):** el cierre del 30-nov-2023 en Investing (5.818,51) coincide con el reportado en prensa. Al promediar por mes los datos diarios de Investing, la diferencia contra la serie del Banco Central fue de 0,014% como máximo en 90 meses (ene-2019 a jun-2026). Julio de 2026 difiere 0,245% porque el mes está incompleto. La misma prueba confirmó que la serie del Banco Central es un promedio mensual.

**Pregunta abierta:** qué fuente de precios usar para el cálculo en vivo desde el 17-jul-2026.

Los datos descargados se guardan en `data/raw/`, carpeta excluida del repositorio público por los términos de uso de Yahoo Finance.

**Limitaciones de datos conocidas:**

- **Empresas deslistadas sin datos:** AES Andes, Grupo Security y La Polar no tienen datos en Yahoo Finance. AES Andes y Grupo Security eran parte del IPSA en 2019 y podrían haber competido por los últimos cupos; no participan en la selección.
- **Cambios de nombre:** Itaú (ITAUCL) y Pampa Investments (PAMPA) conservan su historia desde 2019 bajo el ticker actual. Cencosud Shopping (CENCOMALLS) tiene datos desde el 28-jun-2019, fecha en que empezó a cotizar.
- **Nivel del volumen de Yahoo:** comparado con una serie mensual de montos transados del Banco Central, la razón entre ambas es estable entre 2019 y 2023 (222% a 284%) y salta desde 2024 (675% a 1.135%). El nivel del volumen de Yahoo no es comparable entre años. El ranking de liquidez compara acciones en una misma fecha, por lo que se ve menos afectado.
- **SQM-A en 2021:** Yahoo registra para SQM-A un valor transado muy superior al de SQM-B entre el segundo y el cuarto trimestre de 2021, con precios coherentes entre ambas series. No se pudo verificar si fue actividad real. El impacto en el índice es bajo, porque ambas series son la misma empresa y sus precios se mueven juntos.

## 3. Criterios de selección

Entran las 15 acciones elegibles con mayor liquidez, con un colchón que evita que una acción entre y salga en cada revisión. Las reglas se aplican en este orden:

1. **Frecuencia de negociación:** porcentaje de días hábiles de los últimos 3 meses con al menos una transacción. Mínimo 80% para acciones nuevas y 70% para componentes vigentes. Son los umbrales del MSCI NUAM Index.
2. **Liquidez:** las acciones que pasan el filtro 1 se ordenan por la mediana del valor diario transado (MDVT) de los últimos 6 meses, como en el S&P IPSA.
3. **Colchón de entrada y salida:** las 12 primeras del ranking entran directamente. Los 3 cupos restantes se llenan primero con componentes vigentes que estén entre los 18 primeros; si faltan, con las siguientes del ranking. Es el colchón del IPSA (25 directas y 5 desde el top 35) escalado a 15.
4. **Mínimo de componentes:** si quedan menos de 15 acciones elegibles, el índice opera con las que haya, con un mínimo de 12. Bajo 12 se revisa la metodología.

**Por qué frecuencia de negociación y no presencia bursátil (v0.4):** la presencia bursátil oficial cuenta los días con transacciones de al menos 1.000 UF. Aproximada con precio × volumen de Yahoo, IAM, SalfaCorp e ILC obtenían cerca de 45% en agosto de 2019, cuando oficialmente cumplían con al menos 85–90%: IAM entró al IPSA en septiembre de 2019 y SalfaCorp e ILC eran componentes vigentes. Con la frecuencia de negociación, las tres obtienen 100%. La frecuencia solo requiere saber si hubo transacciones, por lo que no depende del nivel del volumen de Yahoo. En noviembre de 2019, 51 acciones tenían frecuencia de 80% o más; el ranking de liquidez decide cuáles entran.

**Free float en v0.1:** no se aplica un filtro de free float, porque no hay una fuente gratuita verificada del porcentaje flotante por acción. Como el peso es igual para todas, el free float no afecta la ponderación. En v0.2 se evaluará un mínimo de 15%, que es el umbral de MSCI, si se confirma una fuente.

## 4. Ponderación y rebalanceo

En cada rebalanceo todas las acciones vuelven a pesar lo mismo: 1/15, es decir, 6,67% cada una. Entre rebalanceos los pesos se mueven libremente con el precio de cada acción.

**Tope de resguardo:** si al cierre de cualquier día un componente supera 10% del índice, se hace un rebalanceo extraordinario a pesos iguales, efectivo tras el cierre del quinto día hábil siguiente. Con 15 acciones esto solo ocurre si una acción sube cerca de 50% más que el resto en un trimestre.

| Evento | Cuándo |
| --- | --- |
| Fecha de referencia (datos de selección) | Cierre del tercer viernes de febrero, mayo, agosto y noviembre |
| Anuncio de la nueva composición | 5 días hábiles antes de la fecha efectiva |
| Precios para fijar las unidades | Cierre de 7 días hábiles antes de la fecha efectiva, como hace S&P |
| Fecha de referencia en día no hábil | Se usa el último día hábil anterior |
| Fecha efectiva | Después del cierre del tercer viernes de marzo, junio, septiembre y diciembre |

**Composición inicial:** la selección con fecha de referencia 15-nov-2019 define los componentes al 31-dic-2019. Las unidades se fijan con los precios de cierre del 31-dic-2019, para que el índice parta con pesos exactamente iguales.

Como en los rebalanceos siguientes las unidades se fijan con precios de 7 días antes, los pesos efectivos quedan cerca de 6,67%, no exactos. Es la práctica de S&P y evita usar información que no estaba disponible al anunciar el cambio.

## 5. Fórmula de cálculo y eventos corporativos

El nivel del índice es el valor de una canasta de unidades dividido por un divisor. El divisor solo cambia cuando algo que no es un movimiento de precio altera la canasta, para que el índice no salte. Es el mismo mecanismo de S&P DJI.

$$
I_t = \frac{\sum_{i=1}^{N} Q_i \, P_{i,t}}{D_t}
$$

Donde $I_t$ es el nivel del índice el día $t$, $P_{i,t}$ el precio de cierre de la acción $i$, $Q_i$ las unidades de la acción $i$ en la canasta y $D_t$ el divisor.

En cada rebalanceo se fijan unidades que dan igual valor a cada acción al precio de referencia (7 días hábiles antes):

$$
Q_i = \frac{1}{P_{i,\text{ref}}}
$$

Y tras el cierre de la fecha efectiva $e$ se recalcula el divisor para que el nivel no cambie:

$$
D_{\text{nuevo}} = \frac{\sum_{i} Q_i^{\text{nuevo}} \, P_{i,e}}{I_e}
$$

| Evento | Tratamiento en v0.1 (Price Return) |
| --- | --- |
| Split o agrupación de acciones (razón k) | Se multiplican las unidades por k; el divisor no cambia |
| Dividendo ordinario en efectivo | Sin ajuste: el precio cae y el índice lo refleja. Se reinvierte en la versión Total Return (v0.2) |
| Dividendo extraordinario | Se ajusta el divisor, como en los índices de precio de S&P |
| Spin-off | La nueva acción entra a precio cero sin efecto en el divisor y sale en el siguiente rebalanceo |
| Fusión, adquisición o deslistado | Sale al último precio de cierre; se ajusta el divisor; el cupo se llena en el siguiente rebalanceo |
| Suspensión de 60 días o más | Sale en la siguiente revisión a precio cero, como en el S&P/CLX |
| Derechos de suscripción | Sin ajuste en v0.1. Es una limitación conocida que se registra cada vez que ocurra |

## 6. Backtest

El backtest cubre desde la fecha base (31-dic-2019) hasta el 15-jul-2026, último día hábil con datos confiables (el 16-jul fue feriado), y todo resultado se rotula como rendimiento hipotético. Los parámetros de este documento quedan fijos antes de correrlo y no se ajustan después de ver los resultados. Es la principal defensa contra el sobreajuste que describen Bailey, Borwein, López de Prado y Zhu (2014).

| Tramo | Fechas | Tratamiento |
| --- | --- | --- |
| Backtest | 31-dic-2019 al 15-jul-2026 | Rendimiento hipotético |
| Sin datos confiables | 16-jul-2026 al 21-sep-2026 | No se publican niveles diarios. El rebalanceo de septiembre de 2026 no se ejecuta y se mantiene la canasta vigente |
| En vivo | Desde el 22-sep-2026 | El cálculo se reanuda con la canasta vigente cuando haya una fuente de precios validada. El cambio entre el 15-jul-2026 y la reanudación se reporta como un solo movimiento |

| Sesgo | Riesgo en este proyecto | Medida |
| --- | --- | --- |
| Supervivencia | Alto: no hay composición histórica punto en el tiempo gratis, y las acciones deslistadas suelen faltar en las fuentes | Se usan todas las acciones con datos en cada fecha de revisión, no solo las actuales. Faltan AES Andes, Grupo Security y La Polar (sección 2) |
| Anticipación (look-ahead) | Medio: usar datos que no existían en la fecha de decisión | Selección con datos a la fecha de referencia; unidades con precios de 7 días hábiles antes |
| Sobreajuste | Bajo: el equiponderado no tiene parámetros que optimizar | Reglas fijas antes del cálculo; cualquier variante probada se reporta, aunque salga peor |

**Métricas a reportar**, siempre junto al IPSA en el mismo periodo:

- Retorno anualizado y volatilidad anualizada
- Máxima caída (drawdown) y su duración
- Tracking error contra el IPSA
- Rotación (turnover) en cada rebalanceo
- Resultados por subperiodo: 2020, 2021–2023 y 2024 al 15-jul-2026

Los resultados se presentan brutos, sin costos de transacción. Esto se declara, porque la rotación de un equiponderado es mayor que la de un índice por capitalización: S&P midió 29% anual contra 5% en EE.UU.

## 7. Gobernanza, control de errores y publicación

El documento sigue los Principios IOSCO para índices financieros de forma proporcional: es un índice educativo de una persona, pero con reglas públicas, cambios trazables y una política de errores escrita.

- **Versionado:** este documento vive en el repositorio de GitHub. Cada cambio de regla es un commit con número de versión (v0.1, v0.2…) y una nota en el historial de versiones que explica qué cambió y por qué.
- **Cambios de metodología:** se anuncian como Issue en el repositorio al menos 20 días hábiles antes de aplicarse, para recibir comentarios, como pide el Principio 12 de IOSCO. Plazo propuesto; ajustable.
- **Errores:** un error de precio o un evento corporativo omitido que se detecte dentro de 2 días hábiles se corrige y se republica, igual que en S&P DJI. Después de ese plazo se documenta en un registro de errores y no se reexpresa la serie, salvo que cambie el nivel en más de 0,5% (umbral propuesto).
- **Publicación:** en la fase 1 el nivel se publica de forma manual en el repositorio; la publicación diaria automática llega en la fase 3.

### Disclaimer

Va en el repositorio, en la web y en cada publicación en LinkedIn:

> Este índice y su documentación tienen fines exclusivamente educativos. No constituyen asesoría de inversión, oferta ni recomendación para comprar o vender instrumento alguno, en los términos de la Ley N°21.521. El autor es una persona natural no inscrita en el Registro de Prestadores de Servicios Financieros de la CMF. Los resultados históricos son rendimiento hipotético, pueden tener sesgo de supervivencia y no garantizan resultados futuros. Los datos de Yahoo Finance se usan solo con fines personales y no comerciales.

La regla práctica: describir el índice y sus resultados, nunca decir qué comprar o vender.

## Fuentes

- [Metodología de los Índices S&P/CLX (S&P DJI, marzo 2023)](https://www.spglobal.com/spdji/es/documents/methodologies/methodology-sp-clx-indices-spanish.pdf)
- [Index Mathematics Methodology (S&P DJI)](https://www.spglobal.com/spdji/en/documents/methodologies/methodology-index-math.pdf)
- [S&P 500 Equal Weight Index Methodology](https://www.spice-indices.com/idpfiles/spice-assets/resources/public/documents/methodology-sp-500-equal-weight-index.pdf)
- [S&P 500 Low Volatility Index Methodology](https://www.spglobal.com/spdji/en/documents/methodologies/methodology-sp-500-low-volatility-index.pdf)
- [MSCI NUAM Index (MSCI)](https://www.msci.com/documents/10199/9a24b1cd-5825-57b1-1def-a01aa76ad69c)
- [Chile completa su reforma de índices y pasa a MSCI (The Rio Times)](https://www.riotimesonline.com/chile-nuam-index-convergence-msci-2026/)
- [Principios IOSCO para Índices Financieros (2013)](https://www.iosco.org/library/pubdocs/pdf/IOSCOPD415.pdf)
- [API para Base de Datos Estadísticos (Banco Central de Chile)](https://si3.bcentral.cl/estadisticas/Principal1/Web_Services/index_API_sec1_es.htm)
- [bcchapi, librería oficial del Banco Central (PyPI)](https://pypi.org/project/bcchapi)
- [S&P CLX IPSA Historical Data (Investing.com)](https://www.investing.com/indices/ipsa-historical-data)
- [yfinance: advertencia de uso personal (Ran Aroussi)](https://aroussi.com/post/python-yahoo-finance)
- [yfinance issue #2966: histórico roto para mercados nuam](https://github.com/ranaroussi/yfinance/issues/2966)
- [Ley Fintec N°21.521 (CMF Educa)](https://www.cmfchile.cl/educa/621/w3-propertyvalue-46340.html)
- [Asesores de inversión (CMF Educa)](https://www.cmfchile.cl/educa/621/w3-propertyvalue-48194.html)
