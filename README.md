# python_project

> :warning: V tomto repozitáři naleznete vypracovaný python projekt. Projekt obsahuje mapy, proto doporučuji se na výsledný notebook podívat pomocí následujícího odkazu: 
http://nbviewer.org/github/muronmic/python_project/blob/main/Python_project_Muronmic_final.ipynb

Cílem projektu bylo zpracovat informace o "bike sharing" firmy Just Eat Cycles a tím pomoct zefektivnit celý proces půjčování kol (bohužel firma skončila v polovině roku 2021). 

## Zadání: 

Proveďte standardní deskriptivní statistiku dat. Také zjistěte minimálně následující informace:

* identifikujte aktivní a neaktivní stanice
* identifikujte nejfrekventovanější stanice
* identifikujte stanice, na kterých se kola hromadí a stanice, kde potenciálně chybí
* spočítejte vzdálenosti mezi jednotlivými stanicemi
* jak dlouho trvá jedna výpůjčka? Najděte odlehlé hodnoty, zobrazte histogram

**Analýza poptávky:**
* zobrazte vývoj poptávky po půjčování kol v čase
* identifikujte příčiny výkyvů poptávky
* zjistěte vliv počasí na poptávku po kolech (údaje o počasí v Edinburghu jsou v tabulce edinburgh_weather)
* půjčují si lidé kola více o víkendu než během pracovního týdne?


## Postup: 

### Příprava prostředí
Jako první jsem si naimportovala všechny potřebné knihovny a nastavila konexi do mysql databáze, kde jsou uložené tabulky, které byly třeba pro vypracování projektu. 

Potom jsem načetla tabulku edinburgh_bikes, uložila ji do DataFrame `bikes_df` a mohla dále s touto tabulkou pracovat. Vytáhla jsem si z tohoto DF seznam všech stanic podle jejich ID a přidala k nim jejich jméno a polohu, jelikož tyto informace byly nadále potřeba, a uložila je do DF `bikes_station_names`. 

### Aktivní a neaktivní stanice

Vytvořila jsem si kopii DataFrame `bikes_df`. Jako první jsem přidala položky `date_start`, `date_end`, `month`, `weekday`, `weekend` a `year` do DF. 

Za neaktivní stanice jsem považovala ty, které se v posledním roce, tj. v roce 2021, nepoužily ani jednou. Neaktivní stanice jsem vyselektovala pomocí `query` a `merge`. Získala jsem tak seznam stanic s jejich ID, názvem a polohou, kterou jsem později použila. 

Dále jsem spočítala průměrný počet výpůjček a vrácení kol v roce 2021 pro zbylé - aktivní - stanice (první jsem spočítala sumu přes všechny dny pro každou stanici a poté pomocí `groupby` a  `apply(lambda x: np.sum(x) / days_2021_count)` jsem spočítala průměrný počet výpůjček za den v roce 2021). 

Za aktivní jsem označila stanice, které byly použity jako startovní a nebo cílová stanice v posledním roce (2021) průměrně alespoň 5x za den. Za málo aktivní stanice jsem označila ty zbývající, které byly v roce 2021 použity. 

Všechny stanice jsem potom zaznamenala do mapy, kde zeleně jsou zaznamenány aktivní stanice, oranžovou stanice používané v roce 2021, ale méně než 5x za den, a šedou barvou stanice které nebyly v roce 2021 použity vůbec. V mapě je možno filtrovat, takže si můžeme zobrazit např. pouze stanice nepoužívané v roce 2021.

### Nejfrekventovanější stanice

Opět jsem si vytvořila kopii DF `bikes_df` a přidala k němu informaci o dni, měsíci atd. Rozhodla jsem se počítat jakoukoli aktivitu na stanici, tj. půjčení i vrácení kola. 

Pomocí `grouby` a `count()` jsem spočítala celkový počet půjčení kol a počet vrácení kol na stanici. Vytvořila jsem sloupec `activity_sum`, který je součtem vrácených kol a půjčených kol na stanici. Vznikl tak DF  `activity_count_df`, který obsahuje informace o počtu půjčených, vrácených kol a navíc obsahuje informace o stanici - jméno, ID a polohu. 

