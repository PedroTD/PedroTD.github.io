
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## 1. ObtenciÃ³n del conjunto de datos para el estudio.

En primer lugar se carga la librerÃ­a AppliedPredictiveModeling.

```{r}
library(AppliedPredictiveModeling)
```

Una vez cargada la librerÃ­a, importamos los ficheros de datos con los que vamos a trabajar.

```{r}
data(solubility)
```

Al ejecutar este comando, se cargan 6 ficheros de datos:

  - solTrainX: subconjunto de entrenamiento en las unidades originales (variables predictoras o regresoras).
  - solTrainXtrans: subconjunto de entrenamiento tras aplicarle una serie de transformaciones.
  - solTrainY: vector del logaritmo en base 10 de la solubilidad (variable objetivo) para el conjunto de entrenamiento.
  - solTestX: subconjunto de test en las unidades originales.
  - solTestXtrans: subconjunto test tras aplicarle una serie de transformaciones.
  - solTestY: vector del logaritmo en base 10 de la solubilidad (variable objetivo) para el conjunto test.

A continuaciÃ³n se visualizan las variables predictoras/regresoras contenidas en los ficheros de datos.

```{r}
names(solTestX)
```

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```

De las 228 variables mostradas: 

  - Las 208 primeras son variables binarias que indican la presencia o ausencia de una determinada subestructura quÃ­mica en el compuesto bajo estudio.
  - 16 de las restantes variables, son variables de conteo, que facilitan informaciÃ³n relativa al nÃºmero de enlaces quÃ­micos del compuesto o el nÃºmero de Ã¡tomos de cierta sustancia contenidos en el mismo.
  - Hay 4 variables continuas que describen propiedades del compuesto como su peso molecular o el Ã¡rea superficial.

***

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```

## 2. Cuestiones sobre el fichero "EntrenamientoXY". 

### 2.1. Construye el fichero "EntrenamientoXY" de la siguiente forma:
    
(a) Crea un fichero "EntrenamientoX" eliminando de "solTrainXtrans" todas las variables "FP..." 
que ocupan las 208 primeras posiciones.

&nbsp;
```{r}
EntrenamientoX = solTrainXtrans[,209:ncol(solTrainXtrans)]

names(EntrenamientoX)
```

&nbsp;

(b) Construye el fichero "EntrenamientoXY" aÃ±adiendo a "EntrenamientoX" la variable "solTrainY". 
Las variables de "EntrenamientoX" serÃ¡n las variables regresoras y la variable "solTrainY" la variable respuesta.
            
&nbsp;       
```{r}
EntrenamientoXY = cbind(EntrenamientoX, solTrainY)

names(EntrenamientoXY)
```
&nbsp;

(c) Muestra los 5 primeros casos del fichero "EntrenamientoXY".
       
&nbsp;     
```{r}
head(EntrenamientoXY,5)
```


```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.2. Determina el modelo con una y dos variables regresoras que mejor explica la variable respuesta. Denominaremos a estos modelos M1 y M2, respectivamente.   


En primer lugar se importa la librerÃ­a leaps. Con el comando regsubset (regression subset selection), seleccionamos el mejor modelo con una y dos variables regresoras. 

&nbsp;

```{r}
library(leaps)
summary(regsubsets(solTrainY~., data = EntrenamientoXY, nvmax=2, nbest=1))
```

&nbsp;

El modelo con una sola variable regresora que mejor explica la variable respuesta (solTrainY) es aquel que tiene  a MolWeight como variable explicativa.

Las variables explicativas para el mejor modelo con dos variables son: NumNonAtoms y SurfaceArea1.


Tal y como se especifica en el enunciado, se llamarÃ¡ M1 al modelo con una variable explicativa y M2 al modelo con dos variables. 

&nbsp;

```{r}
M1 = lm(solTrainY~MolWeight, data = EntrenamientoXY)
M2 = lm(solTrainY~NumNonHAtoms+SurfaceArea1, data = EntrenamientoXY)
```


```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.3. En los modelos M1 y M2:
     
