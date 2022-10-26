# Fejlett programozás - 1. zh help
 ### Bináris operátor, bal és jobboldal beállításával
 ```cpp

    template<size_t L>
    container& operator += (const container<T,L>& c){
        for (size_t i = 0; i < c.letarolt_elem; i++){
            *this += c[i];
        }
        return *this;
    }
 ```
 ### Cool cucc, megadja a tömb méretét
 ```c
 int size = *(&arr + 1) - arr; // &arr returns a pointer 
 ```
 ## BEVEZETÉS
 ### Osztály
 ```cpp
 class osztalynev
 {
  private:
    // ide jonnek a privat valtozok
  public:
    // ide jönnek a publikus változók, illetve a függvények és a konstruktor

    // default konstruktor alapból létrejön, de mi is kényszeríthetjük:
    osztalynev() = default;

    // ha konstruktort használunk érdemes a generálttal dolgozni, mert az nem bugol
    container(const tipus &valtozonev_1, const tipus &valtozonev_2) : valtozonev_1(valtozonev_1), valtozonev_2(valtozonev_2)
    {

    }

    //operator overloading

    //prefixben
    osztalynev& operator-- (){
    return *this;
    }

    //postfixben
    osztalynev operator--(int) {
        osztalynev tmp = *this;
        operator--();
        return tmp;
    }

    //feluldefinialjuk a << kiratast
    friend ostream& operator << (ostream& os, const osztalynev& nev){
        return os;
    }

    // lehet hogy az kell, hogy egy függvény const legyen

    osztalynev& vicces_fuggveny() const{

    }
 };
 ```
 ### Struct
 #### Ugyan az mint az osztály, annyi a különbség, hogy minden alapból public láthatóságú benne /hasznos lehet a Traitsnél, mert kívülről is alapból látszik, nem kell a public bele/ 
 ```cpp
 struct StructNeve {
  // Adattagok
  // konstruktor
  // függvények
};
 ```
 ## TEMPLATE
 #### Osztályoknak adhatunk specializációt a segítségükkel, illetve dinamikusan deklarálhatunk típusokat
 ```cpp
 //template osztály
template<typename T, unsigned N>
//template<class T, unsigned N> <- classal is lehet ua.
class osztalynev
 {
    private:
       T variable[N]; //T typusú tömb ami N darabszámú
       unsigned db = 0;
    public:
       const unsigned* saved_N = db; //mentsuk le az elemet
 };
 ```

#### Osztály specializációra is használhatjuk, ekkor akár egyéni esetre is specializálhatunk, például specializáljuk le az osztályunkat int-re és 10 elemhosszra.
```cpp
template<>
class osztalynev<int, 10>
{};
``` 
## COPY- KONSTRUKTOR, ASSIGMENT OPERATOR
```cpp
    // Copy konstruktor
    NagyKaracsonyfa(const NagyKaracsonyfa& o) : diszek(o.diszek) {
        if (o.diszek != nullptr) {
            diszek = new VilagitoDisz(*o.diszek);
        }
    }

    // Assigment operator
    NagyKaracsonyfa& operator=(const NagyKaracsonyfa& o) {
        if (this == &o) {
            return *this;
        }

        diszek = o.diszek;

        delete diszek;
        diszek = nullptr;
        if (o.diszek != nullptr) {
            diszek = new VilagitoDisz(*o.diszek);
        }
        return *this;
    }
```
Dinamikusan tömbre:
```cpp
    MammothPine(const MammothPine& o) : ornaments(new IlluminatedOrnament[o.ornament_num]), ornament_num(o.ornament_num),
                                                act(o.act) {
        for (unsigned i = 0; i < act; ++i) {
            ornaments[i] = o.ornaments[i];
        }
    }

    MammothPine& operator=(const MammothPine& o) {
        if (this == &o) {
            return *this;
        }
        delete[] ornaments;
        ornaments = new IlluminatedOrnament[o.ornament_num];
        ornament_num = o.ornament_num;
        act = o.act;
        for (unsigned i = 0; i < act; ++i) {
            ornaments[i] = o.ornaments[i];
        }

        return *this;
    }
```
## EFFEKTOR, MANIPULATOR
### MANIPULÁTOR
Kvázi függvények, amivel az outputot manipuláljuk, mivel függvények, ezért bufferekkel érdemes használni őket.

```cpp
stringstream s;
streambuf* mentett_buffer;

//Kezdés
ostream& hide_digits_begin(ostream &os) {
  s.str("");
  mentett_buffer = os.rdbuf();
  os.rdbuf(s.rdbuf()); //Belerakjuk a stringstream-be
  return os;
}

//Vége
ostream& hide_digits_end(ostream &os) {
  os.rdbuf(mentett_buffer); //Visszaállítjuk a buffert
  string str = s.str();
  for (char c : str)
    os << (('0' <= c && c <='9') ? '*' : c);
  return os;
}
```
Ha nem kell kezdés befejezés `bufferhasználat `, akkor általában `statikus` változókban tároljuk az adatokat.

### EFFEKTOR
Az effektor egy osztály, aminek a konstruktorában dolgozzuk fel a kapott adatokat.
```cpp
class effektor_neve {
  string s;
public:
  template<size_t N>

  // effektor_neve(bool (&t)[N]) az a zárójelezés fontos -> (t&) !!!
  effektor_neve(bool (&t)[N]) {
    for (bool &i : t)
      s += (&i == t ? "" : ", ") + string(i ? "true" : "false" );
  }

  friend ostream& operator<<(ostream& os, const my_vector& v) {
    return os << "[" << v.s << "]";
  }
};
```
## TRAITS
```cpp
/*
    Itt fent vannak a megírt törzsei a traits osztályoknak
*/

// Készítünk egy template osztályt
template<class>
class konfiguracio_ajanlas;

// lespecializáljuk különböző esetekre <AMD / Intel>:
template<>
class konfiguracio_ajanlas<AMD> {
public:
  typedef Asus_A_123<AMD> alaplap_tipus;
  typedef HDD hattertar_tipus;
};

template<>
class konfiguracio_ajanlas<Intel> {
public:  
  typedef Gigabyte_I_789<Intel> alaplap_tipus;
  typedef SSD hattertar_tipus;
};
```

Ez után készítünk egy osztályt:
```cpp
template<class Processor, class Traits = konfiguracio_ajanlas<Processor> >
class szamitogep {
  typedef typename Traits::alaplap_tipus alaplap_tipus;
  typedef typename Traits::hattertar_tipus hattertar_tipus;
  
  Processor processor;
  alaplap_tipus alaplap;
  hattertar_tipus hattertar;
public:
  szamitogep(const Processor& processor, const hattertar_tipus& hattertar) :
    processor(processor), hattertar(hattertar)
  {
  }

  szamitogep(const string& proci_nev, unsigned hattertar_kapacitas) :
    processor(proci_nev), hattertar(hattertar_kapacitas)
  {
  }
  
  bool ervenyes_konfiguracio() const {
    return alaplap.kompatibilis;
  }
  
  friend ostream& operator<<(ostream& os, const szamitogep& sz) {
    return os << "processor: " << sz.processor.processor << " (" << sz.processor.tipus << ")" << endl
              << "alaplap: " << sz.alaplap.alaplap << endl
              << "hattertar: " << sz.hattertar.tipus << " (" << sz.hattertar.kapacitas << ")" << endl;
  }
};

```
