# JavaScript - Facade Design Pattern

Fasada je strukturalni dizajn patern koji pruža interfejs koji štiti klijente od složenih funkcija u jednom ili više podsistema. To je jednostavan patern koji se može učiniti trivijalnim, ali je moćan i izuzetno koristan. Često je prisutan u sistemima koji su izgrađeni oko višeslojne arhitekture.

## Upotreba Facade

Cilj fasade je pružiti high-level interfejs (svojstva i metode) koje čini podsistem ili set alata lakim za korištenje za klijenta.

Na serveru, u višeslojnoj web aplikaciji često imate prezentacijski sloj koji je klijent servis sloju (kako bi vam bolje približio ovo je MVC arhitektura). Komunikacija između ova dva sloja odvija se preko dobro definisanih API-ja. Ovaj API, ili fasada, skriva složenost poslovne logike i njihove interakcije od prezentacijskog sloja. 

Drugi primer gde se fasade koristi je refaktorisanje. Pretpostavimo da imate zbunjujući ili neuredan skup nasleđenih objekata (poznato kao i **Legacy Code**) zbog kojih klijent ne bi trebao biti zabrinut. Ovaj kod možete sakriti iza fasade. Fasada expozuje samo ono što je neophodno i predstavlja čistiji i jednostavan kod za korištenje.

Fasade se često kombinuju s drugim dizajn paternima. Same fasade često se izvode kao singleton factories.

## Diagram

<img width="450" alt="facade_diagram" src="https://user-images.githubusercontent.com/21141150/206467227-1859aee6-464a-4944-bb70-7dc3e9f857c2.png">

## Učesnici

Objekti koji učestvuju u ovom paternu su: 

Fasada -- U primeru: Hipoteka 
1. Zna koji su podsistemi odgovorni za request.
2. Delegira request klijenata odgovarajućim objektima podsistema

Podsistem -- U primeru: Banka, Kredit, Prošlost
1. Implementira i izvodi specijalizovanu funkcionalnost podsistema 
2. Nemaju znanja o fasadi niti se odnose na nju

## Primer

U primeru **Hipoteka** objekat je fasada. Klijentu expozuje jednostavan interfejs sa samo jednom metodom: **prijava**. Ispod ovog jednostavnog API-ja leži znatna složenost.

Ime i Prezime podnosioca prijave prosljeđuje se u funkciju konstruktora hipoteke, ako ne znate sta je funkcijski konstruktor više o tome možete pročitati [ovde](https://github.com/milospantelinac/Prototype_JavaScript). Zatim se poziva metoda **prijava** sa traženim iznosom kredita. Interno, ova metoda koristi servise iz 3 odvojena podsistema koji su složeni i za njihovu obradu je potrebno neko vreme; to su banka, kredit i prošlost (ovo se odnosi na kriminalnu prošlost).

Na osnovu nekoliko kriterijuma (bankovni izvodi, kreditni izveštaj i kriminalna prošlost) podnosiocu zahteva se ili prihvata ili odbija traženi kredit.

## Primer

```
var Hipoteka = function (ime) {
    this.ime = ime;
}

Hipoteka.prototype = {

    prijava: function (iznos) {
        // pozivanje podsistema...
        var rezultat = "odobren";
        if (!new Banka().proveri(this.ime, iznos)) {
            rezultat = "odbijen";
        } else if (!new Kredit().dobiti(this.ime)) {
            rezultat = "odbijen";
        } else if (!new Proslost().proveri(this.ime)) {
            rezultat = "odbijen";
        }
        return `${this.ime} je ${rezultat} hipoteka u iznosu od ${iznos}`;
    }
}

var Banka = function () {
    this.proveri = function (ime, iznos) {
        // kompleksna logika...
        return true;
    }
}

var Kredit = function () {
    this.dobiti = function (ime) {
        // kompleksna logika...
        return true;
    }
}

var Proslost = function () {
    this.proveri = function (ime) {
        // kompleksna logika...
        return true;
    }
}

var hipoteka = new Hipoteka("Petar Petrović");
var rezultat = hipoteka.prijava("$50.000");

console.log(rezultat);
// → Petar Petrović je odobren hipoteka u iznosu od $50.000
```