(a) Representa los grÃ¡ficos de dispersiÃ³n de los regresores frente a la respuesta.
        
&nbsp;
        
En el modelo M1 con una variable regresora se tiene:

&nbsp;

```{r}
par(mfrow = c(1,1))
plot(EntrenamientoXY$MolWeight, EntrenamientoXY$solTrainY, xlab = "MolWeight",
    ylab = "solTrainY")
```
 
&nbsp;

Para el modelo M2 con dos variables regresoras:
```{r}
par(mfrow = c(1,2))
plot(EntrenamientoXY$SurfaceArea1, EntrenamientoXY$solTrainY, xlab = "SurfaceArea1", 
    ylab = "solTrainY")
plot(EntrenamientoXY$NumNonHAtoms, EntrenamientoXY$solTrainY, xlab = "NumNonHAtoms", 
    ylab = "solTrainY")
```

&nbsp;      
    
(b) ObtÃ©n los EMC de los parÃ¡metros, los intervalos de confianza para los parÃ¡metros y los            p-valores asociados a los tests.

&nbsp;

**Estimadores de mÃ­nimos cuadrados de los parÃ¡metros.**

&nbsp;

Para el modelo M1 se tiene:

```{r}
M1$coefficients
```        

&nbsp;

Para el modelo M2:

```{r}
M2$coefficients
```      

&nbsp;
      
**Intervalos de confianza para los parÃ¡metros.**      

&nbsp;

Para el modelo M1 se tiene:

```{r}
confint(M1, level = 0.95)
```

&nbsp;

Para el modelo M2:

```{r}
confint(M2, level = 0.95)
```

&nbsp;

**ObtenciÃ³n de los p-valores asociados a los tests.**

&nbsp;

Para el modelo M1 se tiene:

```{r}
summary(M1)$coefficients
```

&nbsp;

Para el modelo M2:

```{r}
summary(M2)$coefficients
```

&nbsp;

Los p-valores asociados a los tests se muestran en la columna de la derecha ($Pr(>|t|)$) para ambos modelos.

&nbsp;

(c) Interpreta el significado de los EMC.

&nbsp;
        
Los estimadores de mÃ­nimos cuadrados del modelo (EMC) son los valores de los coeficientes que minimizan la suma de cuadrados del error (SCE). Es decir son los valores de los estimadores de los $\beta_i$ que proporcionan el modelo de mejor ajuste para los datos dados.

Para el modelo M1 se tiene:

```{r}
M1$coefficients
```

La interpretaciÃ³n de estos coeficientes es la siguiente: 

  * $\widehat\beta_0=11.954$. Es el valor de la variable explicativa (solTrainY) cuando MolWeight vale 0. 
  * $\widehat\beta_1=-2.82$. Es el decremento que experimenta solTrainY cuando se incrementa MolWeight en una unidad.

&nbsp;    
&nbsp;

Para el modelo con dos variables regresoras (M2) se tiene:

```{r}
M2$coefficients
```

La interpretaciÃ³n de estos coeficientes es la siguiente: 

  * $\widehat\beta_0=5.619$. Es el valor de la variable explicativa (solTrainY) cuando NumNonHAtoms y SurfaceArea1 valen 0. 
  * $\widehat\beta_1=-4.143$. Es el decremento que experimenta solTrainY cuando se incrementa NumNonHAtoms en una unidad, manteniÃ©ndose SurfaceArea1 constante.
  * $\widehat\beta_2=0.331$. Es el incremento que experimenta solTrainY cuando se incrementa SurfaceArea1 en una unidad, manteniÃ©ndose NumNonHAtoms constante.

&nbsp;

(d) Â¿QuÃ© conclusiones se obtienen de los p-valores resultantes?
        
&nbsp;

Con vistas a extraer conclusiones de los p-valores obtenidos, se vuelven a mostrar a continuaciÃ³n para ambos modelos (M1 y M2).

&nbsp;

