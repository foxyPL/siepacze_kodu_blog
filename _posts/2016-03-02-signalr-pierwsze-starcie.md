---
id: 37
title: 'SignalR &#8211; pierwsze starcie'
date: 2016-03-02T22:41:36+00:00
author: Łukasz Zaborski
layout: post
guid: http://www.siepaczekodu.pl/?p=37
permalink: /2016/03/02/signalr-pierwsze-starcie/
image: /wp-content/uploads/2016/03/SignalR-Nuget2-e1458759694461.png
categories:
  - 'C#'
  - SignalR
---
SignalR jest darmowym frameworkiem dla platformy ASP.NET ułatwiającą implementacje aplikacji czasu rzeczywistego. Pozwala na utworzenie dwukierunkowej komunikacji między serwerem a klientem (przegladarka) bez konieczności przeładowania całej strony. SignalR umożliwia tworzenie apliacji www które w założeniu wymagają ciągłej aktualizacji danych pochodzących z serwera. Przykładem takich aplikacji mogą być gry czasu rzeczywistego lub komunikatory internetowe.

SignalR dostarcza gotowe API umożliwający utworzenie między serwerem a klientem zdalnych wywołań procedur (RPC).
  
Pozwala to na wywołanie funkcji JavaScript w przeglądarce klienta od strony serwera napisanego w platformie .NET, jak i metod C# wywoływanych w kodzie JavaScript.

Do frameworka SignalR zostały wbudowane następujące techniki transportowe

  * WebSocket
  * Server Side Events
  * Forever Frame
  * Ajax long polling

API SignalR dostarcza abstrakcyjną warstwe transportu. Oznacza to że programista nie musi martwić się o wsparcie technologiczne zapewniane przez przeglądarki lub systemy operacyjne na których działa serwer. SignalR sam dobiera najodpowiedniejszy typ transportu jaki jest aktualnie dostępny.

#### SignalR &#8211; klasa Hub

Klasa <a href="https://msdn.microsoft.com/query/dev14.query?appId=Dev14IDEF1&l=EN-US&k=k(Microsoft.AspNet.SignalR.Hub);k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5.1);k(DevLang-csharp)&rd=true" target="_blank">Hub</a> umożliwia komunikacje obustronną poprzez wywoływanie metod C# oraz JavaScript. Hub służy jako miejsce przechowywania metod wykonywanych przez serwer oraz aplikacje kliencką (np. przeglądarka internetowa). Dla każdego nowego połączenie tworzona jest nowa instancja Hub-a.

&nbsp;

<a href="http://www.siepaczekodu.pl/wp-content/uploads/2016/02/SignalR.png" rel="attachment wp-att-41"><img class="aligncenter size-full wp-image-41" src="http://www.siepaczekodu.pl/wp-content/uploads/2016/02/SignalR.png" alt="SignalR" width="833" height="350" srcset="http://www.siepaczekodu.pl/wp-content/uploads/2016/02/SignalR.png 833w, http://www.siepaczekodu.pl/wp-content/uploads/2016/02/SignalR-300x126.png 300w, http://www.siepaczekodu.pl/wp-content/uploads/2016/02/SignalR-768x323.png 768w" sizes="(max-width: 833px) 100vw, 833px" /></a>

Poniżej przedstawiona jest pochodna klasy Hub.

<pre class="EnlighterJSRAW" data-enlighter-language="csharp">using Microsoft.AspNet.SignalR;

public class NotyficationHub : Hub
{
    public void Send(string message)
    {
        Clients.All.notyficationMessage(message);
    }
}</pre>

Każda metoda publiczna w klasie może być wywołana z poziomu klienta. W tym przypadku metoda Send korzysta z właściwości <a href="https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.hub.clients(v=vs.118).aspx" target="_blank">Clients</a>, która przechowuje połączenia klientów z SignalR. Jeśli nasza klasa dziedziczy po klasie Hub, typ właściwości All to dynamic.

