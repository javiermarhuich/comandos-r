# Cheatsheet comandos Laboratorios [R]

## LABORATORIO 1

### Cargar un archivo
Ve a ```File``` > ```Import dataset``` y selecciona:
- Archivos XLS: usar ```From Excel```
- Otros archivos: usar ```From text (base)```

### Ver un archivo
```r
archivo <- read.csv2('direccion.archivo')
view(archivo)
```

### Ver los primeros datos de un archivo
```r
head(archivo)
```

### Ver los ultimos datos de un archivo
```r
tail(archivo)
```

### Ver la dimension un archivo
```r
dim(archivo) # retorna [FILAS 	COLUMNAS]
```

### Variables 'NA'
En el caso que tengamos variables 'NA' y queremos obtener funciones como la media de una selección de datos
```r
Filtro = is.na(archivo$datos) #Buscará todos los NA
archivo$datos[Filtro] = mean(archivo$datos, na.rm = TRUE) #Modificará los NA por la media

#Ahora por ejemplo podemos hacer lo siguiente sin que nos marque error
mean(archivo$datos)
```

### Reescribir variables
Digamos que las variables de archivo vienen punteadas ('1. Alto', '2. Medio', '3. Bajo'), esto puede llegar a ser molesto, por lo que hay que eliminar las numeraciones

```r
archivo$datos = gsub('1. ','',archivo$datos)
archivo$datos = gsub('2. ','',archivo$datos)
archivo$datos = gsub('3. ','',archivo$datos)
```

### Hacer un filtro de datos otra seleccion
Para filtrar datos de la selección ```'datos1'``` con los datos de la selección ```'datos2'```
```r
#Ejemplo: queremos todos los datos de datos2 mayores a 50

# Primero generamos un objeto que indique True(1) si es que > 50 y False(0) si <= 50

DatosDos <- c() #ESTO SE CONOCE COMO VARIABLE DICOTÓMICA
for (i in (1:length(archivo$datos2))){
	DatosDos[i] <- ifelse(archivo$datos2[i] > 50, 1, 0)
	#ifelse funciona (prueba a realizar, si es verdadero, si es falso)
}

# Creamos los filtros
Filtro1 = (DatosDos == 1) # Todos los TRUE
Filtro2 = (DatosDos == 0) # Todos los FALSE


# Luego podemos sacar funciones de datos como la media dependiendo el filtro

mean(archivo$datos1[Filtro1]) #Media de todos los datos1 que coincidan con los >50 de datos2
mean(archivo$datos1[Filtro2]) #Media de todos los datos1 que coincidan con los <=50 de datos2
```


## LABORATORIO 2

### Obtener un resumen del archivo
```r
summary(archivo)
```


### Obtener un resumen de los datos
```r
summary(archivo$datos)
```
Esto nos puede indicar si los datos son cualitativos o cuantitativos

- Cualitativos: Separa sus datos según objetos dentro
- Cuantitativos: Separa en mínimo, 1° cuartil, media, mediana, 3° cuartil, máximo

### DATOS CUALITATIVOS
#### Ordenar datos en una tabla
Esto ayudará a obtener probabilidades, porcentajes y gráficos

```r
table(archivo$datos)
```

#### Tabla de porcentajes
```r
prop.table(table(archivo$datos)) # en 0.x
100*prop.table(table(archivo$datos)) # En x%
```

#### Gráficos
Los gráficos soportados por este tipo de datos son los barplot (barras) y pie

```r
barplot(table(archivo$datos), ylab = 'nombre variable eje y', xlab = 'nombre variable eje x', main = 'título')
```

```r
pie(table(archivo$datos), col = 'color', main = 'título')
# col puede ser una función (ej: rainbow(n))
# n = número de objetos
```

#### Tabla conjunta de dos datos cualitativos

```r
table(archivo$datos ~ archivo$datos2)
# Ahora podemos interactuar con todas las otras funciones
prop.table(table(archivo$datos ~ archivo$datos2) , 1) # Orden segun datos
prop.table(table(archivo$datos ~ archivo$datos2) , 2) # Orden segun datos2
```

### DATOS CUANTITATIVOS
#### Obtener media y varianza
```r
mean(archivo$datos2)
var(archivo$datos2)
```

#### Gráficos
Los gráficos soportados son boxplot y hist
```r
boxplot(archivo$datos,  main='título', xlab = 'nombre variable eje x', ylab = 'nombre variable eje y', names = c())
# names sería un objeto con los nombres de los plots
```

