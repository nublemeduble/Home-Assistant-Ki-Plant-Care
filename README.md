# Home-Assistant-Ki-Plant-Care Gießprognose 
KI-gesteuerte Gießprognose mit Gemini, basierend auf 20 Stunden Historie von Gießwaage (ml), Temperatur und Luftfeuchtigkeit.

🌸 KI-Pflanzen-Gießprognose (Google Gemini)
Dies ist ein fortschrittliches Home Assistant Automation Blueprint, das Google Gemini (LLM) nutzt, um eine intelligente, trendbasierte Gießprognose für Zimmerpflanzen zu erstellen.
Die KI analysiert die Verbrauchshistorie (Gießwaage in ml, Temperatur, Luftfeuchtigkeit) der letzten 20 Stunden, um den tatsächlichen Wasserverlust zu berechnen und die optimale Gießmenge zu empfehlen. Ideal für Projekte mit ESPHome-Sensoren.
🛠️ Installation und Ersteinrichtung
0. ⚠️ VORAUSSETZUNGEN
Die folgenden Komponenten müssen in Ihrer Home Assistant Instanz konfiguriert und betriebsbereit sein:
LLM Vision Integration (Gemini): Die Integration muss installiert und ein Provider (z.B. google_gemini) eingerichtet sein, damit der Dienst llmvision.data_analyzer aufgerufen werden kann.
Sensoren (z.B. ESPHome): Sie benötigen funktionierende Sensoren für Temperatur, Luftfeuchtigkeit und einen Sensor für den Wasserstand/das Gewicht in Millilitern (ml).
1. Blueprint importieren
Kopieren Sie die URL des Blueprints und importieren Sie sie über die Home Assistant Oberfläche:
Einstellungen → Automatisierungen & Szenen → Blueprints → Blueprint importieren
2. 🛑 WICHTIG: Helfer Anlegen (Die Datenbasis)
Die Automatisierung benötigt drei spezifische Helfer (input_text), um die 20-stündige Historie zu speichern. Diese Helfer müssen manuell angelegt werden.
Anleitung: Gehen Sie zu Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Text.
Helfer-ID: input_text.temperatur_history (Typ: Text, Max. Länge: 255)
Helfer-ID: input_text.luftfeuchtigkeit_history (Typ: Text, Max. Länge: 255)
Helfer-ID: input_text.wasser_history (Typ: Text, Max. Länge: 255)
3. Sensor-Kompatibilität
Erforderlich: Der Hauptsensor (water_ml_sensor) muss das Gesamtvolumen des Wassers in Millilitern (ml) messen (z.B. eine tarierte Gießwaage). Der Wert muss beim Verbrauch kontinuierlich sinken.
Nicht geeignet: Ein gewöhnlicher Bodenfeuchtesensor (der nur %-Feuchtigkeit misst), da die KI das Volumen und den Verbrauchs-Trend analysieren muss.
🟢 Erweiterung: Nutzung von Bodenfeuchtesensoren (Alternativer Ansatz)
Dieses Blueprint ist nicht für die Analyse von Bodenfeuchte-Prozentwerten ausgelegt. Wenn Sie einen Bodenfeuchtesensor (ESPHome) nutzen möchten, müssen Sie eine separate, zusätzliche Automatisierung erstellen.
Schwellenwert definieren: Legen Sie einen input_number-Helfer als Schwellenwert fest (z.B. input_number.minimum_feuchtigkeit_prozent auf 35).
KI-Trigger: Erstellen Sie eine neue Automatisierung, die nur dann triggert, wenn der Bodenfeuchtesensor unter den Schwellenwert fällt.
Neuer Prompt: Der LLM-Prompt muss dann die KI fragen: "Die Feuchtigkeit ist auf (aktueller Wert)% gefallen. Basierend auf der aktuellen Temperatur und Luftfeuchtigkeit, wie dringend muss jetzt gegossen werden?"
⚙️ Funktionsweise und Wartung
A. Das 20-Stunden-Rolling-Window
Die stündliche Datensammlung funktioniert wie ein Schiebefenster: Der neueste Wert wird angehängt, und der älteste Wert (21. Eintrag) wird gelöscht. Dadurch hat die KI immer die Daten der letzten 20 Stunden zur Verfügung.
B. Anpassung der Analyse-Zeiten
Die KI-Analyse wird nur zweimal täglich durchgeführt. Passen Sie die Zeiten im Blueprint an Ihren Beleuchtungszyklus an, um die besten Prognosen zu erhalten:
Vegetation (18/6): Morgen-Analyse: 09:00:00, Abend-Analyse: 22:00:00
Blüte (12/12): Morgen-Analyse: 09:00:00, Abend-Analyse: 18:00:00
C. ⚠️ Anleitung zum Umtopfen / Neustart
Dies ist der kritischste Wartungsschritt, da ein Topfwechsel die Waagenhistorie springen lassen würde.
Waage Tarieren: Topfen Sie die Pflanze um. Stellen Sie den fertigen, aber trockenen Topf auf die Waage und tarieren Sie die Waage (Waage zeigt 0 ml).
Helfer Leeren: Gehen Sie in Home Assistant zu den drei input_text Helfern und löschen Sie den gesamten Inhalt.
Gießen: Gießen Sie die Pflanze (z.B. 2500 ml).
Automatisierung Startet: Warten Sie bis zur nächsten vollen Stunde. Die Historie beginnt sauber mit dem neuen, hohen Startwert