Para el modelo M1 se obtuvieron los siguientes p-valores asociados a los tests de hipÃ³tesis:

```{r}
summary(M1)
```

&nbsp;

Procediendo del mismo modo para el modelo M2:

```{r}
summary(M2)
```

&nbsp;

En los contrastes de hipÃ³tesis realizados, se fijÃ³ un nivel de significaciÃ³n de $\alpha=P[Error de tipo I]=P[Rechazar H_0 | H_0 cierta]=0.05$.
En ambos modelos, se estableciÃ³ como hipÃ³tesis nula que $\beta_i = 0$ para cada uno de los coeficientes ($\beta_0 , \beta_1$ para M1 y $\beta_0, \beta_1 ,\beta_2$ para M2).

Por ser los p-valores ($Pr(>|t|) < 2*10^{-16}$) menores que el nivel de significaciÃ³n fijado, se pueden rechazar las hipÃ³tesis nulas establecidas. Esto implica que existirÃ­a relaciÃ³n entre las variables regresoras y la variable respuesta, al ser los coeficientes distintos de 0.


```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.4. En el modelo M2, determina la estimaciÃ³n del valor medio de la respuesta, el intervalo de confianza y el intervalo de predicciÃ³n (ambos al 95%) correspondiente a los valores (3,7) de los regresores. (Justifica que (3,7) es un valor apropiado para realizar lo que se pide). Presentar los datos resultantes en una tabla (con sÃ³lo dos decimales).

&nbsp;
 
**JustificaciÃ³n de la  adecuidad del valor (3,7)**

Antes de realizar predicciones o estimaciones de la respuesta media a partir del modelo, hay que asegurar que los valores dados de las variable regresoras para los que se desea hacer la estimaciÃ³n estÃ¡n dentro del cierre convexo de los datos proporcionados por el subconjunto de entrenamiento (EntrenamientoXY).

Para ello se representa la variable SurfaceArea1 frente a la variable NumNonHAtom.

```{r}
plot(EntrenamientoXY$SurfaceArea1, EntrenamientoXY$NumNonHAtoms, 
     main = "ComprobaciÃ³n cierre convexo de los datos", xlab = "SurfaceArea1",
     ylab = "NumNonHAtom", col = "red")

points(7, 3, col = "blue", pch = 21, bg = "blue")
```


En la figura anterior se muestra una representaciÃ³n de los valores que toman las variables regesoras en cuestiÃ³n (NumNonHAtoms y SurfaceArea1) en el subconjunto de entrenamiento (EntrenamientoXY). Puede verse como el valor pedido (NumNonHAtoms=3,SurfaceArea1=7), que se muestra en el grÃ¡fico con un punto azul, estÃ¡ dentro del cierre convexo de los datos.

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```

**EstimaciÃ³n del valor medio en la respuesta.**

&nbsp;

```{r}
dataframe = data.frame(NumNonHAtoms=3,SurfaceArea1=7)
prediction = predict(M2, dataframe)
prediction
```

&nbsp;

**Intervalo de confianza al 95 %.**

&nbsp;

```{r}
IC_1 = predict(M2, dataframe, interval = "confidence")
IC_1
```


&nbsp;

**Intervalo de predicciÃ³n al 95 %.**

&nbsp;

```{r}
IP_1 = predict(M2, dataframe, interval = "predict")
IP_1
```

&nbsp;

**RepresentaciÃ³n de los resultados en una tabla.**

&nbsp;

```{r}
tabla_resultados = data.frame(round(prediction,2), round(IP_1[2],2), 
                        round(IP_1[3],2), round(IC_1[2],2), round(IC_1[3],2))
row.names(tabla_resultados) = c("(3,7)")
names(tabla_resultados) = c("EstimaciÃ³n", "IC(95%) inf", 
                            "IC(95%) sup", "IP(95%) inf", "IP(95%) sup")
print(tabla_resultados)
```

&nbsp;

**Interpreta la diferencia en amplitud de IC(95%) e IP(95%).**
          
