# chmod – ändra filrättigheter i Linux

`chmod` (change mode) används för att ändra vilka rättigheter filer och mappar har – alltså vem som får läsa, skriva eller köra dem.

## 1. Grunderna: tre typer av användare

Varje fil har rättigheter definierade för tre grupper:

- **u** (user) – ägaren av filen
- **g** (group) – gruppen filen tillhör
- **o** (others) – alla andra

## 2. Tre typer av rättigheter

- **r** (read) – läsa filen / lista mappens innehåll
- **w** (write) – ändra filen / skapa-ta bort filer i mappen
- **x** (execute) – köra filen som program / gå in i mappen (`cd`)

Kör man `ls -l` ser man rättigheterna så här:

```
-rwxr-xr--  1 anna users  1024 sep 16 10:00 script.sh
```

Detta bryts ner så här:
- Första tecknet: filtyp (`-` = fil, `d` = mapp)
- Tecken 2-4 (`rwx`) = ägarens rättigheter
- Tecken 5-7 (`r-x`) = gruppens rättigheter
- Tecken 8-10 (`r--`) = övrigas rättigheter

## 3. Två sätt att skriva chmod-kommandon

### A) Symbolisk notation (bokstäver)

Syntax: `chmod [vem][operator][rättighet] fil`

- Vem: `u`, `g`, `o`, `a` (alla)
- Operator: `+` (lägg till), `-` (ta bort), `=` (sätt exakt)
- Rättighet: `r`, `w`, `x`

**Exempel:**
```bash
chmod u+x script.sh      # lägg till körrätt för ägaren
chmod g-w fil.txt        # ta bort skrivrätt för gruppen
chmod o=r fil.txt        # sätt övrigas rättighet till enbart läsning
chmod a+r fil.txt        # alla får läsrätt
chmod u+x,g+x script.sh  # flera ändringar samtidigt
```

### B) Numerisk (oktal) notation

Varje rättighet har ett talvärde:

| Rättighet | Värde |
|-----------|-------|
| r (läs)   | 4     |
| w (skriv) | 2     |
| x (kör)   | 1     |

Man summerar värdena för varje grupp (ägare, grupp, övriga):

- `7` = rwx (4+2+1)
- `6` = rw- (4+2)
- `5` = r-x (4+1)
- `4` = r--
- `0` = inga rättigheter

**Exempel:**
```bash
chmod 755 script.sh
```
Detta betyder:
- Ägare: `7` = rwx
- Grupp: `5` = r-x
- Övriga: `5` = r-x

```bash
chmod 644 dokument.txt
```
- Ägare: `6` = rw-
- Grupp: `4` = r--
- Övriga: `4` = r--

## 4. Praktiska exempel

**Göra ett skript körbart** (mycket vanligt):
```bash
chmod +x deploy.sh
./deploy.sh
```

**Skydda en känslig fil så bara ägaren kan läsa/skriva:**
```bash
chmod 600 hemligt.txt
```

**Standardrättigheter för en mapp som flera ska kunna använda:**
```bash
chmod 775 delad_mapp/
```

**Ändra rekursivt i en hel mappstruktur** (t.ex. för en webbserver):
```bash
chmod -R 755 /var/www/html
```
`-R` gör att ändringen sprids till alla filer och undermappar.

## 5. Vanliga kombinationer att komma ihåg

| Oktal | Betydelse                          | Typiskt användningsfall            |
|-------|-------------------------------------|-------------------------------------|
| 755   | Ägare full, andra läs+kör           | Skript, körbara program            |
| 644   | Ägare läs+skriv, andra bara läs     | Vanliga dokument, konfigfiler      |
| 600   | Bara ägaren kan läsa/skriva         | Lösenord, privata nycklar (SSH)    |
| 700   | Bara ägaren har full åtkomst        | Privata skript/mappar              |
| 777   | Alla får göra allt (undvik!)        | Sällan bra idé – säkerhetsrisk     |

## 6. Ett viktigt säkerhetstips

Undvik `chmod 777` om du inte har en mycket god anledning – det ger *vem som helst* full kontroll över filen, vilket är en vanlig säkerhetsbrist, särskilt på servrar.

---

Vill du att jag också går igenom `chown` (byta ägare) eller specialrättigheter som **SUID**, **SGID** och **sticky bit**? Det bygger naturligt vidare på det här.