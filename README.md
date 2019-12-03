# Game of life

## Specifikace

Implementace [Conwayovy Hry života](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) v okenní aplikaci.

Vývoj generací buněk je zobrazován za sebou ve snímcích jako animace.
Program umožňuje:
- Nakreslit počáteční rozložení buněk v dostatečně velké mřížce.
- Nastavit počty sousedů buněk pro přežití a narození nových.
- Změnit rychlost animace na některou z přednastavených hodnot.
- Přiblížit a oddálit herní plochu.
- Uložit a načíst sestavu buněk z textového souboru.

## Použití
Spusťte soubor `main.py`, zobrazí se okno programu. V horním panelu jsou k dispozici tlačítka pro minimalizaci, maximalizaci a zavření okna.
![App window](Images/app-window.png)

### Tvorba mřížky
1. Kliknutím na **New Board** vytvořte prázdnou mřížku.
2. Buňky kreslete držením levého tlačítka myši. Úpravy lze provádět jen před spuštěním animace nebo po jejím resetování. V pravém menu jsou k dispozici 3 módy:

    - **Add** - přidávat buňky.
    - **Remove** - odebírat buňky.
    - **Toggle** - změnit současný stav buňky. Po celou dobu jednoho tahu je nastaven jako *Add* nebo *Remove* podle stavu 1. buňky, na kterou bylo kliknuto.
3. Plochu přibližujte a oddalujte pomocí posuvníku **Zoom** v pravém menu nebo klávesami "**+**" a "**-**".
4. Na mřížce se pohybujte se pomocí šipek.
5. Pro uložení klikněte na tlačítko **Save**. Zobrazí se dialog, v něm vyberte název souboru a cestu, potvrzením soubor uložte.
6. Načtení provedete kliknutím na **Open**. V dialogu najděte příslušný soubor a potvrďte.

### Animace
1. Animaci spusťte tlačítkem **Play**. Text tlačítka se změní na **Stop** a opětovným kliknutím animaci pozastavíte.
2. Rychlost animace lze upravit posuvníkem **Speed** v pravém menu.
3. Číslo současné generace je označen nápisem **Gen**.
4. Animaci resetujete pomocí tlačítka **Reset**.

## Dokumentace
V popisu tříd jsou uvedeny pouze nejdůležitější datové struktury a metody.

### Použité knihovny
Zejména pro vzhled aplikace byly použity některé knihovny:
- **Tkinter** - okno programu a jeho komponenty, zpracování událostí od uživatele.
- **Pillow** - kreslení na obrázek v paměti.
- **Time** - měření času mezi snímky animace.
- **Os** - nalezení cesty k adresáři hlavního programu, která se použije jako výchozí při načítání herní plochy.
- **Typing** - doplnění některých datových typů pro našeptávač, které nepatří mezi standardní.

### Moduly
- **gol.py:**  
Třídy určené pro výpočet, kreslení a animaci: `Board`, `Rule`, `Painter`, `Animator`.
- **main.py:**  
Hlavní program se třídami: `FileManager`, `TkState`, `Application`.

### Třída `Board`
Obsahuje informace o herní ploše (seznam buněk) a stará se o výpočet dalších generací.
Umožňuje přidávat a odebírat buňky.

#### Proměnné:
- **current:**  
Mřížka současné generace buněk jako 2D seznam proměnných bool (`True` = živá buňka, `False` = mrtvá buňka).
- **living:**  
Seznam souřadnic živých buněk v seznamu `current`.

#### Metody:
- **next_gen() -> None:**  
Spočítá další generaci buněk podle počtu jejich sousedů.  
Prochází pouze živé buňky a jejich sousedy, protože ostatní buňky zůstanou nezměněné.

### Třída `Rule`
Slouží pro snadnější nastavování pravidel hry (počtů sousedů) pomocí textového řetězce.

#### Proměnné:
- **birth_rule:**  
Množina pro počty sousedů, které způsobí narození nové buňky.
- **remain_rule:**  
Množina pro počty sousedů, se kterými zůstane buňka naživu.

#### Metody:
- **try_set_rule(value: str) → bool:**  
Pokusí se nastavit textový řetězec `value` jako nové pravidlo.  
Vrací `True`, pokud je pravidlo správné, jinak `False`.

### Třída `Painter`
Kreslí herní plochu `Board` na plátno `Canvas`.

#### Proměnné:
- **grids:**  
Obrázky s mřížkou pro různé velikosti buněk. Slouží jako pozadí, na které se budou buňky kreslit.  
Vytváří se v metodě *reset*, kterou je nutné zavolat před jakýmkoliv kreslením.  
Lze mezi nimi přepínat a tím mřížku „přibližovat / oddalovat“.
- **m_cell:**  
Souřadnice buňky, která je zobrazena uprostřed mřížky.  
Se změnou těchto souřadnic se bude zobrazovat jiná část herní plochy.  
Obraz se tak může posouvat nahoru, dolů, anebo do stran.
- **canvas_image:**  
Objekt obrázku na plátně `Canvas` knihovny `tkinter`.  
Je natažen na celou velikost plátna, pokud nepřekračuje velikost mřížek.  
Nakreslení herní plochy je provedeno změnou jeho parametru *image*.

#### Metody:
- **reset() -> None:**  
Vytváří obrázky mřížek pro zadané velikosti.
- **draw_board() -> None:**  
Nakreslí herní plochu v těchto krocích:
  - Ořízne prostřední část mřížky z `grids` na velikost plátna.
  - Podle souřadnic buňky kreslené doprostřed (`m_cell`) nakreslí všechny viditelné buňky na vyříznutou mřížku.
  - Výsledný obrázek nastaví jako parametr `image` objektu `canvas_image`.

### Třída `Animator`
S využitím objektu typu `Painter` kreslí generace buněk za sebou jako animaci.

#### Proměnné:
- **time_per_gen**:  
Čas mezi kreslením generací.

#### Metody:
- **play() -> None:**  
Spustí animaci.
- **stop() -> None:**  
Pozastaví animaci.

### Třída `FileManager`
Statická třída, která načítá a ukládá plochu `Board` do souboru.
Výsledek zobrazuje v okně se zprávou (`messagebox`).

### Třída `TkState`
Statická třída, která aktivuje a deaktivuje komponenty `tkinter`.

### Třída `Application`
Propojuje výpočet a grafické rozhraní programu.
Obsahuje komponenty okna a objekty modulu `gol.py`, se kterými manipuluje podle událostí.