El intervalo de confianza indica que el valor esperado (valor medio) de una serie de observaciones estarÃ¡ contenido entre sus lÃ­mites (en este caso -6.467 y -2.516) con una probabilidad de 0.95.
Por otro lado, el intervalo de predicciÃ³n expresa la probabilidad (en este caso de 0.95) con la que una estimaciÃ³n puntual estarÃ¡ contenida dentro del rango de valores especificado (en este caso -4.586 y -4.397).
De ahÃ­ que el intervalo de confianza sea mÃ¡s amplio que el de predicciÃ³n.
     
          
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.5. Modelo con interacciÃ³n.
     
(a) Construye el modelo M2int, resultante de aÃ±adir a M2 la interacciÃ³n entre sus dos regresores.
        
&nbsp;

```{r}
M2int = lm(solTrainY~NumNonHAtoms*SurfaceArea1, data = EntrenamientoXY)

# Lo anterior es equivalente a:
# M2int = lm(solTrainY~NumNonHAtoms+SurfaceArea1+NumNonHAtoms*SurfaceArea1,
#            data = EntrenamientoXY)
```

&nbsp;
        
(b) Contruye la siguiente tabla comparativa: M1, M2, M2int. Comenta los resultados.
        
&nbsp;

```{r}
tabla_comp_r2 = c(summary(M1)$r.sq, summary(M2)$r.sq, summary(M2int)$r.sq)
tabla_comp_r2_sq = c(summary(M1)$adj.r.sq, summary(M2)$adj.r.sq, summary(M2int)$adj.r.sq)
tabla_comp_rse = c(summary(M1)$sigma, summary(M2)$sigma, summary(M2int)$sigma)
tabla_comp = data.frame(rbind((tabla_comp_r2), (tabla_comp_r2_sq), (tabla_comp_rse)))
row.names(tabla_comp) = c("R2", "R2 ajustado", "Residual standard error")
names(tabla_comp) = c("M1", "M2", "M2int")

print(tabla_comp)
```

&nbsp;

De la observaciÃ³n de la tabla pueden extraerse las siguientes conclusiones:

  * Al pasar del modelo con una variable regresora (M1) al modelo con dos (M2) aumenta de forma significativa el $R^2$ y disminuye el error.
  
  * Puede verse como al aÃ±adir la interacciÃ³n entre las variables regresoras no se produce una mejora significativa del ajuste. Asimismo, el error permanece prÃ¡cticamente constante. 

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.6. Construye el modelo de regresiÃ³n lineal con todos los regresores disponibles en el fichero (Mtodas).
     
&nbsp;

En primer lugar, se genera el modelo Mtodas.

&nbsp;

```{r}
Mtodas = lm(solTrainY~., data = EntrenamientoXY)

summary(Mtodas)
```

&nbsp;

De la observaciÃ³n de los p-valores asociados a los tests, muchos de los cuales son mayores que el nivel de confianza fijado ($\alpha = 0.05$), puede deducirse que muchas de las variables del modelo tendrÃ¡n coeficientes nulos. Esto se debe a que, al ser los p-valores mayores que el nivel de confianza, se aceptarÃ­a la hipÃ³tesis nula que establece que el coeficiente asociado a dicha variable es nulo.

Puede observarse asimismo que ha mejorado el ajuste del modelo ($R^2 = 0.844$) debido a que se ha incluido mucha mÃ¡s informaciÃ³n en el mismo. Como inconvenientes destacables, cabe mencionar el aumento del coste computacional y los problemas de multicolinealidad, que serÃ¡n objeto de estudio en una secciÃ³n posterior. 

&nbsp;

(a) Interpretar los resultados obtenidos en comparaciÃ³n con los de los modelos M1, M2 y M2int considerados en los apartados anteriores.
        
&nbsp;

Para comparar los resultados obtenidos en el modelo Mtodas con los anteriores modelos, vamos a incluirlo en la tabla creada en el apartado anterior.

