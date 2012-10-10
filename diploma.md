## Predstavitev tehničnih materialov

### Pretvornik pdf v html (že opisan)
Podatki v obliki pdf (Adobe Acrobat) so le elektronski približek tiska in za to za strojno (računalniško) obdelavo
povsem neprimerni. Dokumente smo zato iz oblike pdf pretvarjali v obliko xml, ki je strukturirana in primerna za
nadaljno obdelavo. Orodje za pretvorbo je plod lastnega razvoja. Pri nekaterih dokumentih z oblikovno zahtevnejšo
vsebino (table, formule) je bila potrebna tudi ročna asistenca.
dokumenta, ki

### XML-editor (že opisan)
XML editor je bilo glavno orodje pri pomenskem označevanju dokumentov. Uporabili smo brezplačno verzijo programa XMLMind.


### Dokumentna baza MongoDB
Relacijsko bazo (My SQL), ki smo jo uporabljali v zgodnejših fazah razvoja, smo nadomestili z dokumentno bazo MongoDB, ki je bolj
ustrezala naravi problema. Podatkli o zdravilu so namreč strukturirani kot večnivojsko podatkovno drevo in je zato prisilna
pretvroba v relacijsko shemo nepotrebna, saj vnaša dodatno kompleksnost in zmanjša hitrost delovanja. 

Podatki v dokumenti bazi so zapisani v binarni varianti zapisaj JSON (Javascript Object Notation), ki se naravno preslika v podatkovna
tipa Object (javascript) in dict (python). 


### Programska jezika Phython

Strežniški del aplikacije in pomožna orodja so napisana v jeziku Python. Python spada med t.i. skriptne jezike, ki se lahko izvajajo z interpetiranjem
ali pretvorbo v psevdokodo, ki teče na navideznem izvajalniku (Python Virtual Machine). Jezik je zelo ekspresive in podpira tudi funkcionalni način
programiranja, kar močno zmanjša kompleksnost in količino kode. Jezik se monžično uporablja, med drugim tudi v podjetju Google, od katerega smo
uporabili tudi druga razvojna orodja.

### Django framework

Django je t.i. spletni okvir (Web Framework), ki je zbirka bolj povezanih knjižnic za obdelavo poizvedb HTTP. Ustrezal nam je zaradi njegovih
ohlapno sklopljenih komponent, ki jih je mogoče nadomestit z drugimi. Uporabili smo verzijo, ki zna upravljati z dolkumento bazo. 
Poleg sistema za serviranje strani Smpc, kart zdravil in strani PUZ, je v okviru Django napisan tudi administracijski del Draagla.

### nginx (http strežnik, usmerjevalnik in predpomnilnik)

Nginx uporabljamo v vlogi spletenga strežnika v ospredju, ki zahtevke delegira ustreznim podprocesom. Tako zahtevke, ki se začno z `/search/`, delegira
iskalniku Solr/Lucene, tiste z `/drug/` ali `/basket/` pa okviru Django. Ponavljajoči se zahtevki se postrežejo iz predpomlninika, kar močno zmanjša
obremenitev celega sistema.


### Google Closure Tools (google closure compiler, google closure library, google closure templates, google stylesheets)

Klientski del aplikacije Draagle je napisan v jeziku Javascript, in teče v brskalniku. Produkt je obsežen in kompleksen in zato smo
za razvojno orodje izbrali zbirko Googlovih spletnih orodji. Najpomembnejši člen je gotovo Google Closure Compiler, ki sier dinamičnemu jeziku
Javascript s pomočjo dogovorjenih komentarjev doda tako imenovane statične tipe. Na ta način dobi prevajalnik več informacij o naravi posameznih
spremenljiv v kodi, kar prinese dve pomembni prednosti. Prevajalnik lahko sam poišče mnoge napaki, ki bi seicer ostale skrite, kodo pa lahko drastično
zmanjša in pohitri.

Google Closure Templates je prevajalnik, ki vzorce strani HTML, ki se generirajo iz podatkov, ki pridejo s strežnika, spremeni v kodo v jeziku Javascript,
ki jo naot Google Closure Compiler integrira v prevedeno aplikacijo.

