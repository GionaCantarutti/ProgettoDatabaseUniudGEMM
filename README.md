# Introduzione

Il progetto riguarda la creazione di un database di supporto all'organizzazione delle working conferences del IFIP.
La consegna del problema è la seguente:

“Si vuole progettare una base di dati di supporto all'organizzazione delle working conferences dell'IFIP (International Federation for Information Processing). Una IFIP Conference è una conferenza internazionale intesa a riunire esperti di tutti i paesi aderenti al IFIP per discutere problemi che interessano uno o più IFIP Working Group. Ogni Working Group opera sotto gli auspici di un Technical Committee costituito dai rappresentanti nazionali dei paesi aderenti al IFIP.
Alla conferenza possono partecipare solo persone che hanno ricevuto un invito. L'invito è inviato a tutti i membri dei Working Groups e Technical Committees interessati. Il numero delle persone che parteciperanno ai lavori deve essere superiore ad una soglia minima, per garantire la copertura dei costi, ed inferiore ad una soglia massima, per non superare le capacità ricettive delle strutture.
La conferenza è organizzata da due comitati: il Comitato di Programma e il Comitato Organizzatore. Il primo cura gli aspetti scientifici della conferenza, nominando il Comitato dei Revisori, che esaminerà gli articolo sottomessi alla conferenza e deciderà quali articoli accettare, rispettando il vincolo sul numero massimo prestabilito. Il secondo cura gli aspetti finanziari e logistici, gli inviti e la pubblicità. Ogni comitato è costituito da esperti ed è previsto un Chairman per ogni comitato e un General Chairman per la conferenza. Tutti i comitati lavorano utilizzando dati comuni che vanno raccolti ed elaborati in modo consistente.
Si definisca uno schema Entità-Relazioni che descriva il contenuto informativo del sistema, illustrando con chiarezza le eventuali assunzioni fatte. Lo schema dovrà essere completato con attributi ragionevoli per ciascuna entità (identificando le possibili chiavi) e relazione. Vanno specificati accuratamente i vincoli di cardinalità e partecipazione di ciascuna relazione.”

Abbiamo svolto il progetto in varie fasi:
- Analisi dei requisiti
- Progettazione concettuale
- Progettazione logica
- Progettazione fisica
- Implementazione
- Analisi dei dati

# Glossario

- Conferenza: Singola conferenza IFIP.
- Comitato: Comitato generico. Composto da almeno un membro e con esattamente un Chairman.
- Comitato Tecnico: Comitato assegnato ad un Working Group in una conferenza.
- Comitato di Programma: Gestisce aspetti scientifici della conferenza. Nomina comitato dei revisori.
- Comitato Organizzatore: Gestisce aspetti logistici finanziari. Inoltra gli inviti.
- Comitato dei Revisori: Approva o respinge articoli sottomessi alla conferenza.
- Comitato dei Revisori: Approva o respinge articoli sottomessi alla conferenza.
- Working Group: Gruppo interessato ad una o più specifiche conferenze.
- Persona: Individuo generico.
- Rappresentante: Membro di un Working Group che rappresenta una nazione.
- Chairman: Individuo a capo di un comitato.
- General Chairman: Individuo a capo di una conferenza.
- Invitato: Individuo invitato ad una conferenza.
- Articolo: Articolo sottomesso o approvato ad una o più conferenze. C'è un limite massimo di articoli approvabili per ogni conferenza.

# Analisi dei requisiti

- Analizzando il testo della consegna ed espandendo utilizzando risorse in rete abbiamo riconosciuto i seguenti fondamentali requisiti:
- Il database è atto alla gestione di conferenze IFIP.
- Le conferenze trattano problemi di interesse per uno o più Working Groups.
- A ogni Working Group è assegnato un Comitato Tecnico.
- I comitati tecnici sono 13 e si dividono per argomenti trattati.
- Un Comitato Tecnico è composto da rappresentanti nazionali.
- Alla conferenza partecipano solo e soltanto gli invitati.
- Ogni membro di un comitato lla conferenza riceve l'invito.
- Il numero dei partecipanti a una conferenza deve rispettare requisiti minimi e massimi.
- Il Comitato di Programma gestisce gli aspetti scientifici della conferenza.
- Il Comitato Organizzatore si occupa degli aspetti logistici della conferenza.
- Il Comitato Organizzatore inoltra gli inviti.
- Il Comitato di Programma nomina il Comitato dei Revisori.
- Il Comitato dei Revisori approva o respinge gli articoli sottomessi alla conferenza.
- Gli articoli sottomessi non ancora approvati non vengono modellati nel database.
- Non possono essere accettati più di massimo di articoli in una data conferenza.
- Ogni comitato (tranne quelli tecnici) ha un Chairman.
- Ogni conferenza ha un General Chairman.
- I comitati (tranne quelli tecnici) sono relativi a una singola conferenza.
- Un individuo può far parte di più di un comitato allo stesso tempo.
- Un individuo può far parte di più Working Groups o comitati tecnici allo stesso tempo.
- Un individuo può essere invitato anche se non appartiene a un comitato.
- E' necessario modellare uno storico dei comitati tecnici.
- Le conferenze vanno generalizzate in base al loro status (indetta, fissata e passata).
- Una persona non può fare parte di un Comitato Tecnico senza fare parte di uno dei suoi Working Group.
- Un Working Group può non avere partecipanti come alla creazione o quando viene spopolato.
- I comitati di conferenza a essa legati vengono creati prima di quest'ultima.
- I comitati di conferenza vengono creati già con il Chairman assegnato.
- La conferenza viene creata con il General Chairman assegnato.

# Progettazione concettuale

## Diagramma E-R

\*immagine\*

# Progettazione logica

Seguono rispettivamente la tabella volumi e la tabella delle frequenze, contenenti dati arbitrari ma verosimili.

## Tabella volumi

\*tabella\*

## Tabella frequenze

\*tabella\*

## Analisi delle ridondanze

Utilizzando le precedenti tabelle abbiamo analizzato la possibilità di utilizzare ridondanze per rappresentare alcuni dati e abbiamo ottenuto i seguenti risultati:

* **Da mantenere:**
  - Il numero di membri in un working group
  - Il conteggio degli articoli approvati per la conferenza
  - Il numero di membri in un comitato

* **Da eliminare:**
  - Il numero di presenze in conferenze passate
  - La validità delle conferenza
  - Il conteggio degli inviti alla conferenza

Per brevità riportiamo il processo di analisi di solo uno di questi dati, in particolare il numero di membri in un comitato.

### Analisi numero di membri in un comitato:
Seguono le operazioni che interagiscono con il numero di membri in un comitato con la loro tabella degli accessi e frequenza di utilizzo, divise nei due casi (presenza e assenza di ridondanza).

#### Con ridondanza:

\* analisi con schemini \*

#### Senza ridondanza:

\* analisi con schemini \*

Come si evince dall’analisi, mantenere la ridondanza riduce gli accessi giornalieri di oltre 10 volte.

# Analisi database

Per l'analisi dati abbiamo scelto di utilizzare 4 grafici diversi: boxplot, histogram, barplot e heatmap

Prima di poter realizzare i grafici dobbiamo però connetterci al database

```{r connection, include=FALSE}
library("RPostgreSQL")
drv <- dbDriver("PostgreSQL")
con <- dbConnect(drv, dbname="conferenze",
                 host="127.0.0.1", port=5432, user="postgres", password="postgres")
```

## Boxplot

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

## Histogram

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

## Barplot

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

## Heatmap

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

