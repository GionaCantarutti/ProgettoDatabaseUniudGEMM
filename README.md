# Introduction

This project, ment to test our ability to fully design and develop a database from the ground up, has been developed by four people for a university assignment

The assignment has been originally developed in Italian and this is its translation to english. The original version can be found in this very repo in the file named "relazione"

### Assignment (translated to english)

“The goal is to create a database to support the conferences held by the IFIP (International Federation for Information Processing). An IFIP conference is an international conference ment to unite experts from all participating nations to discuss problems of interest to one or more IFIP Working Groups. Each Working Group works under a Technical Committee made up from national representatives.
Only invited people may participate to the conference. The invitation is sent to every member of each Working Group and Techical Committee intereasted in the conference. The number of participants must be withing a certain range depending on the conference.
The conference is organized by  two committees: the Program Committee and the Logistical Committee. The former handels the scientific aspects of the conference and nominates a Committee of Reviewers that will be tasked with approving the submitted papers for the conference while respecting a maximum threshold. The latter handles the logistical and financial aspects of the conference and it's tasked with sending the invitations. Each committee is made up by experts and each has a chairman. A general chairman is also choosen for the conference.”

The project is split in the following phases:
- Requirement analysis
- Conceptual design
- Logical design
- Physical design
- Implementation
- Data analysis

# Analisi dei requisiti

