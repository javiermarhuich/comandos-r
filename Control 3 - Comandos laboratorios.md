# Cheatsheet Comandos Control 3 [R]

## Test de hipótesis
El test de hipótesis nos ayuda a saber si nuestras sospechas sobre el comportamiento de los parámetros es verdadera o no.

Esto se hace planteando dos hipótesis: ```H0``` (Hipótesis Nula, la cual intentamos rechazar) y ```H1``` (Hipótesis Alternativa, establecida por el investigador, la cual intentamos apoyar). Por lo general se toma que:
```
H0: parámetro == valor
H1: parámetro != valor
```
donde H1 puede ser mayor, menor o directamente no contener el valor que posee H0.

**Nota:** es preferible tener la librería "TeachingDemos" instalada, recuerda importarla con
```R
library(TeachingDemos)
```

**TIP**: Mucha atención en cómo está establecida H1.
- Si dice que ```H1 > (o >=) valor``` usamos ```alternative = 'greater'```
- Si dice que ```H1 < (o <=) valor``` usamos ```alternative = 'less'```
- Si dice que ```H1 != (distinto a) valor``` usamos ```alternative = 'two.sided'```

### Test de hipótesis desde la región crítica
Una de las formas para realizar el test de hipótesis es realizarlo desde la región crítica.
Las regiones críticas de cada caso se pueden encontrar en el formulario de IC + Test de Hipótesis.

Por ejemplo, tomemos una región crítica para una Bernoulli:
```R
z0 <- (p.gorro - p0)/sqrt((p0*(1-p0))/n) #Creamos el pivote
# p.gorro sería el valor del EMV
# p0 el valor observado
# n es la cantidad de datos de la muestra

valor.p <- pnorm(z0) # Obtenemos el valor-p de la nuestra
# Nota: recordemos que por TLC usamos la distribución normal en Bernoulli si n es pequeño
```
Con esto obtenemos el valor-p de la muestra, la cual representa la posibilidad de conseguir un caso extremo teniendo a H0 como verdadera, lo que sería incoherente generando un Error Tipo I.

Para comparar se hace lo siguiente:
```r
# Definamos alpha como el nivel de significancia, entonces:
valor.p > alpha
```
- Si al escribir esto en la consola esta retorna ```TRUE``` entonces no podemos rechazar H0
- Si al escribir esto en la consola esta retorna ```FALSE``` entonces rechazamos H0

### Test de Hipótesis con t-test
Esta nos servirá en el caso de revisar la media.
```R
t.test(archivo$datos, mu = valor, alternative = alternativa, conf.level = 1 - alpha)
```
Donde:
- ```valor``` es el valor que toma H0 (para simplificar y no terminar quemando nuestros computadores tomaremos el caso donde ```H0 = valor```)
- ```alternativa``` depende de lo que nos indique H1 (revisar el tip de arriba)
- ```alpha``` es el nivel de significancia

**NOTA:** Algo interesante es que para usar el valor-p directamente podemos evitar agregar el ```conf.level```, pues el resultado será el mismo independiente el caso.

Del resultado del test podemos obtener el valor-p, el valor-t (o estadístico observado) y el intervalo de confianza:
- Comparar con el valor-t: comparamos según la región de rechazo (revisar formulario)
- Comparar dentro del IC: Hay que revisar si el valor de H0 se encuentra dentro del Intervalo de Confianza
- Comparar con el valor-p: Realizamos la siguente comparación
```r
# alpha = nivel de significancia
valor.p > alpha
```
	- Si al escribir esto en la consola esta retorna ```TRUE``` entonces no podemos rechazar H0
	- Si al escribir esto en la consola esta retorna ```FALSE``` entonces rechazamos H0


**NOTA:** Los 3 test son igualmente concluyentes (todos llegan a la misma conclusión, por lo que si uno es falso los otros dos son falsos), por lo que se recomienda usar el valor-p.


### Test de hipótesis con sigma.test
Este test nos servirá en el caso de revisar la varianza
```r
sigma.test(archivo$datos, sigma = valor, alternative = alternativa, conf.level = 1 - alpha)
```
Nuevamente podemos probar con el valor-p
```r
# alpha = nivel de significancia
valor.p > alpha
```
- Si al escribir esto en la consola esta retorna ```TRUE``` entonces no podemos rechazar H0
- Si al escribir esto en la consola esta retorna ```FALSE``` entonces rechazamos H0