```{r}
tabla2_comp_r2 = c(summary(M1)$r.sq, summary(M2)$r.sq, summary(M2int)$r.sq, 
                  summary(Mtodas)$r.sq)
tabla2_comp_r2_sq = c(summary(M1)$adj.r.sq, summary(M2)$adj.r.sq, summary(M2int)$adj.r.sq,
                  summary(Mtodas)$adj.r.sq)
tabla2_comp_rse = c(summary(M1)$sigma, summary(M2)$sigma, summary(M2int)$sigma,
                  summary(Mtodas)$sigma)
tabla2_comp = data.frame(rbind((tabla2_comp_r2), (tabla2_comp_r2_sq), (tabla2_comp_rse)))
row.names(tabla2_comp) = c("R2", "R2 ajustado", "Residual standard error")
names(tabla2_comp) = c("M1", "M2", "M2int", "Mtodas")

print(tabla2_comp)

```

&nbsp;

Puede verse como con este Ãºltimo modelo (Mtodas) mejora significativamente el ajuste (aumenta el $R^2$) y disminuye el error. El inconveniente del modelo es el mayor coste computacional y los problemas de multicolinealidad, cuyo estudio se llevarÃ¡ a cabo en una secciÃ³n posterior.

&nbsp;
        
(b) ObtÃ©n los grÃ¡ficos de diagnÃ³sticos y comenta los resultados.
      
&nbsp;

ObtenciÃ³n de los grÃ¡ficos de diagnÃ³stico.

```{r}
par(mfrow = c(2,2))
plot(Mtodas)
```

&nbsp;

De los cuatro grÃ¡ficos de diagnÃ³stico representados se puede extraer la siguiente informaciÃ³n:

  * **Resiuals vs Fitted**. Este grÃ¡fico permite determinar si se cumple la hipÃ³tesis de linealidad del modelo mediante la detecciÃ³n de patrones no lineales entre las variables regresoras y la variable respuesta. El que los residuos estÃ©n equitativamente repartidos a lo largo de la lÃ­nea horizontal es un buen indicador de que no hay relaciones no lineales entre las variables predictoras y la variable respuesta. Por lo tanto, se puede asumir en este caso que existe una relaciÃ³n lineal entre las variables.
  
  * **Normal Q-Q**. Este grÃ¡fico indica si los residuos siguen o no una distribuciÃ³n normal. Al estar los residuos alineados con la recta, se puede asumir que se cumple la hipÃ³tesis de normalidad.
  
  * **Scale-Location**. Mediante este grÃ¡fico se puede determinar si los residuos estÃ¡n equitativamente repartidos en todo el rango de las variables regresoras. En otras palabras, este modelo permite comprobar la validez de la hipÃ³tesis de homocedasticidad. De la observaciÃ³n del grÃ¡fico se puee concluir que los residuos estÃ¡n repartidos de forma equitativa a lo largo del eje horizontal, lo que verifica la hipÃ³tesis de homocedasticidad.  
  
  * **Residuals vs Leverage**. Este grÃ¡fico permite determinar si existen o no observaciones "influencia" en el conjunto de datos. No todas las observaciones outliers son observaciones "influencia". Para que una observaciÃ³n sea "influencia" debe ser ademÃ¡s "high-leverage". Este tipo de observaciones tienen gran peso en la generaciÃ³n de los modelos de regresiÃ³n. Su inclusiÃ³n puede acarrear grandes cambios en el modelo, por lo que a menudo es recomendable eliminarlas del estudio. En este caso no se han detectado observaciones "influencia", que, de estar presentes, se encontrarÃ­an en el extremo superior derecho o inferior derecho de la grÃ¡fica, mÃ¡s allÃ¡ de la lÃ­nea de puntos que marcarÃ­a la distancia de Cook. 

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
        
(c) Calcula los vif (factores de inflaciÃ³n de la varianza) de los regresores. Â¿QuÃ© conclusiÃ³n se obtiene?
       
&nbsp;