Google Closure Library je orgomna zbirka funkcij in drugih gradnikov v jeziku Javascript, ki so napisani z upoštevanjem razširjene notacije, ki jo uporablja Google
Closure Compiler. Knjižnico Google aktivno uporablja za svoje produkte kot so Gmail, Google Maps, Google Docs, zato je funkcijonalno bogata in dobro preizkušena.

Google Stylesheets je prvajalnik za jezik CSS. CSS je deklarativni jezik za opis oblike na strani HTML. Google Stylesheets doda v jezik CSS mnoge razširitve in omogoča
modularno programiranje.
    

### Iskalnika Solr/Lucene

Za iskanje po bazi zdravil s prostimi besedami uporabljamo iskalnik Lucene in spletni ovoj Solr. Lucene omogoča izkanje z vnosom daljšega sklopa besed. Pri tem
sam razpona oblike besede (različne sklone in števila) in sinonimne izraze. Največji izziv je predstavljala prilagoditev Lucene-a za slovenski jezik.



## Predstavitev tehničnih metod

### Prtvorba oblike iz pdf v xml
Kot sem že omenil, smo dokumente iz oblike pdf pretvorili v obliko xml, katere shemo smo določili sami in izhaja iz poenostavljene sheme za strani xhtml,
dodali pa smo neoblikovne oznake in atribute za zajem klkiničnih in administrativnih podatkov o zdravilu. Po pretvorbi, se s pomočjo orodja XMLmind v
SMPC dodajo oznake, ki povzamejo nestrukturirano informacijo o lastnostih zdravila in predstavljajo osnovo za nadaljno obdelavo.

### Uvoz označenih xml-jev v dokumentno bazo podatkov in v indeks iskalnika Lucene
SMPC dokumenti v obliki XML z označenimi lastnostmi zdravila se prenesejo v sistem. MEdp roceom vnosa se dopolni baza lastnosti (faktov) o zdravilih,
hkrati pa se navedki zajamejo tudi v indeksu iskalnika Lucene. Na ta način je možno tako strukturirano kot prostobesedno iskanje po bazi zdravil.

### Ekstrakt navedkov s snovmi in medicinskimi stanji
Ob vnosu označeni dokumentov XML se oddvojijo vsi navedki z medicinskimi stanji, ki je jih nato opremi s skupinamiu navedkov (tu napiši v katerem poglavju so opisane grupe).
Dodajanje skupin je ročen proces, ki se ga opravi v navadnem tekstovnem urejevelniku. Delo se olajša z avtomatski upoštevanjem sinonimov in različih besednih oblik. 
Tako dopolnjen seznam, se ponvno uvozi v bazo.

### Uvoz urejenih snovi, medicinskih stanj in sinonimov
Kakor navedki medicinskih stanje, se tudi podatki o učinkovinha oddvojijo iz baze vseh zdravil. V nadaljnem procesu se te učinkovine razporedi v skupine učinkovin,
zabeleži se sinonimne navedke in oblike, ki so po strukturi različne, po delovanju pa enake. Tako urejena zbirka se vnese nazaj v bazo in omogči izračun medsebojnega
delovanja med zdravili.

### Uvoz administrativnih podatkov iz xml CBZ
Administrativni podatki o zdravilu (režim izdaje, pakiranja, lista, režim predpisovanja itd) se avtomatično prenesejo s strežnika CBZ. Podatki se iz oblike, ki jo 
predpisuje CBZ preslikajo v obliko, ki jo uporablja Draage in prilepijo obstoječim kliničnim podatkom.

### Uvoz drugih virov podatkov
Klinične in administrativne podatke dopolnimo še s podatki o priporočilih jemanja za strejše (Priscus List).

### Združevanje uvoženih virov v enoten dokument
Vsi podatki o zdravilu se na zahtevo združijo v enoten večnivojski dokument, ki ga strežnik servira v obliki HTML ali JSON, pač glede na želeno obliko, ki jih
klient navede v glavi poizvedbe HTTP. Prednost ima oblika JSON, ki je bolj kompakta, saj ne vsebuje oblike. Dokumenti so označeni tudi z navodili za hrambo
v predpomnilnikih. To precej pohitri dostop do podatkov, saj se pogosto prenesejo iz predpomnilnika, ki se lahko nahaja kar na klientskem računalniku,
na posebnem vmesnem spletnem strežniku (`caching proxy`) ali v pa v `nginx`, ki za hitr dostop do ob ponavljajočih se poizvedbah uporablja komponento `memcahced`.
