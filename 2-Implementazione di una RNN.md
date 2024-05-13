# Implementazione di una RNN

Colab è uno strumento che permette di scrivere dei markdown con snippet di codice eseguibile in Python, quest’ultimo è tra i linguaggi di programmazione più diffusi nel settore nel machine learning. 

Uno tra i framework più famosi è Keras, costruito sulla base di un ulteriore framework chiamato TensorFlow, quella che alleghiamo qui è l’implementazione di un modello RNN simile a quello che abbiamo trattato in via teorica nella precedente newsletter.

Consigliamo di scorrere questa pagina e il notebook colab contemporaneamente, dato che qui ci proponiamo di spiegare ulteriormente gli snippet di codice (che è possibile eseguire direttamente dal runtime di colab)

Detto ciò, questo è il link del notebook in oggetto:

[<img src="assets/colab.png">](https://colab.research.google.com/github/r-giambra/gen-ai/blob/main/Colab%20Notebooks/Newsletter%20GENAI%20-%20text_generation.ipynb)


Dopo una prima fase in cui vengono installate le librerie, inizia quello che va sotto il nome di ETL (Extraction, Transformation, Loading) dei dati.

Per capire come organizzare il dataset è necessario comprendere l’algoritmo di Learning da applicare. Nello specifico, si vuole allenare la cella RNN character-wise, quindi a partire da un carattere (e lo stato globale acquisito fino ad ora) si vuole generare il successivo.

Per allenare l’algoritmo sarà necessario avere dei campioni che ad ogni carattere associano il successivo e allo stesso tempo bisogna gestire uno stato che si evolve di carattere in carattere. Per ottenere tale risultato, a partire da un testo molto lungo, si estrarranno dei token lunghi seq_len caratteri che fanno da input e dei token di pari lunghezza ma shiftati di un carattere che faranno da output. Oltre tutto ciò, bisogna sempre considerare che naturalmente la rete necessita di dati numerici per operare, per cui sarà necessario convertire i caratteri in numeri e viceversa.

Nello specifico, la fase di ETL è distribuita così nei blocchi:

- [3][4][5] import dei dati (un campione di sheakspare di 1115394 caratteri)
- [6] costruzione di un vocabolario di caratteri distinti presenti nel testo (che sarà utilizzato per mapping caratteri, numeri)
- [7][8][9][10][11][12][13] costruzione delle funzioni di mapping ids_from_chars e chars_from_ids, e la complessiva text_from_ids 
- [14][15][16][17][18][19] creazione dei token lunghi seq_len a partire dal testo
- [20][21][22][23][24] creazione del vero e proprio dataset, creando le coppie input/target che contengono due sequenze di uguale lunghezza ma shiftate di un carattere (gestito dalla funzione split_input_target) 

Paradossalmente il grosso è passato, spesso infatti nella scrittura di algoritmi di machine learning la parte più complessa è proprio quella del reperimento dei dati, nelle successive fasi si procede con la creazione, training ed applicazione del modello.

Nello specifico, la fase di creazione del modello è distribuita così nei blocchi:

- [25][26][27] creazione della cella RNN (NB: rispetto a quanto trattato nella precedente newsletter, l’algoritmo fa uso di una versione precedente di cella di memoria rispetto alla LSTM, ossia la GRU)
- [28][29][30][31][32] test del modello non trainato (che naturalmente ritorna una predizione casuale)

- La fase di training del modello è gestita dalla [33] alla [39], in quest’ultima viene proprio effettuato il fit del modello.

- Infine dalla cella [40] alla [43] viene creata una classe per poter applicare il modello e viene successivamente applicato, generando una frase di 1000 caratteri a partire dalla stringa iniziale “ROMEO:”

Gli step successivi servono per esportare la rete allenata, quello su cui invece ci si vuole soffermare riguarda l’output generato:

Si nota come l’output abbia la stessa struttura del copione usato come training 
```
PERSONAGGIO: 
Battuta
```
E inoltre nelle frasi si nota una corretta grammatica in stile sheakespear.
Tuttavia, se si legge a fondo, si noterà che le frasi non hanno un grande senso, come suggerisce lo sviluppatore stesso, allenando l’algoritmo per più epoche si raggiungono livelli migliori
