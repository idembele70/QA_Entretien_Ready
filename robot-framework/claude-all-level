Voici un guide d'entretien complet sur Robot Framework, prêt à copier-coller :

---

# 🤖 Guide d'entretien — Robot Framework

## Niveau Junior

### Fondamentaux

**Q : Qu'est-ce que Robot Framework et à quoi sert-il ?**
> Robot Framework est un framework d'automatisation de tests open source basé sur Python. Il utilise une syntaxe de mots-clés (keyword-driven) lisible par tous, ce qui le rend accessible aux non-développeurs. Il est principalement utilisé pour les tests d'acceptation, les tests end-to-end et le RPA (Robotic Process Automation).

**Q : Quelle est la structure d'un fichier `.robot` ?**
> Un fichier `.robot` est divisé en sections :
> - `*** Settings ***` — imports de librairies, ressources, setup/teardown
> - `*** Variables ***` — déclaration des variables globales
> - `*** Test Cases ***` — définition des cas de test
> - `*** Keywords ***` — définition de mots-clés personnalisés

**Q : Quels types de variables existe-t-il dans Robot Framework ?**
> - `${SCALAR}` — variable scalaire (string, int, objet)
> - `@{LIST}` — variable liste
> - `&{DICT}` — variable dictionnaire
> - `${ENV_VAR}` — variable d'environnement via `%{NOM}`

**Q : Comment lancer une suite de tests en ligne de commande ?**
```bash
robot tests/
robot --include smoke tests/
robot --outputdir results/ tests/
robot -v ENV:prod tests/
```

---

## Niveau Intermédiaire

### Architecture & Bonnes pratiques

**Q : Quelle est la différence entre `Setup`, `Teardown` et `Suite Setup` ?**
> - `Test Setup` / `Test Teardown` : exécuté avant/après **chaque test**
> - `Suite Setup` / `Suite Teardown` : exécuté une seule fois avant/après **toute la suite**
> - `[Setup]` / `[Teardown]` en inline : surcharge au niveau du test individuel

**Q : Comment structurer un projet Robot Framework proprement ?**
```
projet/
├── tests/
│   ├── smoke/
│   └── regression/
├── resources/
│   ├── keywords/
│   ├── variables/
│   └── pages/         ← Page Object Model
├── libraries/         ← librairies Python custom
└── results/
```

**Q : Qu'est-ce que le Page Object Model (POM) dans Robot Framework ?**
> Le POM consiste à créer un fichier `.robot` ou `.resource` par page de l'application. Chaque fichier expose des mots-clés de haut niveau (`Login User`, `Search Product`) qui encapsulent les locators et les interactions. Cela améliore la maintenabilité et réduit la duplication.

**Q : Comment gérer les tags et filtrer les tests ?**
```robot
*** Test Cases ***
Test Login
    [Tags]    smoke    auth    regression
    ...

# Lancement filtré
robot --include smoke tests/
robot --exclude wip tests/
robot --include smokeANDauth tests/
```

**Q : Quelle est la différence entre une librairie intégrée et une librairie externe ?**
> - **Built-in** : `BuiltIn`, `Collections`, `String`, `OperatingSystem`, `DateTime` — disponibles sans installation
> - **Externes** : `SeleniumLibrary`, `RequestsLibrary`, `DatabaseLibrary` — à installer via `pip`
> - **Custom** : classes ou fonctions Python que l'on écrit soi-même

---

## Niveau Avancé

### Librairies & Intégrations

**Q : Comment créer une librairie Python custom ?**
```python
# libraries/MyLibrary.py
class MyLibrary:
    ROBOT_LIBRARY_SCOPE = 'GLOBAL'  # ou TEST, SUITE

    def my_keyword(self, arg1, arg2):
        """Documentation visible dans Robot."""
        return f"{arg1} - {arg2}"
```
```robot
*** Settings ***
Library    libraries/MyLibrary.py
```

**Q : Comment fonctionne `SeleniumLibrary` et quels sont ses avantages ?**
> `SeleniumLibrary` est le wrapper Robot Framework autour de Selenium WebDriver. Elle fournit des mots-clés comme `Open Browser`, `Click Element`, `Input Text`, `Wait Until Element Is Visible`. Elle supporte Chrome, Firefox, Edge, et s'intègre avec des outils comme BrowserMob Proxy ou Allure Reports.

**Q : Quelle est la différence entre `SeleniumLibrary` et `Browser Library` (Playwright) ?**
| Critère | SeleniumLibrary | Browser Library |
|---|---|---|
| Moteur | Selenium/WebDriver | Playwright |
| Vitesse | Modérée | Rapide |
| Async natif | Non | Oui |
| Multi-navigateur | Oui | Oui (Chromium, FF, WebKit) |
| Réseau / Mock | Limité | Natif |

**Q : Comment gérer les attentes (waits) efficacement ?**
```robot
# ❌ Mauvais
Sleep    3s

# ✅ Bon
Wait Until Element Is Visible    ${LOCATOR}    timeout=10s
Wait Until Keyword Succeeds    3x    1s    My Keyword
```

**Q : Comment intégrer Robot Framework dans une pipeline CI/CD ?**
```yaml
# Exemple GitHub Actions
- name: Run Robot Tests
  run: |
    pip install robotframework robotframework-seleniumlibrary
    robot --outputdir results/ --xunit xunit.xml tests/

- name: Publish results
  uses: actions/upload-artifact@v3
  with:
    name: robot-results
    path: results/
```

---

## Questions Comportementales / Situationnelles

**Q : Comment débuggez-vous un test Robot Framework qui échoue de manière intermittente (flaky test) ?**
> Approche : analyser les logs `log.html` et `report.html` générés, vérifier les timeouts, ajouter des screenshots sur échec via `Register Keyword To Run On Failure`, utiliser `Run Keyword And Continue On Failure` pour isoler le problème, et vérifier la stabilité de l'environnement de test.

**Q : Comment organiseriez-vous les tests d'une application avec 50+ pages ?**
> Utiliser le POM avec un fichier resource par page, regrouper par domaine fonctionnel, centraliser les variables dans un fichier `variables/env.yaml`, et utiliser des tags pour la priorisation. Mettre en place une suite `smoke` (< 5 min) et une suite `regression` complète.

**Q : Avez-vous déjà utilisé Robot Framework pour du RPA ? Quelle différence avec les tests ?**
> En RPA, on utilise des librairies comme `RPA Framework` (Robocorp), `RPA.Browser`, `RPA.Excel`. La logique change : on gère des exceptions métier, on travaille avec des fichiers, des emails, des APIs. Les tests valident un comportement ; le RPA automatise un processus métier avec gestion d'état et reprise sur erreur.

---

## Ressources utiles à mentionner

- **Docs officielles** : [robotframework.org](https://robotframework.org)
- **SeleniumLibrary** : [github.com/robotframework/SeleniumLibrary](https://github.com/robotframework/SeleniumLibrary)
- **Browser Library** (Playwright) : [robotframework-browser.org](https://robotframework-browser.org)
- **Robocorp / RPA Framework** : [robocorp.com](https://robocorp.com)
- **RIDE** : IDE dédié Robot Framework

---

*Entretien généré le 30/03/2026 — Niveaux : Junior / Intermédiaire / Senior*
