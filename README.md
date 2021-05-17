# Analisi database

Per l'analisi dati abbiamo scelto di utilizzare 4 grafici diversi: boxplot, histogram, barplot e heatmap

Prima di poter realizzare i grafici dobbiamo però connetterci al database

```{r connection, include=FALSE}
library("RPostgreSQL")
drv <- dbDriver("PostgreSQL")
con <- dbConnect(drv, dbname="conferenze",
                 host="127.0.0.1", port=5432, user="postgres", password="postgres")
```

### Boxplot

Per il boxplot abbiamo deciso di rappresentare la quantità di articoli per ogni conferenza già passata

```{r dati, include=FALSE}
numero_articoli_passate <- dbGetQuery(con, "SELECT numero_articoli 
                                            FROM conferenze.conferenza 
                                            WHERE stato='PASSATA'")
```

```{r}
grafico_articoli <- boxplot(numero_articoli_passate$numero_articoli, horizontal = T, col = "yellow")
```

![Boxplot](Risorse/boxplot.png)

### Histogram

Per l'istogramma mostriamo gli anni di nascita di tutti i Network Administrator presenti nel database

```{r include=FALSE}
network_administrator <- dbGetQuery(con, "SELECT data_nascita 
                                          FROM conferenze.persona
                                          WHERE professione='Network Administrator'")
```

Una volta eseguito il query scartiamo giorno e mese dalle date per una visualizzazione delle didascalie più pulita

```{r}
a <- format(as.Date(network_administrator$data_nascita,format="%d/%m/%Y"), "%Y")
a <- as.numeric(a)
hist(a, breaks = 20, col = rgb(0.2,0.8,0.5,0.5), border = F, freq = T, las=1, main= "Birth Dates", xlab = "")
```

![Histogram](Risorse/hist.png)

### Barplot

Abbiamo invece utilizzato il barplot per mostrare la quantità di professionisti in ciascuna disciplina all'interno di uno specifico Working Group (in questo caso WG-1.1)

```{r}
job_in_wg <- dbGetQuery(con, "SELECT persona.professione 
                              FROM conferenze.persona LEFT JOIN conferenze.membro_wg 
                                                      ON persona.cf = membro_wg.cf 
                              WHERE membro_wg.numero_wg = 1 AND membro_wg.comitato_tecnico = 1")
```

```{r}
par(mar=c(2,8,0,1))
barplot(table(job_in_wg$professione),horiz = T, las=1, cex.names = 0.6, col = "#87CEFA")
```

![Barplot](Risorse/barplot.png)

### Heatmap

L'heatmap rappresenta le distribuzioni di professioni e specializzazioni all'interno di uno specifico Working Group (in questo caso WG-2.5)

```{r}
prof_spec <- dbGetQuery(con, "SELECT persona.professione, persona.specializzazione 
                              FROM conferenze.persona LEFT JOIN conferenze.membro_wg 
                                                      ON persona.cf = membro_wg.cf 
                              WHERE membro_wg.numero_wg = 5 AND membro_wg.comitato_tecnico = 2")

```

Una volta eseguito il query ne abbiamo usato il risultato per riempire una matrice le cui dimensioni sono rispettivamente professione e specializzazione e dove il valore di ciascuna cella rappresenta quante persone abbiano la corrispondente combinazione di professione e specializzazione

```
unique_prof <- unique(prof_spec$professione)
unique_spec <- unique(prof_spec$specializzazione)
m <- matrix(0, nrow = length(unique_prof) ,length(unique_spec))
colnames(m) <- unique_spec
rownames(m) <- unique_prof
for (i in 1:length(prof_spec$professione)) {
  m[prof_spec$professione[i],prof_spec$specializzazione[i]] = m[prof_spec$professione[i],prof_spec$specializzazione[i]]+1
}
```

Infine abbiamo utilizzato la matrice generata per realizzare la heatmap

```
heatmap(m, Rowv = NA, Colv = NA, col= rev(heat.colors(3)), cexCol = 0.8, cexRow = 0.7, margins = c(10,1))
legend(x=0.001, y=1, legend = c(0, 1, 2), fill = colorRampPalette(rev(heat.colors(3)))(3))
```

![Heatmap](Risorse/heatmap.png)