### Test de hipótesis con prop.test
Este test nos servirá en el caso de revisar datos con una distribución Bernoulli:
```r
prop.test(datos_favorables, datos_totales, p = valor, alternative = alternativa, conf.level = 1 - alpha)
```

Nuevamente podemos probar con el valor-p
```r
# alpha = nivel de significancia
valor.p > alpha
```
- Si al escribir esto en la consola esta retorna ```TRUE``` entonces no podemos rechazar H0
- Si al escribir esto en la consola esta retorna ```FALSE``` entonces rechazamos H0


## Test de Independencia
Este test nos sirve para revisar si dos parámetros son independientes.

Para esto se genera un test de hipótesis donde:
- H0: los parámetros son independientes
- H1: los parámetros son dependientes

Si bien podemos realizar todos los casos dentro del estimador a partir de cierto punto sería un exceso por lo que dentro de R también podemos utilizar ```chisq.test```.

```R
# Primero realizamos una tabla doble con los datos que buscamos comparar
tabla <- table(archivo$datos_1, archivo$datos_2)

chisq.test(tabla)
```

Nuevamente podemos probar con el valor-p
```r
# alpha = nivel de significancia, por lo general 0.05
valor.p > alpha
```
- Si al escribir esto en la consola esta retorna ```TRUE``` entonces hay independencia entre los parámetros
- Si al escribir esto en la consola esta retorna ```FALSE``` entonces hay dependencia entre los parámetros


## Otros comandos que aparecieron en los laboratorios
### Test de hipótesis de media de dos muestras
Si queremos comparar la media de una muestra ```datos_1``` (de tipo cuantitativa) con respecto a otra muestra ```datos_2``` (de tipo cualitativa) hacemos lo siguiente:

```R
# Primero creamos filtros
filtro.1 <- (archivo$datos_2 == 'filtro_1')
filtro.2 <- (archivo$datos_2 == 'filtro_2')

t.test(archivo$datos_1[filtro.1], archivo$datos_2[filtro.2], alternative = alternativa, conf.level = 1 - alpha)
```

Esto nos sirve para ver si la media de los ```datos_1``` con filtro ```filtro.1``` es igual a la media de los ```datos_1``` con filtro ```filtro.2```, Es decir:
- H0: media[filtro.1] =  media[filtro.2]
- H1: media[filtro.1] != media[filtro.2]

Luego, podemos comparar mediante el valor-p
```r
# alpha = nivel de significancia, por lo general 0.05
valor.p > alpha
```
- Si al escribir esto en la consola esta retorna ```TRUE``` entonces las medias son iguales
- Si al escribir esto en la consola esta retorna ```FALSE``` entonces las medias no son iguales

### Test de hipótesis para Bernoulli de dos muestras
Digamos que tenemos la siguiente tabla:

|	   	 	 |	 X1	  |	  X2   |
|------------|--------|--------|
|	**Y1**	 | valor1 | valor2 |
|	**Y2**	 | valor3 | valor4 |

Donde:
- X1 y X2 son datos de ```archivo$datos_1```
- Y1 e Y2 son datos de ```archivo$datos_2```

Y queremos comparar la proporción de datos en X1 que cumplan con Y1 vs los datos en X1 que cumplan con Y2.

Realizamos lo siguiente:
```R
# Primero es ideal hacer una tabla conjunta:
table(archivo$datos_1, archivo$datos_2)

# Sacamos la suma total de X1 e X2
x1 <- sum(archivo$datos_1 == 'X1')
x2 <- sum(archivo$datos_1 == 'X2')

# Sacamos la suma de los filtros
x1.y1 <- sum(archivo$datos_2[archivo$datos_1 == 'X1'] == 'Y1')
x1.y2 <- sum(archivo$datos_2[archivo$datos_1 == 'X1'] == 'Y2')

# Realizamos el test
prop.test(c(x1.y1, x1.y2), c(x1, x2), alternative = alternativa, conf.level = 1 - alpha)
```