El problema de la multicolinealidad surge cuando existe una dependencia lineal "casi exacta" entre las variables regresoras. Cuando esto sucede, es difÃ­cil identificar cuÃ¡l es el que estÃ¡ influyendo en la respuesta.

La colinealidad incrementa la varianza de los estimadores de los coeficientes ($\widehat\beta_i$), conduciendo a la ampliaciÃ³n de los intervalos de confianza y a la reducciÃ³n de la potencia del test (probabilidad de detectar correctamente un $\beta_j = 0$).

Para detectar la correlaciÃ³n entre las variables regresoras se calculan los factores de inflaciÃ³n de la varianza (VIF), que son los elementos diagonales de $R^{-1}$ (inversa de la matriz de correlaciones).

Se verifica que:  $VIF = 1/(1-R_j^{2})$

&nbsp;

```{r}
library(car)
vif(Mtodas, data = EntrenamientoXY)
```

&nbsp;

Los valores del factor de inflaciÃ³n de la varianza mayores de 5 o 10 son indicadores de serios problemas de multicolinealidad. SegÃºn este criterio, casi todas las variables del modelo Mtodas darÃ­an problemas de multicolinealidad, aunque hay variables como NumAtoms, NumNonHAtoms, NumNonHBounds y NumBonds con valores especialmente altos del factor de inflaciÃ³n de la varianza, lo que muestra una fuerte correlaciÃ³n entre ellas, conclusiÃ³n deducible por otra parte a partir de los nombres de las variables.
        
        
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.7. Determina el modelo resultante de una regresiÃ³n paso a paso hacia adelante. Sea MHA el modelo resultante.
     
&nbsp;

La selecciÃ³n de variables consiste en encontrar, entre un amplio conjunto de posibles regresores, un subconjunto apropiado de regresores para el modelo.

El mÃ©todo de selecciÃ³n de variables paso a paso hacia delante comienza considerando el modelo con el tÃ©rmino independiente y sin regresores.

En cada paso se determina el regesor que al aÃ±adirlo a los existentes produce una mayor reducciÃ³n de la suma de cuadrados del error (SCE).
     
&nbsp;

```{r}
library(MASS)

modelo_sin_regresores = lm(solTrainY~1, data = EntrenamientoXY)

MHA = stepAIC(modelo_sin_regresores, scope=list(lower=modelo_sin_regresores, upper=Mtodas),    
              direction="forward", trace=1)

summary(MHA)
```

     
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.8. Construye un grÃ¡fico para comparar $R^2$ ajustado en los siguientes modelos, y comenta los resultados.

&nbsp;

(a) MHA12: Modelo con las dos primeras variables (v1 y v2) que entran en la regresiÃ³n paso a paso hacia adelante.
      
&nbsp;        
      
De la observaciÃ³n del proceso de selecciÃ³n de variables se tiene que las dos primeras variables seleccionadas son: 

  * v1 = MolWeight
  * v2 = SurfaceArea1
  
```{r}
v1 = EntrenamientoXY$MolWeight
v2 = EntrenamientoXY$SurfaceArea1
```
  
Procedemos entonces a crear el modelo MHA12:

```{r}
MHA12 = lm(solTrainY ~ MolWeight + SurfaceArea1, data = EntrenamientoXY)
```


&nbsp;
        
(b) $MHA12 + (v1)^2$, $MHA12 + (v1)^2 + (v1)^3$, $MHA12 + (v2)^2$, $MHA12 + (v2)^2 + (v2)^3$ y $MHA12 + (v1*v2) + (v1)^2 + (v1)^3 + (v2)^2 + (v2)^3$.
        
&nbsp;

Llamaremos a cada modelo:

  * MHA12_a: $MHA12 + (v1)^2$
  * MHA12_b: $MHA12 + (v1)^2 + (v1)^3$
  * MHA12_c: $MHA12 + (v2)^2$
  * MHA12_d: $MHA12 + (v2)^2 + (v2)^3$
  * MHA12_e: $MHA12 + (v1*v2) + (v1)^2 + (v1)^3 + (v2)^2 + (v2)^3$


