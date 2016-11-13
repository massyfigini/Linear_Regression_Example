# Esempio di regressione lineare semplice
Massimiliano Figini  
13 novembre 2016  




### Diamond

La libreria UsingR contiene "diamond",  con i prezzi di 48 diamanti (in dollari di Singapore) e la loro grandezza (in carati).
Importo anche ggplot2 per il grafico.


```r
library(UsingR)
data(diamond)
library(ggplot2)
```


### Grafico

"diamond" contiene i prezzi di 48 diamanti (in dollari di Singapore) e la loro grandezza (in carati).

Codice per il grafico con la retta di regressione


```r
# imposto il grafico
g = ggplot(diamond, aes(x = carat, y = price))

# etichetto gli assi
g = g + xlab("Dimensione (carati)")
g = g + ylab("Prezzo ($ Singapore)")

# inserisco i dati: alpha=0.2 per dare trasparenza e vedere così le sovrapposizioni
g = g + geom_point(size = 6, colour = "blue", alpha=0.2)

# retta di regressione
g = g + geom_smooth(method = "lm", colour = "red")
g
```

![](Regressione_Lineare_Semplice_files/figure-html/grafico-1.png)<!-- -->


### Parametri

Il grafico mostra chiaramente una correlazione lineare tra le due variabili.
Calcolo i parametri della regressione


```r
rl <- lm(price ~ carat, data = diamond)
coef(rl)
```

```
## (Intercept)       carat 
##   -259.6259   3721.0249
```

Quindi beta0 è circa -260 (sarebbe il prezzo di un diamante da zero carati, che non esiste), e beta1 è 3721: per ogni aumento di un carato ci aspettiamo che il prezzo di un diamante aumenti di 3721 dollari.

Per un valore dell'intercetta più interpretabile possiamo utilizzare una funzione che trasforma beta0 nel prezzo atteso per un diamante di dimensione media. beta1 naturalmente rimane uguale:


```r
lm(price ~ I(carat - mean(carat)), data = diamond)$coefficient
```

```
##            (Intercept) I(carat - mean(carat)) 
##               500.0833              3721.0249
```

L'aumento di un decimo di carato naturalmente aumenta di un decimo il prezzo del diamante rispetto all'aumento di un carato:


```r
lm(price ~ I(carat * 10), data = diamond)$coefficient
```

```
##   (Intercept) I(carat * 10) 
##     -259.6259      372.1025
```


### Previsioni

Prevediamo il prezzo di 3 diamanti da 0.15, 0.22, 0.31 e 0.40 carati:


```r
# vettore con la dimensione dei diamanti di cui stimare il prezzo
xn <- c(0.15, 0.22, 0.31, 0.40)

# previsione
predict(rl, newdata = data.frame(carat = xn))
```

```
##         1         2         3         4 
##  298.5278  558.9996  893.8918 1228.7840
```

```r
# è equivalente a calcolarlo tramite la funzione di regressione
coef(rl)[1] + coef(rl)[2] * xn
```

```
## [1]  298.5278  558.9996  893.8918 1228.7840
```


### Residui

Calcoliamo i residui


```r
# memorizzo i valori attesi della regressione
pred <- predict(rl)

# memorizzo i residui
e <- resid(rl)

# controllo che il valore del prezzo dei diamanti sia equivalente a quello delle previsioni sommati agli errori
round(sum(diamond$price-(pred+e)), digits=10)
```

```
## [1] 0
```

La somma dei residui è zero, tutto corretto.


### Summary

Tutti i risultati della regressione, compreso l'R^2 e i dati della t per poter fare inferenza possiamo vederli con:


```r
summary(rl)
```

```
## 
## Call:
## lm(formula = price ~ carat, data = diamond)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -85.159 -21.448  -0.869  18.972  79.370 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -259.63      17.32  -14.99   <2e-16 ***
## carat        3721.02      81.79   45.50   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 31.84 on 46 degrees of freedom
## Multiple R-squared:  0.9783,	Adjusted R-squared:  0.9778 
## F-statistic:  2070 on 1 and 46 DF,  p-value: < 2.2e-16
```
