# Human-Disease-Diagnosis-

##Indice
1. [Introduzione](#introduzione)
2. [Modello di dominio](#modello-di-dominio)
3. [Requisiti specifici](#requisiti-specifici)
4. [Stile architetturale](#stile-architetturale)
  -[Api utilizzate](#api-utilizzate)
  -[Acquisizione dei dati](#acquisizione-dei-dati)
  -[Manipiolazione dei dati](#manipolazione-dei-dati)
  -[Modello probabilistico](#modello-probabilistico)
  -[How question](#how-question)
5. [Manuale utente](#manuale-utente)



## Introduzione
Human Disease Diagnosis è un’applicazione di diagnosi di malattie con interfaccia grafica (GUI), il modello presentato è un grafo orientato dove
•	I nodi sono malattie e sintomi
•	Gli archi partono dalla malattia per convergere sui sintomi
Viene utilizzata l’ontologia “DOID: Disease Ontology” in sola lettura. È un’ontologia standardizzata per le malattie umane allo scopo di fornire alla comunità biomedica descrizioni coerenti, riutilizzabili e sostenibili dei termini delle malattie umane e dei relativi concetti di quest’ultime, del vocabolario medico, che sono appunto collegate ai sintomi.


## Modello di dominio
Per creare la base di conoscenza (KB) le classi prese in considerazione sono state due: Disease (malattie) e Symptom (sintomi), prese attraverso la proprietà “has_symptom”.
•	La classe malattia (disease) è suddivisa in sottoclassi, fino alla foglia, che rappresenta la vera malattia, Figura (1)

PNG
Figura 1: esempio di gerarchia delle malattie attraverso la proprietà “has_sublcass”

•	La classe sintomi (sympton) è suddivisa in sottoclassi, fino alla foglia, che rappresenta il sintomo reale, Figura (2)

PNG
Figura 2: esempio di gerarchia dei sintomi attraverso la proprietà “has_sublcass”

•	L’arco che collegate le malattie e i sintomi attraverso la proprietà “has_symptom”, Figura (3)

PNG
Figura 3: esempio di collegamento tramite la proprietà “has_symptom” tra la malattia “febbre gialla” e il sintomo “itterizio” ovvero colorazione gialla della pelle


##Requisiti specifici
Il sistema software soddisfa i seguenti requisiti:
•	visualizza la malattia in base ai sintomi inseriti in input da un’utente
•	visualizza le malattie più probabili in base ai sintomi inseriti in input dall’utente
•	Spiega come è stata trovata la malattia


##Stile architetturale

###Api utilizzate
Al fine di gestire la conoscenza come una KB basata su clausole si è fatto uso delle classi proposte da David L. Poole e Alan K. Mackworth (www.aipython.org), le classi in questione sono logicBottomUp.py e logicProblem.py

Per l’utilizzo dell’ontologia inferred_doid è stata utilizzata la libreria standard owlready2 di python, la quale fornisce le primitive per il caricamento, la manipolazione e l’interrogazione di una qualunque ontologia

Per problemi legati all’uso del reasoner, non è stato possibile utilizzare la versione originale, infatti per risolvere il problema è stato utilizzato Protege. Protege è un’applicazione  per la modellazione di ontologie, è stata utilizzata per lanciare il reasoner ed esportare il tutto, al fine di risolvere i problemi legati all’utilizzo di quest’ultimo tramite la libreria python owlready2

###Acquisizione dei dati
Per la costruzione della base di conoscenza (oggetto di tipo logicProblem.KB), sono state scritte funzionalità per l’interrogazione della base di conoscenza, queste funzionalità sono contenute nel file ontology_manager.py, al fine di reperire le malattie con i rispettivi sintomi. 

Successivamente sono state costruite le clausole (logicProblem.Clause) e quindi la base di conoscenza.
Bisogna notare che la KB sarebbe dovuta essere completata, perché una malattia è caratterizzata da determinati sintomi, ma soprattutto è necessario che tutti gli altri non si manifestino, però ciò avrebbe portato ad un sistema molto selettivo, il quale avrebbe rilevato malattie in molti meno casi, senza tollerare errori (un esempio può essere un sintomo manifestato come falso positivo). 

Per questo motivo è stato creato un modello probabilistico, per cui ad ogni malattia appartenente ad un punto fisso della base di conoscenza è stato assegnato uno score, il quale indica la probabilità che l’utente abbia contratto quella malattia

###Manipolazione dei dati
Una volta che i dati sono stati acquisiti ed è stata costruita la base di conoscenza, il sistema è pronto per essere utilizzato. Attraverso l’interfaccia grafica, l’utente sceglie quali sintomi selezionare, dunque premendo il bottone “Submit” parte il ragionamento, esso consiste nel ritrovamento del punto fisso nella base di conoscenza, dunque viene creato il modello probabilistico e viene stampato a video

###Modello probabilistico
Per ragioni già citate precedentemente, è stato creato un modello probabilistico, esso assegna ad ogni malattia appartenente ad un punto fisso, uno score tale che esso sia positivo e minore o uguale a 1, rispettando la regola che impone che la somma degli score delle malattie appartenenti al punto fisso sia uguale a 1.

La probabilità calcolata si basa sul numero dei sintomi in comune che ha la malattia presa in considerazione con i sintomi selezionati dall’utente, per cui se denotiamo con S l’insieme dei sintomi che l’utente ha selezionato e con s(m) l’insieme dei sintomi che caratterizzano la malattia m, allora la probabilità che l’utente abbia contratto la malattia m è denotata dalla legge |s(m)|/|S|.
Quindi se sono stati inseriti tutti e soli i sintomi che caratterizzano la malattia m, è vero che ciò non esclude il fatto che vengano estratte anche altre malattie, ma sicuramente la probabilità di aver contratto la malattia m sarà maggiore o al massimo uguale alla probabilità di aver contratto una qualsiasi altra malattia estratta

###How question
Sono state implementate le funzionalità per spiegare i risultati ottenuti, in particolare sono state realizzate le funzioni che permettono all’utente di sapere quali siano state le motivazioni del sistema per cui una determinata malattia sia stata rilevata come vera e sia stata comunicata in output all’utente. La metodologia adottata è stata quella delle how questions trattate nel ragionamento con vincoli, per cui il sistema restituisce la clausola utilizzata per dimostrare la malattia richiesta dall’utente


##Manuale utente
L’applicazione può essere eseguita da riga di comando posizionandosi nella directory scr e digitando “python user_interface.py” se ciò non dovesse funzionare, si può eseguire il tutto da riga di comando tramite la Shell Anaconda o in alternativa da un idee qualsiasi tipo Spyder. 
L’applicazione richiede l’utilizzo attraverso un’interfaccia utente (GUI), all’apertura dell’applicazione viene mostrata una finestra con le seguenti funzionalità:
•	Cliccando su “Select Symptom” viene mostrata una finestra con una lista di sintomi tra cui scegliere
•	Attraverso la console “Console Area” l’utente può visualizzare i sintomi da lui inseriti e successivamente le malattie che potrebbe avere
•	Cliccando su “Submit” i sintomi scelti dall’utente verranno utilizzati per dedurre le potenziali malattie mostrate a console
•	Cliccando su “Cancel” verrà cancellato il contenuto della console
•	La parte in basso dell’interfaccia contiene un’are di testo per inserire i sintomi o le malattie per cui si vuole sapere come sono state dimostrate