Dále jsem počítala průměrný počet půjčení a vrácení kol za den pro každou stanici v pracovních dnech a o víkendu a uložila tyto informace do `means_df`. Průměr jsem počítala přes všechny pracovní dny, víkendy za celou dobu fungování služby bike sharingu, ne pouze pro dny, kdy fungovaly dané stanice. Je tedy jasné, že např. v případě stanice Portobello - Kings Road (ID 1728) by průměr vystoupal, kdyby se počítalo pouze s aktivními dny pro stanici.
Poté jsem zobrazila 5 nejfrekventovanějších stanic z `activity_count_df` s jejich počty výpůjček/vratek a pro těchto 5 stanic jsem vytáhla informace z `means_df` o průměrné aktivitě o víkendu a pracovních dnech. 

### Stanice, kde se kola hromadí a kde potenciálně chybí

Opět jsem si vytvořila kopii `bikes_df` a přidala další informace, stejně jako výše. 

Vypočítala jsem počet odchozích kol ze stanice za den, tj. počet výpůjček na stanici, a počet příchozích kol, tj. počet vratek na stanici za den. 
Poté jsem vytvořila sloupec `total_bikes`, který je rozdíem příchozích a odchozích kol. 

Vytvořila jsem DF `shortage_df` (stanice s nedostatkem - ty, které mají průměrně za den o 2 kola méně)  a `excess_df` (stanice s nadbytkem - ty, které mají průměrně za den o 2 kola více). Pozn. opět jsem pracovala pouze se stanicemi, které se v roce 2021 použily alespoň jednou. Stanice v těchto DF jsem zobrazila do mapy, zeleně jsou označené stanice s nadbytkem, červeně s nedostatkem. 


## Vzdálenosti mezi stanicemi

Tady jsem použila DataFrame `bikes_station_names` a `stations_2021_df` (to je DF, kde jsou uloženy stanice, které se použily v roce 2021). Použila jsem knihovny `geopy`, pomocí které jsem byla schopna vyčíslit vzdálenosti mezi stanicemi, a `qgrid`, díky které jsem mohla zobrazit DF se vzdálenostmi. Vyzkoušela jsem také vytvořit widgety pomocí `ipywidgets` 

> :warning: Bohužel interaktivní tabulky s použitím qgrid nebo ipywidgets nejsou v githubu nebo v nbviewer viditelné, proto jsem zobrazila alespoň statický HTML náhled.

## Délka jedné výpůjčky

Do kopie DataFrame `bikes_df` jsem přidala sloupec s údajem o délce výpůjčky v minutách (původní hodnota je v sekundách). 
Poté jsem vytvořila dva histogramy - jeden ukazuje histogram k výpůjčkám, které trvaly méně než 100 minut a ten druhý je histogram s logaritmickou transformací délky výpůjček. V případě druhého histogramu jsem nemusela nijak omezovat délku výpůjčky. 

Zobrazila jsem také DF, který ukazuje odlehlé hodnoty, kdy výpůjčky trvaly déle než jeden týden. Můžeme jenom předpokládat, že se jednalo o ztracená kola, která se po nějakém čase vrátila.


### Analýza poptávky
### Vývoj poptávky po půjčování kol v čase

Vypočítala jsem počet výpůjček za každý den, kdy byla služba v provozu a to pomocí `groupby` a `count`.
Tuto informaci jsem zaznamenala do lineárního grafu, který ukazuje jak se vyvíjelo množství půjčených kol v čase. 

Dále jsem ještě vypočítala průměrný počet výpůjček v průběhu dne, tj. pro každou hodinu, a zaznamenala do lineárního grafu. 

### Příčiny výkyvů poptávky

V grafu vytvořeném v předchozím kroku můžeme vidět, že v letních měsících je zvýšená poptávka po půjčování kol. Zvýšená poptávka začíná v květnu. Je to dáno tím, že v Edinburghu v tuto dobu probíhají různé festivaly - např. Edinburgh International Children's Festival (květen), Edinburgh Jazz & Blues Festival (červenec), Edinburgh International Festival (srpen), Art festival (srpen), Military festival (srpen), The Edinburgh Festival Fringe (srpen). Dále jsou 8.5., 25.5., 31.8. státní svátky. 25.5.2020 je jeden z dnů, kdy bylo vůbec nejvíc vypůjčených kol.

Vzhledem ke koronavirové situaci byly ale festivaly v roce 2020 v létě zrušeny. Proto zvýšený zájem o kola od května v tomto roce musíme hledat jinde.

