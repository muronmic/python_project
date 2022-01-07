# python_project

V tomto repozitáři naleznete vypracovaný python projekt. Projekt obsahuje mapy, proto doporučuji se na výsledný notebook podívat pomocí následujícího odkazu: 
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

Potom jsem načetla tabulku edinburgh_bikes, uložila ji do `bikes_df` a mohla dále s touto tabulkou pracovat. Vytáhla jsem si z této tabulky seznam všech stanic podle jejich ID a přidala k nim jejich jméno a polohu, jelikož tyto informace byly nadále potřeba, a uložila je do tabulky `bikes_station_names`. 

### Aktivní a neaktivní stanice

Vytvořila jsem si kopii tabulky `bikes_df`. Jako první jsem přidala položky `date_start`, `date_end`, `month`, `weekday`, `weekend` a `year` do tabulky. 

Za neaktivní stanice jsem považovala ty, které se v posledním roce, tj. v roce 2021, nepoužily ani jednou. Neaktivní stanice jsem vyselektovala pomocí `query` a `merge`. Získala jsem tak seznam stanic s jejich ID, názvem a polohou, kterou jsem později použila. 

Dále jsem spočítala průměrný počet výpůjček a vrácení kol v roce 2021 pro zbylé - aktivní - stanice (první jsem spočítala sumu přes všechny dny pro každou stanici a poté pomocí `groupby` a  `apply(lambda x: np.sum(x) / days_2021_count)` jsem spočítala průměrný počet výpůjček za den v roce 2021). 

Za aktivní jsem označila stanice, které byly použity jako startovní a nebo cílová stanice v posledním roce (2021) průměrně alespoň 5x za den. Za málo aktivní stanice jsem označila ty zbývající, které byly v roce 2021 použity. 

Všechny stanice jsem potom zaznamenala do mapy, kde zeleně jsou zaznamenány aktivní stanice, oranžovou stanice používané v roce 2021, ale méně než 5x za den, a šedou barvou stanice které nebyly v roce 2021 použity vůbec. V mapě je možno filtrovat, takže si můžeme zobrazit např. pouze stanice nepoužívané v roce 2021.

### Nejfrekventovanější stanice

Opět jsem si vytvořila kopii tabulky `bikes_df` a přidala k ní informaci o dni, měsíci atd. Rozhodla jsem se počítat jakoukoli aktivitu na stanici, tj. půjčení i vrácení kola. 

Pomocí `grouby` a `count()` jsem spočítala celkový počet půjčení kol a počet vrácení kol na stanici. Vytvořila jsem sloupec `activity_sum`, který je součtem vrácených kol a půjčených kol na stanici. Vznikla tak tabulka `activity_count_df`, která obsahuje informace o počtu půjčených, vrácených kol a navíc obsahuje informace o stanici - jméno, ID a polohu. 

Dále jsem počítala průměrný počet půjčení a vrácení kol za den pro každou stanici v pracovních dnech a o víkendu a uložila tyto informace do `means_df`. Průměr jsem počítala přes všechny pracovní dny, víkendy za celou dobu fungování služby bike sharingu, ne pouze pro dny, kdy fungovaly dané stanice. Je tedy jasné, že např. v případě stanice Portobello - Kings Road (ID 1728) by průměr vystoupal, kdyby se počítalo pouze s aktivními dny pro stanici.
Poté jsem zobrazila 5 nejfrekventovanějších stanic z tabulky `activity_count_df` s jejich počty výpůjček/vratek a pro těchto 5 stanic jsem vytáhla informace z `means_df` o průměrné aktivitě o víkendu a pracovních dnech. 

### Stanice, kde se kola hromadí a kde potenciálně chybí

Opět jsem si vytvořila kopii tabulky `bikes_df` a přidala další informace, stejně jako výše. 

Vypočítala jsem počet odchozích kol ze stanice za den, tj. počet výpůjček na stanici, a počet příchozích kol, tj. počet vratek na stanici za den. 
Poté jsem vytvořila sloupec `total_bikes`, který je rozdíem příchozích a odchozích kol. 

Vytvořila jsem tabulky `shortage_df` (stanice s nedostatkem - ty, které mají průměrně za den o 2 kola méně)  a `excess_df` (stanice s nadbytkem - ty, které mají průměrně za den o 2 kola více). Pozn. opět jsem pracovala pouze se stanicemi, které se v roce 2021 použily alespoň jednou. Stanice v těchto tabulkách jsem zobrazila do mapy, zeleně jsou označené stanice s nadbytkem, červeně s nedostatkem. 


## Vzdálenosti mezi stanicemi

Tady jsem použila tabulky `bikes_station_names` a `stations_2021_df` (to je tabulka, kde jsou uloženy stanice, které se použily v roce 2021). Použila jsem knihovny `geopy`, pomocí které jsem byla schopna vyčíslit vzdálenosti mezi stanicemi, a `qgrid`, díky které jsem mohla zobrazit tabulku se vzdálenostmi. Vyzkoušela jsem také vytvořit widgety pomocí `ipywidgets` 

Bohužel interaktivní tabulky s použitím qgrid nebo ipywidgets nejsou v githubu nebo v nbviewer viditelné, proto jsem zobrazila alespoň statický HTML náhled.

## Délka jedné výpůjčky


