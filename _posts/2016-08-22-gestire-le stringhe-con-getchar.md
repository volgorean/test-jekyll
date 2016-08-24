---
title: Gestire le stringhe con getchar
date: 2016-08-22 12:00:00
layout: post
summary: Aquisita una stringa digitata dall'utente, verrà controllata per determina se contiene al massimo due caratteri uguali di seguito.
---

Aquisita una stringa digitata dall'utente, verrà controllata per determina se contiene al massimo due caratteri uguali di seguito. 

{% highlight c %}
#include <stdio.h>
#include <stdbool.h>

// dichiarazione prototipo funzione
bool al_massimo_due_di_seguito_(const char *s);

int main (void){
    char character = 0;
    char string[81]; // 81 è il numero max di caratteri inseribili da console
    int i = 0;
    
    printf("\n**************************************\n"
    "Verifico se in una parola ci sono \n"
    "al max 2 caratteri uguali consecutivi.\n"
    "**************************************\n\n"
    "Inserisci una parola: ");
    
    // riempimento array con caratteri 
    do{
        character = getchar(); // leggo carattere per carattere da tastiera
        string[i] = character; // ogni carattere è inserito in un array
        i++;
    }while( character != '\n' );
    
    // stampa output integrando integrando dati da una funzione
    printf("\n> Ci sono %s caratteri uguali consecutivi! \n\n", 
    al_massimo_due_di_seguito_(string) ? "da 0 a 2" : "più di 2");
    
    return 0; 
}

// funzione di conteggio caratteri uguali 
bool al_massimo_due_di_seguito_(const char *s){
    while ( *s ){
        if ( *s == *(s + 1) && *s == *(s + 2) ) 
            return false;
            
        s++;
    }

    return true;
}
{% endhighlight %}


Un esempio di output:

```
**************************************
Verifico se in una parola ci sono 
al max 2 caratteri uguali consecutivi.
**************************************

Inserisci una parola: qwertt

> Ci sono da 0 a 2 caratteri uguali consecutivi! 
```

Altro esempio di output:

```
**************************************
Verifico se in una parola ci sono 
al max 2 caratteri uguali consecutivi.
**************************************

Inserisci una parola: qweeerty

> Ci sono più di 2 caratteri uguali consecutivi!  
```

### Cosa succede.

Una volta dichiarato e inizializzata la variabile ```character``` e l'array ```string[]```, si chiede all'utente di inserire la stringa da elaborare: 

{% highlight c %}
// riempimento array con caratteri 
do{
    character = getchar(); // leggo carattere per carattere da tastiera
    string[i] = character; // ogni carattere è inserito in un array
    i++;
}while( character != '\n' );
{% endhighlight %}

La funzione ```getchar()```, quando invocata, attiva un *buffer* di lettura. Il *buffer* di lettura è una caratteristica del terminale (shell o linea di comando) che quando invocato immagazzina lo streaming dei caratteri ricevuto in input tramite la tastiera fin quando non viene premuto il tasto Enter/Invio.

Quando si invoca ```getchar()``` si va a leggere il singolo **primo** carattere del *buffer* di lettura. Dopo la lettura il carattere letto viene "eliminato dal buffer" e il carattere successivo diventa il primo (il carattere "a capo" ```\n``` identifica che ci troviamo alla fine del buffer). 

Tecnicamente l'array ```string[i] = character;``` lo troveremo riempito così (esempio): 

```
char stringa[] = {q, w, e, r, t, y, \n, \0}
```

Ora, avendo letto i dati carattere per carattere, è necessario ricomporre tali dati in un'unico posto. Con  l'uso di un ciclo e di un array riusciamo a ricomporre la stringa inserita dall'utente. Dopo tutto una stringa non è altro che un array di caratteri!

Vediamo poi cosa succede nella parte successiva del codice:

{% highlight c %}
// funzione di conteggio caratteri uguali 
bool al_massimo_due_di_seguito_(const char *s){
    while ( *s ){
        if ( *s == *(s + 1) && *s == *(s + 2) ) 
            return false;
            
        s++;
    }

    return true;
}
{% endhighlight %}

Quando viene invocata, la funzione sopra prende ***l'indirizzo*** della stringa di caratteri inserita dall'utente e la elabora con l'uso dei puntatori. Inizialmente il puntatore punta all'indirizzo del primo carattere della stringa. Fin quando il *contenuto* a cui punta il puntatore  ```*s ``` è diverso dal carattere nullo  ```\0``` (il carattere nullo equivale a 0), il ciclo ```while``` incrementa la posizione di memoria a cui punta ```*s``` passando quindi al carattere successivo (ogni carattere si trova in una cella di memoria con un proprio indirizzo fisico).  

La parte di codice con l' ```if``` fa un confronto dei contenuti presenti nei relativi indirizzi di memoria a cui punta ```*s``` e ne "ritorna" TRUE o FALSE a seconda dei casi.

### Bibliografia o altri riferimenti

* [What is the standard input buffer?](http://stackoverflow.com/questions/14068346/what-is-the-standard-input-buffer)
* [Input da tastiera](http://www.repni.it/view/guida_c_base_input_da_tastiera.html)
* [Standard input (stdin)](https://it.wikipedia.org/wiki/Canali_standard#Standard_input_.28stdin.29)
