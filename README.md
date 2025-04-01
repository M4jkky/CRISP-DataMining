# CRISP-DM

## Detekcia anomálií v sieťovej komunikácii

Tento projekt sa zameriava na detekciu anomálií v sieťovej komunikácii pomocou modelov strojového učenia, pričom postupujeme podľa metodiky CRISP-DM (Cross Industry Standard Process for Data Mining).

### Prehľad projektu

V tomto projekte analyzujeme sieťovú komunikáciu s cieľom identifikovať nezvyčajné vzory, ktoré môžu predstavovať bezpečnostné hrozby. Využívame rôzne techniky strojového učenia, vrátane popisných a prediktívnych modelov na odhalenie potenciálnych útokov v dátach.

## Štruktúra projektu:

### **Predspracovanie dát (`data_preprocessing.ipynb`)**:

1. **Načítanie dát**:
    - Transformácia cieľovej premennej na binárnu (0 = normal, 1 = anomaly)

2. **Výber atribútov**:
    - Na základe informačného zisku (importance > 0.02)
    - Na základe korelácie s cieľovým atribútom (korelácia > 0.1)
    - Prekódovanie kategorických premenných pomocou OneHotEncoder

3. **Korelačná analýza**:
    - Vizualizácia pomocou heatmapy
    - Odstránenie vysoko korelovaných atribútov (redundantná informácia)

4. **Transformácia distribúcie**:
    - Analýza šikmosti každého atribútu
    - Aplikácia logaritmickej transformácie na zošikmené atribúty

5. **Spracovanie outlierov**:
    - Detekcia pomocou metódy IQR (Interquartile Range)
    - Vizualizácia outlierov v závislosti od triedy (normal/anomaly)
    - Odstránenie outlierov len z normálnej triedy - dôležité pre zachovanie anomálií

6. **Optimalizácia pamäte**:
    - Konverzia float64 na float16
    - Konverzia int64 na int16

### **Exploračná analýza dát (`eda.ipynb`)**

1. **Načítanie dát a základné informácie**:
    - Import z `data/Train_data.csv`
    - Základné štatistiky pomocou `describe()` a `info()`
    - Kontrola chýbajúcich hodnôt a duplikátov

2. **Analýza kategorických atribútov**:
    - Vizualizácia početnosti pre atribúty `service`, `flag` a `class`
    - Grafické znázornenie vzťahu medzi kategorickými atribútmi a cieľovou premennou

3. **Analýza kvantitatívnych atribútov**:
    - Histogramy a KDE grafy pre každý numerický atribút
    - Boxploty na identifikáciu outlierov v dátach

4. **Korelačná analýza**:
    - Transformácia cieľového atribútu `class` z kategorickej na binárnu premennú
    - Vizualizácia korelačnej matice pred aj po one-hot encodingu
    - Identifikácia silno korelovaných atribútov (nad 0.97)

5. **Výpočet informačného zisku**:
    - Využitie `DecisionTreeClassifier` na výpočet dôležitosti atribútov
    - Vizualizácia atribútov s dôležitosťou > 0.01


### Použité dáta

Projekt pracuje s predspracovanými sieťovými dátami, ktoré zahŕňajú tieto kvantitatívne atribúty:
- `src_bytes` - počet bajtov od zdroja k cieľu
- `dst_bytes` - počet bajtov od cieľa k zdroju
- `hot` - počet "hot" indikátorov
- `count` - počet spojení na rovnaký cieľ v posledných 2 sekundách
- `serror_rate` - percento spojení s "SYN" chybami
- `rerror_rate` - percento spojení s "REJ" chybami
- `diff_srv_rate` - percento spojení na iné služby
- `dst_host_count` - počet spojení na rovnakú cieľovú IP adresu
- `dst_host_same_srv_rate` - percento spojení na rovnakú službu
- `dst_host_diff_srv_rate` - percento spojení na rôzne služby


### Výsledky

### Modeling 1

- **Random Forest**:
    - Dosiahol najvyššiu presnosť spomedzi testovaných modelov
    - Optimalizácia hyperparametrov výrazne zlepšila recall pre anomálnu triedu

- **K-Nearest Neighbors**:
    - Po optimalizácii hyperparametrov vykazuje výrazné zlepšenie presnosti
    - Najvyššia hodnota správnosti pre anomálnu triedu
    - Nájdená optimálna hodnota n_neighbors zabezpečuje vyvážený pomer precision/recall

- **Logistická regresia**:
    - Slúži ako baseline model pre porovnanie s komplexnejšími algoritmami
    - Cross-validácia ukazuje stabilnú výkonnosť na rôznych podmnožinách dát
    - ROC krivka ukazuje dobrú schopnosť rozlíšiť medzi normálnymi a anomálnymi prípadmi

### Modeling 2

- **Optimalizácia logistickej regresie**:
    - GridSearchCV našiel optimálne hyperparametre: C=1.0, penalty='l2', solver='liblinear'
    - Optimalizované nastavenia výrazne zlepšujú AUC skóre
    - Confusion matrix zobrazuje výrazné zníženie false negative prípadov

- **Vizualizačné techniky**:
    - Classification report zobrazený ako farebný bar plot pre lepšiu interpretáciu
    - Detailné porovnanie metrik pred a po optimalizácii
    - Export výsledkov do samostatných PNG súborov pre dokumentáciu

### Modeling 3

- **K-means clustering**:
    - Dosiahnutá Silhouette Score 0.685 potvrdzuje kvalitu vytvorených klastrov
    - Vizualizácia klastrov pomocou PCA zobrazuje jasné rozdelenie normálnych a anomálnych inštancií
    - Identifikované misklasifikované prípady sú zobrazené v samostatnom grafe

- **Asociačné pravidlá**:
    - Algoritmus Apriori identifikoval frekventované vzory s podporou nad 0.01
    - Vygenerované pravidlá s minimálnou hodnotou spoľahlivosti 0.7
    - Vizualizácia vzťahu medzi support, confidence a lift metrikami pomocou farebného scatter plotu

### Vizualizácie

Projekt obsahuje rozsiahle vizualizácie výsledkov:
- ROC krivky pre klasifikačné modely s výpočtom AUC
- Matice zámen zobrazujúce TP, TN, FP, FN hodnoty
- Klasifikačné reporty vizualizované pomocou bar plotov
- Scatter plot PCA dimenzií s vyznačenými anomáliami (`pca.png`)
- Vizualizácia správne a nesprávne klasifikovaných inštancií (`miss_true.png`)
- Vizualizácia asociačných pravidiel podľa support, confidence a lift metrík (`association_rules_scatterplot.png`)

