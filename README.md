# callout-maker
a python everything except frontmatter to callout maker

import os

# --- KONFIGURATION ---
ORDNER_PFAD = '...'               
ZIEL_ORDNER = '...' 
CALLOUT_NAME = '...'
# ---------------------

if not os.path.exists(ZIEL_ORDNER):
    os.makedirs(ZIEL_ORDNER)

for dateiname in os.listdir(ORDNER_PFAD):
    if dateiname.endswith('.md'):
        pfad_quelle = os.path.join(ORDNER_PFAD, dateiname)
        pfad_ziel = os.path.join(ZIEL_ORDNER, dateiname)
        
        # WICHTIG: utf-8-sig entfernt unsichtbare BOM-Zeichen am Dateianfang!
        with open(pfad_quelle, "r", encoding="utf-8-sig") as f:
            zeilen = f.read().splitlines()
        
        frontmatter = []
        body = []
        
        # Leerzeilen am absoluten Dateianfang überspringen
        start_idx = 0
        while start_idx < len(zeilen) and zeilen[start_idx].strip() == "":
            start_idx += 1
        
        # Prüfen, ob nach eventuellen Leerzeilen das Frontmatter beginnt
        if start_idx < len(zeilen) and zeilen[start_idx].strip() == '---':
            schliesser_index = -1
            # Nach dem schließenden '---' suchen
            for i in range(start_idx + 1, len(zeilen)):
                if zeilen[i].strip() == '---':
                    schliesser_index = i
                    break
            
            if schliesser_index != -1:
                # Frontmatter und Text sauber trennen
                frontmatter = zeilen[start_idx:schliesser_index + 1]
                body = zeilen[schliesser_index + 1:]
            else:
                body = zeilen[start_idx:]
        else:
            body = zeilen[start_idx:]

        # --- Neue Datei schreiben ---
        with open(pfad_ziel, "w", encoding="utf-8") as f_neu:
            
            # 1. Frontmatter ausgeben (ohne Blockquote-Zeichen!)
            if frontmatter:
                for fm_zeile in frontmatter:
                    f_neu.write(f"{fm_zeile}\n")
                f_neu.write("\n") 
            
            # 2. Den Callout starten
            f_neu.write(f"> [!info] {CALLOUT_NAME}\n")
            
            # 3. Inhalt einrücken (führende Leerzeilen entfernen)
            while body and body[0].strip() == "":
                body.pop(0)

            for zeile in body:
                f_neu.write(f"> {zeile}\n")

print(f"Fertig! Die Dateien in '{ZIEL_ORDNER}' sollten das Frontmatter jetzt oben behalten haben.")
