---
title: Funzioni e memoria STACK
date: 2016-08-23 14:00:00
layout: post
author: 
summary: Analisi del comportamento tra due funzioni e la memoria STACK. Ovvero come una funzione andrà ad influenzare il comportamento di una seconda funzione.
---
Analizziamo il codice sotto:  

{% highlight c %}
#include <stdio.h>

void foo1(int xval) {
    int x;
    x = xval;
    
    // stampa l'indirizzo e il valore di x
    printf("Indirizzo di x: %p\n", &x);
    printf("   Valore di x: %i\n\n", x);
}

void foo2(int dummy) {
    int y;
    
    //stampa l'indirizzo e il valore di y 
    printf("Indirizzo di y: %p\n", &y);
    printf("   Valore di y: %i\n", y);
}

int main(void) {   // funzione principale
    foo1(7);
    foo2(11);
    
    return 0;
}
{% endhighlight %}

Dopo aver compilato ed eseguito il programma, l'output prodotto sarà il seguente:

```
Indirizzo di x: 0x7ffc0c90bc6c
Valore di x: 7

Indirizzo di y: 0x7ffc0c90bc6c
Valore di y: 7
```
(l' ```indirizzo di x``` e l'```indirizzo di y``` cambieranno ad ogni esecuzione).

**Cosa succede.** Generalemente, quando una funzione viene invocata, la memoria del calcolatore viene riservata per intero alla funzione. La memoria di cui si parla è lo [STACK]. Più precisamente, alla funzione, si riservano dei blocchi di memoria con dimensione prefissate meglio conosciuti come "*slot di memoria*". In ogni slot verrà inserito un dato. Il dato sarà un numero che rappresenta un quantitativo o un indirizzo, in base a come il codice è programmato.

Guardiamo il codice della funzione ```foo1()``` per capirne il funzionamento: 

{% highlight c %}
void foo1(int xval) {
    int x;
    x = xval;
    
    // stampa l'indirizzo e il valore di x
    printf("Indirizzo di x: %p\n", &x);
    printf("   Valore di x: %i\n\n", x);
}
{% endhighlight %}

Con ```int x;``` si dichiara la variabile ```x``` come intero (qui lo STACK non è ancora usato). Con ```x = xval;```  si inizializza la variabile ```x``` con il valore del "parametro formale" della funzione. **Qui si usa lo STACK**, infatti l'inizializzazione della variabile fa si che nel primo slot di memoria disponibile sullo STACK viene inserito il valore del parametro formale ```xval``` della funzione (in questo caso il valore è il numero 7 perchè nella funzione principale ```main()``` si invoca ```foo1(7)```). Successivamente si esegue la stampa dell'indirizzo e poi del valore contenuto nella variabile ```x```.

Terminata la funzione la memoria dello STACK viene deallocata (liberata; è un comportamento di default delle funzioni), quindi resa disponibile per un'uso successivo. 

Il valore 7 però non viene cancellato dallo slot di memoria, ma continua ad esistere fin quando non verrà sovrascritto. Il dato verrà sovrascritto solo quando (ad esempio) si inizializza una nuova variabile di una qualsiasi altra funzione invocata. Ciò non accade quando si invoca la funzione ```foo2()``` che dichiara, **ma non inizializza**, la varibile ```y```. 

{% highlight c %}
void foo2(int dummy) {
    int y;
    
    //stampa l'indirizzo e il valore di y 
    printf("Indirizzo di y: %p\n", &y);
    printf("   Valore di y: %i\n", y);
}
{% endhighlight %}

Questo spiega il perchè l'output della funzione ```foo2()``` generi sempre il valore 7 al posto del valore 11, come ci si aspetterebbe vedendo la funzione ```foo2(11)``` nella funzione principale ```main```.

Se la variabile ```y``` fosse stata inizializza nel modo seguente ```y = dummy``` l'output sarebbe stato 11. 

[STACK]: https://it.wikipedia.org/wiki/Pila_(informatica)