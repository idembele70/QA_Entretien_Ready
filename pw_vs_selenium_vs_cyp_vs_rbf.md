# Comparatif : Playwright vs Selenium vs Cypress vs Robot Framework

## Vue d'ensemble

| Critère | Playwright | Selenium | Cypress | Robot Framework |
|---|---|---|---|---|
| **Langage** | JS/TS, Python, Java, C# | JS, Python, Java, C#, Ruby | JS/TS uniquement | Keyword-driven (Python) |
| **Navigateurs** | Chromium, Firefox, WebKit | Chrome, Firefox, Edge, Safari, IE | Chrome, Firefox, Edge | Via SeleniumLib ou BrowserLib |
| **Architecture** | CDP direct / async natif | WebDriver W3C Protocol | Intégré dans le navigateur | Keyword-driven + librairies Python |
| **Vitesse** | ⚡ Très rapide | 🔶 Modérée | ⚡ Rapide | 🔶 Modérée |
| **Mobile** | Partiel (WebKit) | ✅ Oui (Appium) | ❌ Non | ✅ Oui (AppiumLibrary) |
| **API Testing** | ✅ Natif | ❌ Non | ✅ cy.request() | ✅ RequestsLibrary |
| **Mock réseau** | ✅ route/intercept | ❌ Limité | ✅ cy.intercept() | 🔶 Via lib externe |
| **RPA** | ❌ Non | ❌ Non | ❌ Non | ✅ Oui (Robocorp) |
| **Accessible non-dev** | ❌ Dev only | ❌ Dev only | 🔶 Dev-friendly | ✅ Syntaxe naturelle |
| **Licence** | Apache 2.0 | Apache 2.0 | MIT | Apache 2.0 |

---

## Playwright

**Points forts**
- API unifiée multi-navigateurs (Chromium, Firefox, WebKit/Safari)
- Async natif, exécution parallèle out-of-the-box
- Trace Viewer : vidéo, screenshot, network, console — tout dans un fichier `.zip`
- Mock réseau avec `page.route()`, intercept de requêtes
- Tests API intégrés avec `request` context
- Codegen : génère du code à partir de vos clics

**Points faibles**
- Pas de test mobile natif (WebKit ≠ Safari réel)
- Plus verbeux que Cypress pour les assertions simples
- Moins de ressources communautaires que Selenium

**Quand l'utiliser ?**
> Applications modernes, projets greenfield, besoin de cross-browser rapide, CI/CD Cloud.
```python
# Exemple Python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://example.com")
    page.locator("text=More information").click()
    assert page.title() == "IANA — Example Domains"
    browser.close()
```

---

## Selenium

**Points forts**
- Framework le plus mature (depuis 2004), très large écosystème
- Supporte TOUS les navigateurs, y compris IE et Safari réel
- Intégration Appium pour mobile natif
- Selenium Grid pour parallélisation à grande échelle
- Compatible avec tous les langages et tous les CI

**Points faibles**
- Protocole WebDriver = plus lent que CDP
- Flaky tests fréquents sans gestion des attentes
- Setup complexe (drivers, Grid, configuration)
- Pas de mock réseau ni d'API testing natif

**Quand l'utiliser ?**
> Legacy projects, besoin IE/Safari, mobile avec Appium, grande entreprise avec Grid existant.
```java
// Exemple Java
WebDriver driver = new ChromeDriver();
driver.get("https://example.com");
WebElement element = driver.findElement(By.linkText("More information"));
element.click();
driver.quit();
```

---

## Cypress

**Points forts**
- Developer experience excellent : Time Travel, rechargement en live
- Tout-en-un : runner + assertions + mocking (aucune dépendance externe)
- `cy.intercept()` pour stubber les appels réseau
- `cy.request()` pour tester les API REST
- Vidéo et screenshots automatiques sur échec

**Points faibles**
- JavaScript/TypeScript uniquement
- Multi-onglets non supportés nativement
- Pas de WebKit/Safari natif
- La parallélisation nécessite Cypress Cloud (payant)
- Tests lents si beaucoup de cy.wait() inutiles

