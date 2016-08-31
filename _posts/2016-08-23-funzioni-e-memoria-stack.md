---
title: Funzioni e memoria STACK
date: 2016-08-23 14:00:00
layout: post
author: 
summary: Analisi del comportamento tra due funzioni e la memoria STACK. Ovvero come una funzione andrà ad influenzare il comportamento di una seconda funzione.
---
Analizziamo il codice sotto e arriveremo a capire come una funzione, che non rispetta alcuni accorgimenti, generi un output inaspettato.  

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

**Cosa succede.** Generalemente, quando una funzione viene invocata, la memoria del calcolatore viene riservata per intero alla funzione. La memoria di cui si parla è lo [STACK]. Più precisamente, alla funzione, si riservano dei blocchi di memoria meglio conosciuti come "*slot di memoria*". Ogni slot avrà un proprio *indirizzo di memoria* e una dimesione specifica. In ogni slot verrà inserito un dato. Il dato sarà datermianto in base a come viene dichiarato il *tipo di dato* (es: intero, char, float o anche puntatore ecc...).

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

Quando in ```main()``` la funzione è invocata, lo stack è reso interamente a disposizione della funzione chiamante. Con ```int x;``` si dichiara la variabile ```x``` come intero e nello stack succede che alla variabile ```x``` viene assegnato un indirizzo di memoria in modo da poter "localizzare" la variabile durante l'uso del programma (una sorta di indirizzo civico di un'abitazione). Con ```x = xval;```  si inizializza la variabile ```x``` con il valore del "parametro formale" della funzione; in pratica si "riempie" il primo slot di memoria libero con un dato. Nel nostro caso l'inizializzazione della variabile fa si che nel primo slot di memoria disponibile sullo STACK viene inserito il valore del parametro formale ```xval``` della funzione (in questo caso il valore è il numero 7 perchè nella funzione principale ```main()``` si invoca ```foo1(7)```). Successivamente si esegue la stampa dell'indirizzo e poi del valore contenuto nella variabile ```x```.

Terminata la funzione la memoria dello STACK viene *deallocata* (cioè resa nuovamente disponibile per altri usi; è un comportamento di default dello stack quando si usano le funzione). 

Il valore 7 però non viene cancellato dallo slot di memoria, ma continua ad esistere fin quando non verrà sovrascritto (nota che lo slot di memoria continua ad avere SEMPRE lo stesso indirizzo "fisico" di memoria). Il dato verrà sovrascritto solo quando (ad esempio) si **inizializza** una nuova variabile di una qualsiasi altra funzione invocata. Ciò non accade quando si invoca la funzione ```foo2()``` che dichiara, **ma non inizializza**, la varibile ```y```. La *dichiarazione* assegna alla variabile ```y``` la prima locazione di memoria disponibile sullo stack.  

{% highlight c %}
void foo2(int dummy) {
    int y;
    
    //stampa l'indirizzo e il valore di y 
    printf("Indirizzo di y: %p\n", &y);
    printf("   Valore di y: %i\n", y);
}
{% endhighlight %}

La prima locazione di memoria (slot) disponibile sullo stack è la stessa che era stata usata in precedenza dalla funzione ```foo1()```, quindi a quell'indirizzo esiste ancora il dato (sovrascrivibile) inserito dalla funzione precedente. Non inizializzare la varibile ```y``` fa si che il dato presente nella locazione di memoria assegnata a ```y``` non si sovrascriva!

Quando si stampa, la funzione ```printf("   Valore di y: %i\n", y);``` stampa il valore contenuto nella variabile ```y```. Essendo che la varibile ```y``` risiede nello slot di memoria gia usato in precedenza, conterrà il dato inserito dalla funzione precedente (appunto perchè ```y``` non è stata **inizializzata**).

Questo spiega il perchè l'output della funzione ```foo2()``` generi sempre il valore 7 al posto del valore 11, come ci si aspetterebbe vedendo la funzione ```foo2(11)``` nella funzione principale ```main()```.

Se la variabile ```y``` fosse stata inizializza nel modo seguente ```y = dummy``` l'output sarebbe stato 11. 

[STACK]: https://it.wikipedia.org/wiki/Pila_(informatica)