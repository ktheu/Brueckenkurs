## Daten und Codierung 

---

### Inhalt

1. [Information und Daten, Bits und Bytes](#1-information-und-daten-bits-und-bytes)
2. [Zahlensysteme umrechnen](#2-zahlensysteme-umrechnen-binär-hexadezimal-dezimal)
3. [Datenreduktion bei Bild und Ton](#3-datenreduktion-bei-bild-und-ton)
4. [Lauflängencodierung (RLE)](#4-lauflängencodierung-rle--verlustfrei-komprimieren)
5. [Verlustbehaftet vs. verlustfrei](#5-verlustbehaftete-vs-verlustfreie-kompression)

---

### 1. Information und Daten, Bits und Bytes

**Information** muss immer in geeigneter Weise dargestellt werden, um sie als **Daten** maschinell weiterverarbeiten zu können. Aus Daten gewinnt man erst dann Information, wenn sie gedeutet werden können.

<img src="bild1.png" alt="Information und Daten" width="300"/>

### 2. Zahlensysteme umrechnen: Binär, Hexadezimal, Dezimal

Computer speichern alles als Bits (0/1). Weil lange Bitfolgen unübersichtlich sind, fasst man sie zu **Vierergruppen** zusammen und schreibt sie hexadezimal.

#### A · Binär → Dezimal (Stellenwerte addieren)

Jede Bitstelle hat einen Stellenwert, der von rechts nach links mit der Basis 2 potenziert wird (…, 2³, 2², 2¹, 2⁰). Man multipliziert jede Ziffer (0 oder 1) mit ihrem Stellenwert und addiert alles.

Beispiel 10110101 (Bin) → Dezimal:
```
10110101 binär = 1*2^7 + 0*2^6 + 1*2^5 + 1*2^4 + 0*2^3 + 1*2^2 + 0*2^1 + 1*2^0
         = 128 + 0 + 32 + 16 + 0 + 4 + 0 + 1 = 181
```

#### B · Hexadezimal → Dezimal (Stellenwerte addieren)

Genauso wie bei Binär, nur mit der Basis 16: Jede Stelle wird von rechts nach links mit ihrem Stellenwert (16⁰, 16¹, 16², …) multipliziert; die Ergebnisse werden addiert.

Beispiel D6B (Hex) → Dezimal:
```
D=13   6   B=11
13·16² + 6·16¹ + 11·16⁰
= 3328 + 96 + 11 = 3435
```

#### C · Binär → Dezimal über Hexadezimal

Statt eine Binärzahl direkt über 2er-Potenzen umzurechnen (siehe A), kann man sie auch zunächst in Hexadezimal umwandeln und anschließend wie in B in Dezimal umrechnen.

**Verfahren für Binär → Hexadezimal (Vierergruppen bilden):**
1. Bitfolge von rechts in Blöcke zu je 4 Bit einteilen, ggfs. links mit Nullen auffüllen.
2. Jeden 4er-Block einzeln in seine Hexziffer übersetzen (Tabelle unten).
3. Die Hexziffern in der gleichen Reihenfolge aneinanderreihen.

| Bin  | Hex | Bin  | Hex | Bin  | Hex | Bin  | Hex |
| ---- | --- | ---- | --- | ---- | --- | ---- | --- |
| 0000 | 0   | 0100 | 4   | 1000 | 8   | 1100 | C   |
| 0001 | 1   | 0101 | 5   | 1001 | 9   | 1101 | D   |
| 0010 | 2   | 0110 | 6   | 1010 | A   | 1110 | E   |
| 0011 | 3   | 0111 | 7   | 1011 | B   | 1111 | F   |

Beispiel: `10110101110` (Bin) → Dezimal, über Hex

```
Binär in 4er-Blöcke: 101  1010  1110
Hexadezimal:           5    A     E    = 5AE
Dezimal:             14*16^0 + 10*16^1 + 5*16^2 = 14 + 160 + 1280 = 1454
```

Zur Kontrolle der direkte Weg (A) für dieselbe Zahl:
```
10110101110 binär = 1*2^1 + 1*2^2 + 1*2^3 + 1*2^5 + 1*2^7 + 1*2^8 + 1*2^10
                   = 2 + 4 + 8 + 32 + 128 + 256 + 1024 = 1454
```

Beide Wege führen auf dasselbe Ergebnis — das ist eine gute Möglichkeit, das eigene Ergebnis zu kontrollieren.

> **Tipp:** Stimmen die beiden Ergebnisse nicht überein, liegt meist ein Rechenfehler bei den Stellenwerten (A/B) oder ein falsch gebildeter 4er-Block vor — dann lohnt es sich, beide Rechnungen noch einmal Schritt für Schritt zu vergleichen.

#### D · Dezimal → Binär (fortgesetzte Division)

Das im Unterricht verwendete Verfahren: Immer wieder durch 2 teilen, den **Rest** notieren, mit dem **ganzzahligen Ergebnis** weiterrechnen — bis 0 übrig bleibt. Die Reste **von unten nach oben** gelesen ergeben die Zahl.

Beispiel: 177 → Binär
```
177 : 2 = 88 Rest 1
 88 : 2 = 44 Rest 0
 44 : 2 = 22 Rest 0
 22 : 2 = 11 Rest 0
 11 : 2 =  5 Rest 1
  5 : 2 =  2 Rest 1
  2 : 2 =  1 Rest 0
  1 : 2 =  0 Rest 1

von unten nach oben gelesen: 10110001
```

#### E · Dezimal → Hexadezimal (fortgesetzte Division)

Gleiches Verfahren wie in D, nur wird durch 16 statt durch 2 geteilt.

Beispiel: 2749 → Hexadezimal
```
2749 : 16 = 171 Rest 13 = D
 171 : 16 =  10 Rest 11 = B
  10 : 16 =   0 Rest 10 = A

von unten nach oben gelesen: ABD
```

> **Typische Fehler (D, E):**
> - Reste werden in der falschen Reihenfolge notiert — *von unten nach oben* lesen, nicht von oben nach unten!
> - Bei Hex-Resten über 9 an die Buchstaben A–F denken (10=A … 15=F).
> - Bei Bin→Hex Blöcke immer von rechts bilden; bei einer ungeraden Bitanzahl links mit Nullen auffüllen.

---

### 3. Datenreduktion bei Bild und Ton

Bild- und Audiodateien lassen sich verkleinern, indem man an genau einer von zwei Stellschrauben dreht: **wie oft** gemessen wird oder **wie genau** jede einzelne Messung gespeichert wird. Bei jeder Reduktion gilt: kleinere Datei, aber Informationsverlust.

### Bilder: Auflösung und Farbtiefe

| Größe         | Was sie beschreibt                                           | Wirkung einer Verringerung                                                                              |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| **Auflösung** | Anzahl der Pixel (Bildpunkte), aus denen das Bild besteht    | Mehrere Nachbarpixel werden zu einem zusammengefasst → gröberes Raster, weniger Pixelwerte zu speichern |
| **Farbtiefe** | Anzahl Bits, mit denen die Farbe *eines* Pixels codiert wird | Weniger unterscheidbare Farben pro Pixel → ähnliche Farbtöne werden zusammengefasst ("Colour Banding")  |

> **Merksatz:** 24 Bit Farbtiefe erlauben ca. 16,7 Mio. Farben; 8 Bit nur noch 256. Die Datenmenge pro Pixel sinkt dabei auf ein Drittel — aber ähnliche Farben werden nicht mehr unterschieden.

### Audio: Samplingrate und Samplingtiefe

Beim Aufnehmen wird die Schallwelle in festen Zeitabständen **abgetastet (gesampelt)**. Die **Samplingrate** (in Hz) gibt an, wie viele Abtastwerte pro Sekunde gespeichert werden.

**Argumentationsmuster für Aufgaben zur Samplingrate:**
1. Verhältnis bilden: neue Rate ÷ alte Rate (z. B. 11 025 Hz ÷ 44 100 Hz = ¼).
2. Folgerung für die Dateigröße: nur noch dieser Anteil an Messwerten pro Sekunde → Datei entsprechend kleiner.
3. Folgerung für die Qualität: hohe Frequenzanteile (hohe Töne) werden nicht mehr korrekt erfasst → Klang wirkt weniger originalgetreu. Es handelt sich um verlustbehaftete Kompression.

---

### 4. Lauflängencodierung (RLE) — verlustfrei komprimieren

Idee: Statt jedes einzelne Bit/Zeichen zu speichern, zählt man, wie oft derselbe Wert **direkt hintereinander** vorkommt, und speichert nur *(Anzahl, Wert)*-Paare. Das funktioniert verlustfrei — beim Entpacken entsteht exakt die Ausgangsfolge zurück.

### Verfahren an einem Schwarz-Weiß-Streifen

Bitfolge (0 = weiß, 1 = schwarz): `0000111111000001111100000`

**Verfahren:**
1. Die Folge in Läufe gleicher Werte zerlegen.
2. Jeden Lauf zählen.
3. Als Paar `(Wert, Anzahl)` bzw. `(Anzahl, Wert)` notieren — je nachdem, was in eurem Unterricht vereinbart wurde.

```
Läufe:     0000 | 111111 | 00000 | 11111 | 00000
Längen:      4  |   6    |   5   |   5   |   5

RLE-Code: (0,4) (1,6) (0,5) (1,5) (0,5)
```

### Datenmenge und Ersparnis berechnen

1. Größte vorkommende Zahl bestimmen → daraus die nötige Bitbreite pro Zahl ableiten (z. B. reicht bei max. 12 eine Breite von 4 Bit, weil 2⁴=16 > 12).
2. Ursprüngliche Datenmenge: Anzahl Bits der Originalfolge × 1 Bit.
3. Komprimierte Datenmenge: Anzahl der Läufe × Bitbreite pro Zahl.
4. Ersparnis: `1 − (komprimiert / original)`, als Prozent angeben.

### Wann lohnt sich RLE — und wann nicht?

RLE lohnt sich nur, wenn viele gleiche Werte *direkt hintereinander* stehen — etwa in einfarbigen Flächen eines Schwarz-Weiß-Bildes. Bei normalem Fließtext (z. B. `"Hallo"`) oder bei Fotos mit vielen leicht unterschiedlichen Farbwerten wechseln sich die Werte zu oft ab, sodass die Läufe kurz bleiben und die codierte Fassung sogar größer werden kann als das Original.

---

### 4. Verlustbehaftete vs. verlustfreie Kompression

Die entscheidende Frage: **Muss nach dem Entpacken exakt das Original wiederhergestellt werden — oder darf ein kleiner, kaum wahrnehmbarer Unterschied entstehen?**

| Format / Anwendung       | Art               | Begründung                                                       |
| ------------------------ | ----------------- | ---------------------------------------------------------------- |
| MP3 (Musik)              | 🔴 verlustbehaftet | Für das Gehör kaum wahrnehmbare Frequenzanteile werden entfernt. |
| JPEG (Foto)              | 🔴 verlustbehaftet | Kompromiss zwischen Dateigröße und Bildqualität.                 |
| PNG (Grafik, Screenshot) | 🟢 verlustfrei     | Bilddaten müssen pixelgenau erhalten bleiben.                    |
| ZIP (Programmcode, Text) | 🟢 verlustfrei     | Nach dem Entpacken muss exakt der Originaltext/-code vorliegen.  |

Programmcode und Textdokumente müssen *bit-genau* erhalten bleiben — schon ein verändertes Zeichen (z. B. ein Semikolon) kann Code unbrauchbar machen oder die Bedeutung eines Textes verändern. Bei Video und Musik nimmt der Mensch kleine Abweichungen dagegen kaum wahr, sodass zugunsten einer viel kleineren Datei ein geringer Qualitätsverlust akzeptiert wird — etwa beim Video-Streaming (begrenzte Bandbreite) oder bei Videotelefonie (Echtzeitübertragung, geringe Latenz wichtiger als volle Qualität).

---
