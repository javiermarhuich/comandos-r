# Cheatsheet Comandos laboratorios [R] (Control 2)

## LABORATORIO 5

### Intervalos de confianza

#### IC de distribución normal
Primero importamos la librería ```TeachingDemos```
(En caso de no tener la libería instalada revisa el documento del control1 > Laboratorio 3 > Instalar paquetes).
```R
library(TeachingDemos)
```

Para sacar el Intervalo de confianza de distribución Normal usamos el siguiente comando (obteniendo un IC al 90%):
```R
z.test(archivo$datos, mean(archivo$datos), sd(archivo$datos, conf.level = 0.9))$conf.int[c(1,2)]
```
- ```conf.level``` = Nivel de confianza
- ```conf.int[c(1,2)]``` = Crea un objeto que posee los dos extremos del intervalo de confianza. [Lim. Inferior 	Lim. Superior]

Para una tabla:
```R
prop.test(n_datos_muestra, n_datos_totales, conf.level = 0.90)$conf.int
```


#### IC de distribución t-Studient
Para sacar el Intervalo de Confianza de distribución t-Studient con confianza del 90% usamos el siguiente comando:
```R
t.test(muestra$datos)$conf.int
```