**Quand l'utiliser ?**
> Applications React/Vue/Angular, équipes JS full-stack, projets front-end-centric.
```javascript
// Exemple Cypress
describe('Login', () => {
  it('doit afficher un message d erreur', () => {
    cy.visit('/login')
    cy.get('[data-cy=username]').type('wronguser')
    cy.get('[data-cy=password]').type('wrongpass')
    cy.get('[data-cy=submit]').click()
    cy.get('.error-message').should('be.visible')
  })
})
```

---

## Robot Framework

**Points forts**
- Syntaxe keyword-driven lisible par non-développeurs (PO, QA, métier)
- Écosystème de librairies très riche : SeleniumLibrary, RequestsLibrary, DatabaseLibrary, AppiumLibrary
- Reports HTML très détaillés (`log.html`, `report.html`)
- Extensible en Python (custom keywords)
- Seul framework adapté au **RPA** (Robocorp, RPA Framework)
- Supporte BDD avec `Given/When/Then`

**Points faibles**
- Moins de vitesse que Playwright/Cypress
- Parallélisation via `pabot` (outil externe)
- Moins adapté aux projets 100% JS
- Debugging plus complexe que Cypress

**Quand l'utiliser ?**
> Tests d'acceptation avec parties prenantes non-tech, RPA, projets Python, intégrations multi-systèmes.
```robot
*** Test Cases ***
Login avec mauvais identifiants
    [Tags]    auth    smoke
    Open Browser    https://example.com/login    chromium
    Input Text      id=username    wronguser
    Input Text      id=password    wrongpass
    Click Button    id=submit
    Element Should Be Visible    class=error-message
    [Teardown]    Close Browser
```

---

## Questions d'entretien type

**Q : Quelle est la différence fondamentale entre Selenium et Playwright ?**
> Selenium utilise le protocole WebDriver (JSON Wire / W3C) : chaque commande transite par un driver externe. Playwright communique directement via le Chrome DevTools Protocol (CDP), ce qui le rend plus rapide, plus stable, et lui permet de contrôler le réseau, les permissions et les contextes navigateur nativement.

**Q : Pourquoi Cypress ne supporte-t-il pas les multi-onglets ?**
> Cypress s'exécute directement dans le navigateur, dans la même boucle d'événements que l'application testée. Cela lui confère un accès direct au DOM et une vitesse élevée, mais l'empêche de contrôler d'autres onglets ou fenêtres qui seraient en dehors de son contexte d'exécution.

**Q : Quand choisiriez-vous Robot Framework plutôt que Playwright ?**
> Lorsque les parties prenantes non-techniques (PO, testeurs QA sans background dev) doivent lire, maintenir ou écrire des tests. La syntaxe keyword-driven de Robot Framework est bien plus accessible. C'est aussi le seul choix si le projet inclut du RPA (automatisation de processus métier).

**Q : Comment gérez-vous les tests flaky dans Selenium ?**
> En remplaçant les `time.sleep()` par des `WebDriverWait` avec `expected_conditions`, en ajoutant des retry via `pytest-rerunfailures` ou `@flaky`, et en s'assurant que chaque test est indépendant (pas de dépendances inter-tests). Playwright réduit nativement ce problème via son auto-wait.

**Q : Playwright vs Cypress : lequel choisir pour un projet greenfield ?**
> Playwright si l'équipe est multi-langage, si le cross-browser (Safari) est critique, ou si on a besoin de parallélisation native sans coût. Cypress si l'équipe est 100% JS/TS et veut une DX optimale avec Time Travel et un setup ultra-simple.

---

## Résumé des cas d'usage

| Besoin | Outil recommandé |
|---|---|
| Cross-browser rapide + Safari | Playwright |
| Legacy + IE + Mobile natif | Selenium |
| DX optimale + équipe JS | Cypress |
| Non-devs + RPA + keyword-driven | Robot Framework |
| Tests API purs | Playwright ou Cypress |
| Grande parallélisation en cloud | Selenium Grid ou Playwright |