```{r}
library(ggplot2)

MHA12_a = lm(solTrainY~SurfaceArea1+poly(MolWeight,2, raw=TRUE),data=EntrenamientoXY)
MHA12_b = lm(solTrainY~SurfaceArea1+poly(MolWeight,3, raw=TRUE),data=EntrenamientoXY)
MHA12_c = lm(solTrainY~MolWeight+poly(SurfaceArea1,2, raw=TRUE),data=EntrenamientoXY)
MHA12_d = lm(solTrainY~MolWeight+poly(SurfaceArea1,3, raw=TRUE),data=EntrenamientoXY)
MHA12_e = lm(solTrainY~poly(SurfaceArea1,3,raw=TRUE)+
               poly(MolWeight,3,raw=TRUE)+I(MolWeight*SurfaceArea1),data=EntrenamientoXY)


data_models = c(summary(MHA12)$adj.r.sq, summary(MHA12_a)$adj.r.sq,
                         summary(MHA12_b)$adj.r.sq, summary(MHA12_c)$adj.r.sq,
                         summary(MHA12_d)$adj.r.sq, summary(MHA12_e)$adj.r.sq)

x_val = c(0,1,2,3,4,5)

plot(x_val, data_models, main = "Adjusted R squared", xlab = "Models", ylab = "adj. R2", 
     type = "h", lwd = "5", ylim = c(0.722,0.735))
grid()

```

&nbsp;

De la observaciÃ³n de la grÃ¡fica de $R^2$ ajustado puede concluirse que los 6 modelos ajustan con precisiÃ³n semejante los datos de entrenamiento (EntrenamientoXY).







```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 2.9. AÃ±adir cualquier otro anÃ¡lisis que se considere de interÃ©s.
     
&nbsp;

Como anÃ¡lisis adicional se van a obtener los  dos mejores modelos con 1 a 6 variables regresoras.

&nbsp;

```{r}
best_models = (regsubsets(solTrainY~., data=EntrenamientoXY, nvmax=6, nbest=2))
summary(best_models)
```

&nbsp;
     
Se representan a continuaciÃ³n los modelos generados con regsubset en funciÃ³n de su $R^2$.
     
&nbsp; 
  
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```  
     
```{r}
plot(best_models, scale = "r2")
```

&nbsp; 

Puede comprobarse cÃ³mo, conforme aumenta el nÃºmero de variables del modelo, mejora el ajuste de los datos del conjunto de entrenamiento (aumenta el $R^2$). HabrÃ­a que determinar una situaciÃ³n de compromiso entre un $R^2$ elevado y un coste computacional bajo (menor nÃºmero de parÃ¡metros).

&nbsp; 

***

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```

## 3. Cuestiones sobre el conjunto "TestXY".

### 3.1. Construye el fichero "TestXY" de la siguiente forma:
     
(a) Construye un fichero "TestX" eliminando de "solTestXtrans" todas las variables "FP..." que ocupan las 208 primeras posiciones.
        
&nbsp;

```{r}
TestX = solTestXtrans[,209:ncol(solTestXtrans)]

names(TestX)
```


&nbsp;
        
(b) Construye el fichero "TestXY" aÃ±adiendo a "TestX" la variable "solTestY".

&nbsp;        
      
```{r}
TestXY = cbind(TestX, solTestY)

names(TestXY)
```

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 3.2. Calcula los valores ajustados por el modelo MHA12 para todos los valores de los regresores del conjunto test.
     
&nbsp;

Se aplica en este apartado el modelo MHA12 a los datos del subconjunto test (TestXY) para ver cÃ³mo de acertadas son las predicciones.

&nbsp;

```{r}
prediccion = predict(MHA12, TestXY)