To develop this anaysis we consulted the [official IFIP website](https://www.ifip.org//) and [its Wikipedia page](https://it.wikipedia.org/wiki/International_Federation_for_Information_Processing).

What follows are the requirements we deemed necessary:

-	The database will handle IFIP Conferences
-	Each conference is held to discuss problems that interest one or multiple Working Groups
-	Each Working Group works under a Technical Committee
-	There are a total of 13 Technical Committees devided by topics
-	Each Working Group is made up by national representatives
-	Only invited people may participate
-	Every member of intereasted committees or Working Groups is invited
-	The number of partecipants must be in range (minP, maxP)
-	Program Committee is in charge of scientific aspects
-	Logistical Committee is in charge of logistics
-	Logistical Committee sends the invitations
-	Program Committee nominates a Committee of Reviewers
-	Committee of Reviewers approves or rejects papers submitted for the conference
-	Submitted articles that are yet to be reviewed are not to be included in the database
-	There cannot be more than maxA papers approved for a given conference
-	Each committee has a chairman
-	Each conference has a general chairman
-	Every committee except Technical Committees are strictly relative to a specific conference
-	Techical Committees exist indipendently of any specific conference and don't have a chairman
-	An individual is not restricted in any way from being part of multiple committees at the same time
-	An individual can be invited to a conference without being part of any committee
-	It is necessary to include a history of each Working Group in the database
-	An individual cannot be part of a Technical Committee without being part of one of its Working Groups
-	A Working Group can have no members in special situations
-	A chairman for a committee must be choosen when the committee is created
-	A genral chairman for a conference must be choosen when the conference is created

# Conceptual design

The conceptual design is the first phase in which the database starts to take shape. The main objective of such phase is to develop an E-R shcema that properly fulfills all the previously ensablished requirements.

## E-R Schema

![Diagramma E-R](Risorse/Diagramma_E-R.png)

It's worth noting how conferences are split into three possible states and that such states are modeled by using generalizations so that common attributes would not be repeated.

It is then important to analyze the cycles that appear in the schema:

![Diagramma E-R con cicli](Risorse/Diagramma_E-R_con_cicli.png)

1. The organizational committee shall excusively invite to its conference

2. Every active member of a Working Group shall be invited to any conference in which the Working Group takes part

3. The General Chairman shall be invited to the conference it is assigned to

4. The chairman of a committee shall be one of its members

5. All members of a committee shall be invited to the conference for which the committee was created

6. This cycle doesn't require any additional constraints

7. Articles must be approved by a revision committee for the conference the committee handles

8. The program committee shall nominate a revision committee for the conference it handles

# Logical design

Lo scopo della progettazione logica è quello di giungere ad un modello relazionale. Prima di poterlo costruire, però, è fondamentale analizzare le ridondanze presenti nello schema E-R e determinare quali di esse vadano mantenute e quali scartate.

Preliminariamente alla progettazione logica è necessario avere però a disposizione le tabelle dei volumi e delle frequenze necessarie all'analisi delle ridondanze. Tali tabelle, non provvedute dalla consegna, sono popolate arbitrariamente cercando di mantenere verosimilità rispetto al dominio e tenendo conto degli ipotetici _use case_ del database

Seguono quindi le suddette:

## Tabella volumi

![Tabella volumi](Risorse/Tabella_Volumi.jpg)

## Tabella frequenze

Per alcuni valori è stato ritenuto opportuno distinguere tra frequenza mediana e frequenza di picco in quanto alcune operazioni, sebbene raramente utilizzate, hanno picchi di utilizzo molto alti in specifiche circostanze

![Tabella frequenze](Risorse/Tabella_Frequenze.png)

## Analisi delle ridondanze

Utilizzando le precedenti tabelle è stata analizzata la possibilità di utilizzare ridondanze per rappresentare alcuni dati, seguono i risultati:

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
Seguono le operazioni che interagiscono con il numero di membri in un comitato con la loro tabella degli accessi e frequenza di utilizzo, divise nei due casi: presenza e assenza di ridondanza.

#### Con ridondanza:

![Con ridondanza 1](Risorse/Analisi_conridondanze.png)

![Con ridondanza 2](Risorse/Analisi_conridondanze2.png)

**Costo giornaliero totale: 90**

#### Senza ridondanza:

![Senza ridondanza 1](Risorse/Analisi_senzaridondanze.png)

![Senza ridondanza 2](Risorse/Analisi_senzaridondanze2.png)

**Costo giornaliero totale: 961**

Come si evince dall’analisi, mantenere la ridondanza riduce gli accessi giornalieri di oltre 10 volte.

## Schema E-R ristrutturato

In preparazione allo sviluppo del modello relazionale è necessario ristrutturare significativamente lo schema E-R:

![E-R ristrutturato](Risorse/E-R_ristrutturato_con_chiavi.png)

Oltre ad aver rimosso gli attributi ridondanti ritenuti non necessari durante l'analisi delle ridondanze è anche stata rimossa la generalizzazione delle conferenze e dei comitati.   

Per le conferenze è stato aggiunto l'attributo "Stato" alla conferenza il quale, con l'aiuto di alcune restrizioni aggiuntive, ricoprerà lo stesso ruolo della generalizzazione rimossa.

Per quanto riguarda i comitati, invece, è bastato semplicemente rimuovere l'entità generica e aggiungere le sue relazioni su ciascuno dei tipi di comitato individualmente

## Schema relazionale

Partendo dallo schema E-R opportunamente ristrutturato è stato sviluppato il seguente schema relazionale

![Schema relazionale](Risorse/Tabella_relazionale.jpg)

Nello schema sono evidenziate in verde gli attributi _not null_, sottolineate le _primary keys_ e in corsivo le _foreign keys_

# Progettazione fisica

Il linguaggio utilizzato per progettare fisicamente il database è Postresql, sono di seguito riportati alcuni frammenti di codice rilevanti.

Visto che le conferenze si distinguono in tre stati è stato ritenuto opportuno creare un tipo di dati dedicato.

```sql
CREATE TYPE STATO_CONFERENZA AS ENUM
(
'INDETTA', 'FISSATA', 'PASSATA'
);
```

La tabella centrale e più importante del database è "conferenza", qui sotto il codice per crearla.

```sql
CREATE TABLE conferenze.conferenza
(
  data date NOT NULL,
  argomento character varying(50) NOT NULL,
  stato stato_conferenza NOT NULL,
  min_iscritti smallint NOT NULL,
  max_iscritti smallint NOT NULL,
  conclusioni character varying(100),
  termine_conferenza date,
  luogo character varying(100),
  numero_articoli smallint DEFAULT 0,
  general_chairman character(16),
  numero_membri_org smallint DEFAULT 0,
  chairman_org character(16) NOT NULL,
  numero_membri_prog smallint DEFAULT 0,
  chairman_prog character(16) NOT NULL,
  numero_membri_rev smallint DEFAULT 0,
  chairman_rev character(16) NOT NULL,
  CONSTRAINT conferenza_pkey PRIMARY KEY (data, argomento),
  CONSTRAINT conferenza_chairman_org_fkey FOREIGN KEY (chairman_org, data, argomento)
  	REFERENCES conferenze.membro_org (cf, data_conferenza, argomento_conferenza) MATCH FULL
  	ON UPDATE CASCADE ON DELETE SET NULL
            DEFERRABLE INITIALLY DEFERRED,
  CONSTRAINT conferenza_chairman_prog_fkey FOREIGN KEY (chairman_prog, data, argomento)
  	REFERENCES conferenze.membro_prog (cf, data_conferenza, argomento_conferenza) MATCH FULL
  	ON UPDATE CASCADE ON DELETE SET NULL
            DEFERRABLE INITIALLY DEFERRED,
  CONSTRAINT conferenza_chairman_rev_fkey FOREIGN KEY (chairman_rev, data, argomento)
  	REFERENCES conferenze.membro_rev (cf, data_conferenza, argomento_conferenza) MATCH FULL
  	ON UPDATE CASCADE ON DELETE SET NULL
            DEFERRABLE INITIALLY DEFERRED,
  CONSTRAINT conferenza_general_chairman_fkey FOREIGN KEY (general_chairman)
  	REFERENCES conferenze.persona (cf) MATCH SIMPLE
  	ON UPDATE CASCADE ON DELETE SET NULL
            DEFERRABLE INITIALLY DEFERRED
);
```

La tabella necessita di diversi constraint aggiuntivi, sono mostrati quelli aggiunti per controllare la correttezza della conferenza in base al suo stato.

```sql
ALTER TABLE conferenze.conferenza
ADD CONSTRAINT controlla_indetta
CHECK (
  	termine_conferenza is not NULL
	OR stato != 'INDETTA'
);

ALTER TABLE conferenze.conferenza
ADD CONSTRAINT controlla_fissata
CHECK (
	(
    	data is not null
    	AND luogo is not null
    	AND ( min_iscritti <= conteggio_inviti(data, argomento))
    	AND (conteggio_inviti(data, argomento) <= max_iscritti )
    	AND (numero_articoli > 0)
	) OR stato != 'FISSATA'
);

ALTER TABLE conferenze.conferenza
ADD CONSTRAINT controlla_passata
CHECK (
	(
		conteggio_presenze(data, argomento) > 0
		AND conclusioni is not null
		AND luogo is not null
		AND data is not null
		AND numero_articoli > 0
	) OR stato != 'PASSATA'
);
```

Come si evince dal codice sopra riportato, il constraint "controlla_fissata" necessita della funzione "conteggio_inviti" che viene riportata qui sotto.   
Sono di seguito riportati, inoltre, i trigger utilizzati per mantenere l'attributo ridondante "numero_articoli", anche esso utilizzato nello stesso constraint.

```sql
CREATE OR REPLACE FUNCTION conteggio_inviti( data date, arg character varying(50) )
RETURNS smallint
LANGUAGE plpgsql AS
$$
	declare
		n smallint;
	begin
		select count(*) into n from conferenze.invito i
		where i.data_conferenza = data AND i.argomento_conferenza = arg;
		return n;
	end;
$$;
```

```sql
CREATE CONSTRAINT TRIGGER aggiunta_articolo
AFTER INSERT
ON conferenze.approvazione_articolo
DEFERRABLE
FOR EACH ROW
EXECUTE PROCEDURE aggiunto_articolo();

CREATE OR REPLACE FUNCTION aggiunto_articolo()
RETURNS trigger
LANGUAGE plpgsql AS
$$
	begin
		UPDATE conferenze.conferenza
		SET  numero_articoli =  numero_articoli + 1
		WHERE conferenza.data = new.data_conferenza
    AND conferenza.argomento = new.argomento_conferenza;

    return new;
	end;
$$;
```

# Implementazione

Sono stati generati tramite R dei dati casuali e semi-plausibili per popolare il database, sono riportati alcuni esempi notabili. Il codice mostrato in questa sezione non è quello originariamente usato (in quanto è andato perduto), ma è una ricostruzione apposita per dimostrare i concetti esplicati nella relazione.

### Tabella persona

Una delle prime e più semplici tabelle riempite è stata "persona". Nome e cognome sono estratti casualmente dal [file provveduto dal prof. Della Monica](https://users.dimi.uniud.it/~dario.dellamonica/teaching/19_20_1sem_BDlab/19_20_1sem_BDlab.php) mentre le specializzazioni vengono [dal documento sulle conferenze di Parigi nel 2020](https://networking.ifip.org/images/IFIP_Networking_2020-Booklet.pdf) a partire da pagina 17. Infine, le professioni sono tratte dall'articolo ["IT Jobs: Career Options, Job Titles, and Descriptions"](https://www.thebalancecareers.com/list-of-information-technology-it-job-titles-2061498) di Alison Doyle, scritto per [The Balance Careers](https://www.thebalancecareers.com/).

Per la data di nascita sono stati utilizzati interi casuali, rappresentativi di _time stamp_ unix, poi convertiti in date.

```r
Random_dates <- as.Date(as.POSIXct(sample(0:670204800, size=15000, replace=T), origin="1970-01-01"))
```

Per il codice fiscale si è usufruito di una libreria chiamata [ifc tools](https://cran.r-project.org/package=ifctools) come supporto, la quale genera un codice fiscale plausibile a partire da nome, cognome, sesso, data di nascita e codice catastale.

### Tabella conferenza

Per via della loro distinzione in tre stati con diverse caratteristiche, popolare la tabella "conferenza" è stato più complesso.

Cercando di simulare il ciclo di vita di una conferenza nel database, le conferenze sono inizialmente definite indette e in seguito convertite in fissate e passate in base alle date.

Tutte le conferenze, indipendentemente dallo stato, necessitano di un general chairman a loro assegnato. Per sceglierlo è stato semplicemente preso il primo invitato a ciascuna conferenza.

```r
Conf_join_inviti <- merge(Conferenze, Inviti, by.x = c("argomento", "data"), by.y = ("argomento_conferenza", "data_conferenza"))
Conf_join_inviti_unique <- Conf_join_inviti[!duplicated(Conf_join_inviti[c("argomento", "data")]),]
Conferenze$general_chairman <- Conf_join_inviti_unique$CF
```

In base alle date delle conferenze è stato quindi stabilito il loro stato per poi modificarle di conseguenza, aggiungendo il luogo alle conferenze fissate e luogo, almeno un articolo e conclusioni a quelle passate.

```r
Indette <- Conferenze$data_scadenza > Sys.Date()
Passate <- Conferenze$data < Sys.Date()
Fissate <- !Indette & !Passate

Conferenze$luogo[Passate | Fissate] <- sample(Luoghi, size=550, replace=T)
```

# Analisi database

Per l'analisi dati sono stati utilizzati 4 grafici diversi: boxplot, histogram, barplot e heatmap.

Prima di poter realizzare i grafici è necessario però connettersi al database:

```r
library("RPostgreSQL")
drv <- dbDriver("PostgreSQL")
con <- dbConnect(drv, dbname="conferenze",
                 host="127.0.0.1", port=5432, user="postgres", password="postgres")
```

## Boxplot

Il boxplot rappresenta la quantità di articoli per ogni conferenza già passata:

```r
numero_articoli_passate <- dbGetQuery(con, "SELECT numero_articoli
                                            FROM conferenze.conferenza
                                            WHERE stato='PASSATA'")
```

```r
grafico_articoli <- boxplot(
                            numero_articoli_passate$numero_articoli,
                            horizontal = T,
                            xlab = "Frequency",
                            col = "yellow"
                            )
```

![Boxplot](Risorse/boxplot.png)

## Histogram

L'istogramma raffigura gli anni di nascita di tutti i Network Administrator presenti nel database:

```r
network_administrator <- dbGetQuery(con, "SELECT data_nascita
                                          FROM conferenze.persona
                                          WHERE professione='Network Administrator'")
```

Una volta eseguito il query si scartano giorno e mese dalle date per una visualizzazione delle didascalie più pulita

```r
a <- format(as.Date(network_administrator$data_nascita,format="%d/%m/%Y"), "%Y")
a <- as.numeric(a)
hist(
    a,
    breaks = 20,
    col = rgb(0.2,0.8,0.5,0.5),
    border = F,
    freq = T,
    xlim = c(1970,1991),
    las=1,
    xlab = "Birth Dates",
    main = ""
    )
```

![Histogram](Risorse/hist.png)

## Barplot

Il barplot è invece utilizzato per mostrare la quantità di professionisti in ciascuna disciplina all'interno di uno specifico Working Group (in questo caso WG-1.1):

```r
job_in_wg <- dbGetQuery(con, "SELECT persona.professione
                              FROM conferenze.persona LEFT JOIN conferenze.membro_wg
                                                      ON persona.cf = membro_wg.cf
                              WHERE membro_wg.numero_wg = 1 AND membro_wg.comitato_tecnico = 1")
```

```r
par(mar=c(2,9,0,1))
barplot(
        table(job_in_wg$professione),
        horiz = T,
        las=1,
        cex.names = 0.6,
        col = "#87CEFA"
        )
```

![Barplot](Risorse/barplot.png)

## Heatmap

L'heatmap qui rappresenta le distribuzioni di professioni e specializzazioni all'interno di uno specifico Working Group (in questo caso WG-2.5):

```r
prof_spec <- dbGetQuery(con, "SELECT persona.professione, persona.specializzazione
                              FROM conferenze.persona LEFT JOIN conferenze.membro_wg
                                                      ON persona.cf = membro_wg.cf
                              WHERE membro_wg.numero_wg = 5 AND membro_wg.comitato_tecnico = 2")

```

Una volta eseguito il query il risultato viene utilizzato per riempire una matrice le cui dimensioni sono rispettivamente professione e specializzazione e dove il valore di ciascuna cella rappresenta quante persone abbiano la corrispondente combinazione di professione e specializzazione:

```r
unique_prof <- unique(prof_spec$professione)
unique_spec <- unique(prof_spec$specializzazione)
m <- matrix(0, nrow = length(unique_prof) ,length(unique_spec))
colnames(m) <- unique_spec
rownames(m) <- unique_prof
for (i in 1:length(prof_spec$professione)) {
  m[prof_spec$professione[i],prof_spec$specializzazione[i]] = m[prof_spec$professione[i],prof_spec$specializzazione[i]]+1
}
```

Infine la matrice generata viene utilizzata per realizzare la heatmap:

```r
heatmap(
        m,
        Rowv = NA,
        Colv = NA,
        col= rev(heat.colors(3)),
        cexCol = 0.8,
        cexRow = 0.7,
        margins = c(10,3)
        )

legend(
      x=0,
      y=1,
      legend = c(0, 1, 2),
      fill = colorRampPalette(rev(heat.colors(3)))(3)
      )
```

![Heatmap](Risorse/heatmap.png)
