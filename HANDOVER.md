# Device Lab – Handover

## Projekt
- **Lokaler Pfad:** `/Users/lakestudio/localhost/claude/apps/device-lab/`
- **Repo:** https://github.com/b0li/device-lab
- **Live:** https://b0li.github.io/device-lab/
- **Stack:** Single `index.html` – Vanilla JS, kein Build-Step, keine CDNs (außer Google Fonts)
- **Zweck:** Website-Emulator – fremde/eigene URLs in verschiedenen Geräte-Viewports als Layout-Check testen (iPhone Duo, gängige Phones, Foldables, Tablets).

---

## Funktionsweise

Ein `<iframe>`, dessen Container per JS auf feste CSS-Pixel-Viewports gesetzt wird. Kein echtes Device-Rendering (kein Safari-UI, keine Notch, keine Tastatur) – reiner **Layout-/Reflow-Check**.

### Kernmechanik
1. **Device-Presets** (`devices[]`): pro Eintrag `{ group, name, w, h }` oder `{ group, name, states:[…] }`. `w/h` sind CSS-Pixel im Hochformat.
2. **states**: Geräte mit mehreren Zuständen (Foldables, iPhone Duo) rendern einen zweiten Selektor (`#state`). Normale Geräte blenden ihn aus.
3. **Ausrichtung** (`orient`): Hochkant/Querformat – tauscht `w`/`h` per Segment-Control.
4. **Custom B×H** (`#cw`/`#ch`): manuelle Maße überschreiben das Preset (Titel → „Custom").
5. **Einpassen** (`fit`): skaliert den Device-Frame per `transform:scale()` runter, wenn er größer als der verfügbare Platz ist (iPad Pro, Landscape). Zeigt den Prozentwert.
6. **Block-Erkennung** (`watchBlocked()`): lädt der Frame nicht innerhalb 4s, wird ein Hinweis auf `X-Frame-Options`/CSP eingeblendet.
7. **Titel** (`#title`): spiegelt live das aktive Gerät inkl. Zustand (z.B. „iPhone Duo · Aufgeklappt Portrait").

### iframe-Einschränkung
Fremde Seiten mit `X-Frame-Options: DENY/SAMEORIGIN` oder CSP `frame-ancestors` lassen sich **nicht** einbetten (Google, Amazon etc.). Eigene/Kunden-Domains müssen das Einbetten erlauben. Kein Bug, prinzipbedingt.

---

## Geräte-Presets (CSS-Pixel, Hochformat)

| Gruppe | Gerät | B × H |
|---|---|---|
| Mobile | iPhone 6/7/8 | 375 × 667 |
| Mobile | iPhone 6/7/8 Plus | 414 × 736 |
| Mobile | iPhone SE | 375 × 667 |
| Mobile | iPhone 16 | 393 × 852 |
| Mobile | iPhone 16 Pro Max | 440 × 956 |
| Mobile | Pixel 9 / 9 Pro / 10 | 412 × 915 |
| Mobile | Samsung Galaxy A55 | 384 × 854 |
| Foldables | Pixel 9 Pro Fold | 412 × 915 / 842 × 1080 |
| Foldables | Galaxy Z Fold 6 | 344 × 882 / 653 × 829 |
| Tablets & Desktops | iPad Mini | 744 × 1133 |
| Tablets & Desktops | iPad Pro 13" | 1024 × 1366 |
| Tablets & Desktops | Surface Pro 10 | 912 × 1368 |
| Custom | iPhone 14 Pro | 393 × 852 |
| Custom | iPhone Duo | 466 × 678 / 626 × 890 / 890 × 626 |

> Foldable-Maße (v.a. Z Fold 6 aufgeklappt) sind Näherungswerte. Für exakte Tests das Custom-B×H-Feld nutzen.
> iPhone-Duo-Maße nach Marvin Langer: Hardware-Auflösung ÷ DPR 3.

Neues Gerät ergänzen: einen Eintrag im `devices[]`-Array hinzufügen – die Gruppe (`optgroup`) wird automatisch gebaut.

---

## Design System (LS-Standard, Dark + Gold)

```css
:root {
	--bg: #1a1d23;
	--surface: #22262e;
	--surface-2: #2a2f39;
	--border: #333844;
	--accent: #d4a55a;
	--accent-dim: rgba( 212, 165, 90, 0.15 );
	--green: #6abf69;
	--red: #e06c6c;
	--text: #e8eaf0;
	--text-dim: #8a909e;
	--radius: 10px;
}
```

### Fonts
```css
@import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@400;600;700&display=swap');
```
- **h1:** `DM Serif Display`, 2rem, weight 400, color `var(--accent)` – zeigt das aktive Gerät
- **Body:** `DM Sans`, system-ui Fallback

---

## Code-Style (LS-Standard)
- **Tabs** für Einrückung
- **Spaces inside Parentheses:** `document.querySelector( '.foo' )`
- **Space nach `!`:** `if ( ! el ) return;`
- **Closing Callbacks:** `});`
- Kommentare auf Englisch, Vanilla JS, kein Framework

---

## Deployment

GitHub Pages (main branch, root `/`, HTTPS).

```bash
cd /Users/lakestudio/localhost/claude/apps/device-lab
git add index.html
git commit -m "..."
git push
# → live auf https://b0li.github.io/device-lab/
```
