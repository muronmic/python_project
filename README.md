# python_project

V tomto repozitáři naleznete vypracovaný python projekt. Projekt obsahuje mapy, proto doporučuji se na výsledný notebook podívat pomocí následujícího odkazu: 
https://nbviewer.org/github/muronmic/python_project/blob/main/Project_Muronmic_final.ipynb?flush_cache=true

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

Potom jsem načetla tabulku edinburgh_bikes a mohla dále s touto tabulkou pracovat. Vytáhla jsem si z této tabulky seznam všech stanic podle jejich ID a přidala k nim jejich jméno a polohu, jelikož tyto informace byly nadále potřeba. 

### Aktivní a neaktivní stanice