pred_vs_real = data.frame(cbind(prediccion, TestXY$solTestY))
names(pred_vs_real) = c("Prediction", "Y")
head(pred_vs_real,9)
```


     
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 3.3. Calcula los residuos correspondientes al modelo MHA12 para todos os valores de los regresores del conjunto test.
     
&nbsp;     
     
Se calculan en este apartado los residuos del modelo como la diferencia entre los valores reales y los valores predichos de la variable respuesta.

&nbsp;

```{r}
residuals = data.frame(prediccion, TestXY$solTestY, abs(TestXY$solTestY - prediccion))
names(residuals) = c("Prediction", "Y", "Residuals")
head(residuals,15)
summary(residuals)
```



```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 3.4. Calcula el RSE resultante de aplicar el modelo MHA12 sobre el conjunto test.
     
&nbsp;

Se calcula a continuaciÃ³n el RSE del siguiente modo:

&nbsp;

```{r}
library(lattice)
library(ggplot2)
library(caret)

RMSE(prediccion, TestXY$solTestY)
```


&nbsp;
     
El RSE(residual standard error) es una medida de cuanto se desviarÃ¡ en media el valor ajustado por el modelo del valor real que se estÃ¡ intentando predecir.
     

     
```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 3.5. Compara el RSE sobre el conjunto entrenamiento y el test. Comenta los resultados.
     
&nbsp;

```{r}
summary(MHA12) # Se hace el summary para obtener el RSE sobre el conjunto de entrenamiento
```

&nbsp;

Del resumen anterior y del apartado 3.4. se tiene que:

  * RSE entrenamiento: 1.077
  * RSE test: 1.026
     
&nbsp;     
    
Podemos ver que el RSE es similar para ambos conjuntos de datos, lo que quiere decir que el modelo se adapta bien a la nueva informaciÃ³n (subconjunto test).     
     
Se procede a realizar cross-validaciÃ³n k-fold (10 folds) con el conjunto de entrenamiento. Este mÃ©todo hace una divisiÃ³n de los datos del conjunto en 10 partes,  que irÃ¡ tomando una por una para validar el modelo generado ajustando el 90% de los datos restantes.

&nbsp;

```{r, results='asis', echo=FALSE}
cat("\\newpage")
```
   
### 3.6. AÃ±adir cualquier otro anÃ¡lisis que se considere de interÃ©s.
     
&nbsp;

Cuando el conjunto de entrenamiento no es pequeÃ±o, puede estimarse por remuestreo el rendimiento de los modelos. Este mÃ©todo se conoce como validaciÃ³n cruzada.

```{r}
set.seed(100)
ctrl=trainControl(method = "cv", number=10) 

lmFit1=train(x=EntrenamientoX, y=solTrainY, method = "lm", trControl =ctrl )                              
lmFit1
summary(lmFit1)

lmFit1$results
```

&nbsp;

Como puede comprobarse, el modelo de mejor ajuste alcanza un $R^2$ de 0.844.

&nbsp;

Vamos a predecir el conjunto de datos de tests utilizando el modelo generado por cross-validaciÃ³n.

&nbsp;

```{r}
prediccion_cv = predict(lmFit1, TestXY)

pred_cv_vs_real = data.frame(cbind(prediccion, prediccion_cv, TestXY$solTestY))
names(pred_cv_vs_real) = c("Prediction", "Prediction CV", "Y")
head(pred_cv_vs_real,12)
```

Puede verse cÃ³mo mejoran sustancialmente la predicciones cuando se ajusta el modelo mediante cross-validaciÃ³n.

Se calcula por Ãºltimo el RSE para el subconjunto test:

&nbsp;

```{r}
RMSE(prediccion_cv, TestXY$solTestY)
```

&nbsp;

Se obtiene un RSE mÃ¡s pequeÃ±o que en el caso en el que no se aplicÃ³ cross-validaciÃ³n. A continuaciÃ³n se muestran los RSE obtenidos para el subconjunto de entrenamiento y test usando cross-validaciÃ³n:

  * RSE Entrenamiento: 0.817
  * RSE Test: 0.872


***
