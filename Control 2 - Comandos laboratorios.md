# Cheatsheet Comandos Control 2 [R]

## Intervalos de confianza
Nota: las fórmulas prácticas necesitan la librería ```TeachingDemos``` instalada.
(En caso de no tener la libería instalada revisa el documento del Control 1 > Laboratorio 3 > Instalar paquetes).

Importamos la librería de la siguiente manera
```R
library(TeachingDemos)
```

### IC en distribución Normal
Esta distribución sirve:
- Obtener la media en distribuciones Normales con **desviación estandar conocida**
- Obtener el lambda de una distribución Exponencial **(ver directamente la fórmula práctica)**
- Obtener el lambda de una distribución Poisson **(ver directamente la fórmula práctica)**

La forma teórica del primer caso tiene la siguiente fórmula:
```R
# Vamos a probar con una IC de 95% de confianza
alpha <- 1 - 0.95
mean(archivo$datos) - qnorm(1-alpha/2,0,1)*sqrt(var(archivo$datos) / n_datos) # Cota Inferior
mean(archivo$datos) + qnorm(1-alpha/2,0,1)*sqrt(var(archivo$datos) / n_datos) # Cota Superior

# n_datos es la cantidad total de datos (o de la muestra) (NO ES LA QUE LA FUNCIÓN "MAX" INDICA)
```

Sin embargo, nosotros utilizaremos la fórmula práctica:

```R
# Una fórmula válida (Considere esta sobre la siguiente)
z.test(archivo$datos, stdev = sd(archivo$datos), conf.level = 0.95)$conf.int
# Otra fórmula válida
z.test(archivo$datos, mean(archivo$datos), sd(archivo$datos, conf.level = 0.95))$conf.int
z.test(archivo$datos, mean(archivo$datos), sd(archivo$datos, conf.level = 0.95))$conf.int[c(1,2)]
```

Es importante saber los siguientes datos:
- ```conf.level``` = Nivel de confianza
- ```conf.int``` = Limitamos los datos a los extremos del intervalo de confianza más un nivel de confianza probablemente indicado según los datos
- ```conf.int[c(1,2)]``` = Retorna únicamente los extremos el intervalo de confianza. [Lim. Inferior 	Lim. Superior]

### IC en t-Studient
Esta distribución sirve para:
- Buscar la media de distribuciones normales con **desviación normal desconocida**

La fórmula práctica es la siguiente:
```R
# Como ejemplo tomaremos un nivel de confianza del 95%
t.test(archivo$datos, conf.level = 0.95)$conf.int
```

### IC en Chi-cuadrado
Esta distribución sirve para:
- Obtener la varianza de una distribución Normal con **media desconocida**

La fórmula teórica es la siguiente:
```R
# Consideremos un nivel de significancia del 2.5% (confianza del 97.5%)
((n_datos - 1)*var(archivo$datos))/qchisq(0.975, n_datos - 1)
((n_datos - 1)*var(archivo$datos))/qchisq(0.025, n_datos - 1)
```

La fórmula teórica es la siguiente:
```R
sigma.test(archivo$datos)$conf.int
```


### IC en Bernoulli y tablas
Si deseamos sacar el IC de una distribución Bernoullí la podemos procesar a una tabla.
```R
table(archivo$datos)
```

De esta forma obtenemos la cantidad de datos que:
1. Corresponden con lo que buscamos (es decir, los datos con probabilidad pi)
2. No corresponden con lo que buscamos (es decir, los datos con probabilidad (1 - pi))

En la Bernoulli la fórmula teórica es la siguiente:
```R
# Consideremos un nivel de confianza del 97.5%
# Consideremos como "datos_favorables" aquella cantidad de datos que corresponden con lo que buscamos
p = datos_favorables / datos_totales
p - qnorm(0.975, 0, 1)*sqrt((p*(1-p)) / datos_totales)
p + qnorm(0.975, 0, 1)*sqrt((p*(1-p)) / datos_totales)
```

La fórmula práctica es la siguiente:
```R
prop.test(datos_favorables, datos_totales, conf.level = 0.95)$conf.int
```

NOTA: Existen ciertas diferencias entre los resultados en las fórmulas teóricas y la fórmula práctica, pero es relativamente mínimo por lo que no hay que preocuparse.

NOTA: Nótese que esto también funciona para datos cuantitativos, por lo que los podemos pasar a tratar como una distribución Bernoulli.

### IC en otras funciones
Por ejemplo saquemos el IC de la desviación estandar (de distribución normal con media desconocida).
Por Invarianza podemos deducir que para obtener el IC de una función hay que evaluar en la función dicho intervalo.

```R
# IC de la varianza:
sigma.test(diamantes$quilate)
# Buscamos el IC de la SD --> var(a) = sd^2(a)


# Digamos que tenemos IC(a) y queremos IC(g(a))
# Entonces por el principio de invarianza encontramos que ocurre lo siguiente:
# IC(g(a)) ---> g(IC(a))
# Por lo tanto, el IC de la distribución estandar es:
sqrt(sigma.test(archivo$datos)$conf.int)
```

## Otros comandos que aparecieron en los laboratorios
###### !--- SECCIÓN EN CONSTRUCCIÓN ---!

### Sacar intervalos inferiores y superiores en las fórmulas prácticas
Si nos damos cuenta, las fórmulas prácticas nos entregan el intervalo entre ambas cotas, pero si buscamos los intervalos de los extremos superiores e inferiores (por ejemplo, buscar cuando "mu > x") usamos el atributo ```alternative```.

- ```alternative = 'less'```: Entrega un intervalo en un extremo desde la cota inferior
- ```alternative = 'greater'```: Entrega un intervalo en un extremo desde la cota superior

Ejemplo
```R
# Digamos que buscamos la varianza en N(mu, sigma) con media desconocida.
# Consideremos una confianza del 95%

z.test(archivo$datos, stdev = sd(archivo$datos), conf.level = 0.95, alternative = 'less')
z.test(archivo$datos, stdev = sd(archivo$datos), conf.level = 0.95, alternative = 'greater')
```


### Filtros:

#### Filtro para eliminar los NA
A veces los NA pueden hacer que no obtengamos las medias o varianzas que buscamos.
Para esto se recurre al siguiente filtro:

```R
# Digamos que buscamos la varianza en N(mu, sigma) con media desconocida.
# El filtro para eliminar los NA de la muestra es el siguiente:
muestra$datos[is.na(muestra$datos)==FALSE]

# Esto se puede agregar en la fórmula de la siguiente manera:
sigma.test(muestra$datos[is.na(muestra$datos)==FALSE])$conf.int

# O guardando el filtro en una variable
datos_con_filtro = muestra$datos[is.na(muestra$datos)==FALSE]
sigma.test(datos_con_filtro)$conf.int
```
