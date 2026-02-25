# JavaScript

Język programowania przeglądarek internetowych — dziś używany też po stronie serwera (Node.js).

```javascript
const powitanie = (imie) => `Cześć, ${imie}!`;

// Async/await — nowoczesny JavaScript
async function pobierzDane(url) {
  const odpowiedz = await fetch(url);
  return odpowiedz.json();
}
```

## Ekosystem

- **React, Vue, Angular** — frameworki UI
- **Node.js** — backend i narzędzia
- **Electron** — aplikacje desktopowe
- **React Native** — aplikacje mobilne

## Cechy charakterystyczne

- Typowanie dynamiczne
- Prototypowe dziedziczenie
- Zdarzeniowy model programowania
- Jednowątkowy z pętlą zdarzeń (event loop)

## TypeScript

Nadzbiór JavaScriptu dodający **statyczne typowanie** — poprawia jakość kodu w większych projektach.