<pre class="EnlighterJSRAW" data-enlighter-language="csharp">Clients.All.notyficationMessage(message);</pre>

Powyższa linia kodu wywołuje w każdym połączonym kliencie metode notyficationMessage zadeklarowaną w obiekcie proxy utworzonym przez $.connection

A teraz sposób wykorzystania Hub-a po stronie klienta

<pre class="EnlighterJSRAW" data-enlighter-language="js">&lt;script src="~/Scripts/jquery.signalR-2.1.2.js"&gt;&lt;/script&gt;
&lt;script src="~/signalr/hubs"&gt;&lt;/script&gt;

var notyficationHub = $.connection.notyficationHub;
notyficationHub.client.notyficationMessage = function (message) {
    console.log(message);
}

$.connection.hub.start();</pre>

Po pierwsze potrzebujemy referencji do biblioteki SignalR oraz do automatycznie wygenerowanych skryptów przez SignalR. Następnie za pomocą wczytanej biblioteki wyciagamy referencje do automatycznie wygenerowanego obiektu proxy komunikującego się z Hubem po stronie serwera. Należy wspomieć iż referencje do obiektów klas pochodnych po Hub-ie oraz metod znajdujących sie w nich zapisywane są w formacie camelCase. Mając już obiekt proxy możemy zaimplementować funkcje która będzie wywoływana przez metode Send klasy NotyficationHub opisaną powyżej. Ostatnia linijka inicjalizuje połączenie.

Następnie możemy wywołać poniższą linijke po stronie klienta w celu rozesłania wszystkim połączonym klientom wiadomości:

<pre class="EnlighterJSRAW" data-enlighter-language="js">notyficationHub.server.send("Hellooo from the othjer sideeee...");</pre>

&nbsp;

#### Małe podsumowanie

  * SignalR wspomaga implementacje aplikacji wymagających dynamicznego odświeżania ekranu dla wielu klientów.
  * Aplikacje napisane w SignalR są skalowalne. Rozwiązanie przygotowane dzięki platformie można zastosować nie tylko w aplikacjach działających na przeglądarkach internetowych ale również w aplikacjach WPF oraz aplikacjach działających pod systemem Windows Phone.
  * Dzięki SignalR, programista korzysta z jednego API, dzięki czemu nie musi troszczyć się co dokładnie przeglądarka wspiera, ponieważ framework dobiera najlepszą technologie jaka jest aktualnie dostępna.
  * Framework jest wspierany oraz rozwijany na bieżąco. (Niestety nie jest jeszcze dostępny w nowym ASP.NET Core 1)

W kolejnym wpisie dotyczącym SignalR postaram sie przedstawić nieco większy przykład zastosowania frameworka, który znajduje się w mojej pracy magisterskiej 🙂

<div class='sfsi_Sicons' style='width: 100%; display: inline-block; vertical-align: middle; text-align:left'>
  <div style='margin:0px 8px 0px 0px; line-height: 24px'>
    <span>Udostępnij</span>
  </div>
  
  <div class='sfsi_socialwpr'>
    <div class='sf_fb' style='text-align:left;width:98px'>
      <div class="fb-like" href="http://www.siepaczekodu.pl/2016/03/02/signalr-pierwsze-starcie/" width="180" send="false" showfaces="false"  action="like" data-share="true"data-layout="button" >
      </div>
    </div>
    
    <div class='sf_twiter' style='text-align:left;float:left;width:auto'>
      <a href="http://twitter.com/share" data-count="none" class="sr-twitter-button twitter-share-button" lang="en" data-url="http://www.siepaczekodu.pl/2016/03/02/signalr-pierwsze-starcie/" data-text="SignalR &#8211; pierwsze starcie" ></a>
    </div>
    
    <div class='sf_google' style='text-align:left;float:left;max-width:62px;min-width:35px;'>
      <div class="g-plusone" data-href="http://www.siepaczekodu.pl/2016/03/02/signalr-pierwsze-starcie/" data-size="large" data-annotation="none" >
      </div>
    </div>
  </div>
</div>