```r
hist(archivo$datos, main='título', xlab = 'nombre variable eje x', ylab = 'nombre variable eje y', col = 'color', add = T)
```
Hist nos permite dar una idea de qué tipo de distribución poseen los datos

Podemos agregar líneas para comparar distribución 
```r
#Ejemplo: distribución normal
distribucion_datos = dnorm(sort(archivo$datos), mean(archivo$datos), var(archivo$datos))
lines(sort(archivo$datos), distribucion_datos)

# También podemos usar curve (sobretodo para EMV)
curve(dgamma(x,shape=eta.em,rate=lambda.em),from=0,to=10,col="red",add=T)
```

### Intersección datos Cualitativos y cuantitativos
Permite comparar los datos cuantitativos en son de los cualitativos
```r
#aggregate(calitativo ~ cuantitativo, función)
aggregate(archivo$datos ~ archivo$datos2, FUN = mean) # Obtiene media

aggregate(archivo$datos ~ archivo$datos2, FUN = sd) # Obtiene desviacion estandar
```
Ahora podemos usar los gráficos cuantitativos BOXPLOT y ver por variable cualitativa


## LABORATORIO 3
### Instalar paquetes
Algunas funciones necesitan usar paquetes que ni R ni RStudio las traen instaladas de fábrica.
Para instalar una librería:
1. Ve a Tools
2. Clickea en "Install Packages.."
3. Se desplegará una ventana de instalación de paquetes, ingresa la librería que buscas en Packages
4. Clickea en "Install"

### Obtener EMV mediante fitdistr
Primero es necesario tener "MASS" instalado.

Luego se importa al documento usando el comando ```library()```.

Después usamos fitdistr para obtener el EMV... pero nosotros sólo necesitamos el estimado, por lo que lo restringimos a ```$estimate```

```r
library(MASS) # Primero importamos MASS
fitdistr(archivo$datos, densfun='normal')

#para EMV estimado
fitdistr(archivo$datos, densfun='normal')$estimate
```

Fitdistr soporta las siguientes distribuciones:
- Normal: ```normal```
- Exponencial: ```exponential```
- Gamma: ```gamma```
- Beta: ```beta```
- Chi-cuadrado: ```chi-squared```
- Geométrica: ```geometric```
- Log-Normal: ```lognormal```, ```log-normal```
- Poisson: ```poisson```
- t-Studient: ```t```
- Binomial Negativa: ```negative binomial```
entre otras.

Dudas revisar la ayuda ingresando ```?fitdistr``` en la consola.

### Comparar EMVs teóricos de una muestra
Digamos que tenemos como EMVs:
- 1/media
- 1/mediana

```r
# Guardamos los datos en variables
est1 <- c()
est2 <- c()

# 200 muestras de 100 elementos
for(i in 1:200){
	muestra <- sample(Focos$Tiempo, 100) # Generamos una muestra aleatoria para el ejemplo
	est1[i] <- 1 / mean(muestra)
	est2[i] <- 1 / median(muestra) 
}

# Podemos graficar
par(mfrow = c(1,2)) #Separar en 2 el plot
hist(est1, col='skyblue')
hist(est2, col='purple')
```

#### Sacar el EMRV
```r
# Sacamos el EMV estimado
fitdistr(archivo$datos, densfun = 'exponential')$estimate



# Digamos que nos dio 0.33
estimado = 0.33

var(est1) + (mean(est1) - estimado)^2
var(est2) + (mean(est2) - estimado)^2
```

El resultado del Error Cuadrático Medio (la ecuación ```var(EMV) + (mean(EMV) - estimado)^2```) más bajo es el mejor estimador


## LABORATORIO 4

### Obtener un EMV de una muestra
Creemos una muestra n de N elementos.
En este caso tomemos la distribución exponencial (lambda = 2), n = 100, N = 1000

```r
n = 100
N = 1000
lambda = 2

mv <- rexp(n*N, lambda) # Generamos y guardamos muestras
muestras <- matrix(mv, nrow = n, ncol = N) # Ordenamos los datos

library(MASS)
emv = function(x){
  return (fitdistr(x, densfun = 'exponential')$estimate)
}

EMV = apply(muestras, 1, emv)

hist(EMV,freq=F,main="Histograma de los EMV",xlab="EMV de las muestras",ylab="Frecuencia relativa")

```