V květnu v Edinburghu a Lothianu otevíralo stále více podniků. Blížilo se léto a všichni si mysleli, že to nejhorší z pandemie je za nimi. Přišel červen, léto, a s ním i nepolapitelné skotské slunce. Omezení způsobu života se nadále uvolňovala a z hlediska roku 2020 to vypadalo dobře. Koncem byl nejteplejší den v roce (v té době), a přimělo tisíce lidí, aby se vydali na pláže a volná prostranství. To způsobilo davy lidí na pláži Portobello. (zdroj: https://www.edinburghnews.scotsman.com/news/people/edinburgh-evening-news-2020-review-look-back-our-most-popular-stories-year-3072023)

Naopak myslím, že nikoho nepřekvapí fakt, že dny, kdy byla nejmenší poptávka po půjčování kol v Edinburghu jsou např. vánoční svátky nebo dny v zimních měsících.

### Vliv počasí na poptávku po kolech

Nejprve jsem si načetla tabulku s počasím `edinburgh_weather` a uložila ji do DataFrame `df_weather`. Poté jsem opět pracovala s kopií `bikes_df` s přidanou informací `date_start`. Použila jsem i informaci o počtu výpůjček za den a uložila ji do DF `daily_rents`. 

Protože jsem chtěla pracovat s teplotou jako s číslem, musela jsem odstranit nečíselné znaky. Toho jsem dosáhla následovně: 
`df_weather['temp_int'] = df_weather['temp'].str.replace(r'[^0-9]+', '').astype(int)`

Poté už jsem mohla pracovat s údajem `temp_int` jako s číslem, tj. použít funkci mean a vypočítat průměrnou teplotu za každý den v tabulce. 
DF s průměrnou teplotou za každý den jsem spojila s DF `daily_rents` následovně: `temp_mean_rents_df = temp_mean_df.join(daily_rents)[['temp_int', 'total_daily_rents']].dropna()` a to z toho důvodu, že DF s informacemi o počasí nepokrývá takové časové období jako DF o výpůjčkách kol (proto levý join k průměrné teplotě a ne naopak). 

Dále jsem přidala dva sloupce - odchylky od průměru výpůjček kol a od průměrné denní teploty a tuto nový DF pojmenovala `temp_mean_rents_diff_df`. 

Stejným způsobem jsem pracovala s informacemi o srážkách (DF `rain_sum_rents_diff_df`, kde jsou ale celkové srážky za den, ne průměr), o nárazovém větru (DF `gust_mean_rents_diff_df`) a o rychlosti větru (DF `wind_mean_rents_diff_df`). 

Pro každý výsledný DataFrame jsem vytvořila korelační matici a poté pro každý ze 4 údajů vytvořila bodový graf, který by nám mohl ukázat případné závislosti. 
Data odpovídají korelačním maticím. Můžeme vidět, že existuje pozitivní korelace - přímá úměra - mezi počtem výpůjček za den a průměrnou denní teplotou. O vztahu mezi množstvím srážek a počtem výpůjček nejsme schopni nic říct, vzhledem k tomu, že korelační koeficient je téměř 0. Naopak u nárazového větru a větru můžeme vidět mírnou zápornou korelaci s počtem výpůjček za den.

## Rozdíl mezi poptávkou o víkendu a v pracovních dnech

Opět jsem pracovala s kopií DF `bikes_df` a přidala údaje jako `date_start` atd. Vypočítala jsem kolik dní provozu bylo v letech 2018 - 2021 a kolik z nich bylo pracovních a kolik víkendových. 

Poté jsem pracovala s celkovými počty za celé časové období - tj. vypočítala kolik bylo celkově výpůjček, kolik jich bylo v pracovních dnech a kolik o víkendu a kolik bylo průměrně za den výpůjček, kolik průměrně o víkendu a kolik průměrně v pracovních dnech. Tytéž informace jsem vypočítala pro každý rok 2018 - 2021 zvlášť. Tyto informace jsem zaznamenala do "kolekce" grafů, která nám ukazuje koláčové grafy, kde je podíl výpůjček o víkendu a pracovních dnech pro každý rok i celkově, dále ukazuje také sloupcový graf, kde je znázorněn průměrný počet výpůjček o víkendových a v pracovních dnech. V sloupcovém grafu nalezneme také informaci, o kolik procent byla poptávka po půjčování kol vyšší o víkendu než v pracovních dnech. 
