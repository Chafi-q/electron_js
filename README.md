


Ce guide vous expliquera le processus de création d'une application Hello World minimaliste dans Electron.

À la fin de ce tutoriel, votre application ouvrira une fenêtre de navigateur affichant une page web contenant des informations sur les versions de Chromium, Node.js et Electron en cours d'exécution.


## Prérequis


Pour utiliser Electron, vous devez installer [Node.js][node-download]. Nous vous recommandons d'utiliser la dernière version `LTS` disponible.


> Veuillez installer Node.js en utilisant les installateurs précompilés pour votre plateforme.
> Vous pourriez rencontrer des problèmes d'incompatibilité avec différents outils de développement autrement.


Pour vérifier que Node.js a été installé correctement, tapez les commandes suivantes dans votre client terminal :


```sh npm2yarn
node -v
npm -v
```


Les commandes devraient afficher les versions de Node.js et npm en conséquence.


**Remarque :** Étant donné qu'Electron intègre Node.js dans son binaire, la version de Node.js exécutant votre code n'est pas liée à la version exécutée sur votre système.


[node-download]: https://nodejs.org/en/download/


## Créez votre application


### Générer le projet


Les applications Electron suivent la même structure générale que les autres projets Node.js.
Commencez par créer un dossier et initialiser un package npm.


```sh npm2yarn
mkdir my-electron-app && cd my-electron-app
npm init
```


La commande interactive `init` vous demandera de remplir certains champs dans votre configuration.
Il y a quelques règles à suivre pour les besoins de ce tutoriel :


* Le `point d'entrée` doit être `main.js`.
* `author` et `description` peuvent avoir n'importe quelle valeur, mais sont nécessaires pour
[la création de paquets d'application](#package-and-distribute-your-application).


Votre fichier `package.json` devrait ressembler à ceci :


```json
{
  "name": "simple-application-electron",
  "version": "1.0.0",
  "description": "Hello world!",
  "main": "main.js",
  "author": "Jane Doe",
  "license": "MIT"
}


Ensuite, installez le package `electron` dans les `devDependencies` de votre application.


```sh
npm2yarn
npm install --save-dev electron
```


> Remarque : Si vous rencontrez des problèmes lors de l'installation d'Electron, veuillez
> consulter le guide [Installation Avancée][advanced-installation].


Enfin, vous devez pouvoir exécuter Electron. Dans le champ [`scripts`][package-scripts] de votre configuration `package.json`, ajoutez une commande `start` comme suit :


```json
{
  "scripts": {
    "start": "electron ."
  }
}
```


Cette commande `start` vous permettra d'ouvrir votre application en mode développement.


```sh npm2yarn
npm start
```


> Remarque : Ce script indique à Electron de s'exécuter dans le dossier racine de votre projet. À ce stade,
> votre application renverra immédiatement une erreur vous indiquant qu'elle ne trouve pas d'application à exécuter.



### Exécuter le processus principal


Le point d'entrée de toute application Electron est son script `main`. Ce script contrôle le **processus principal**, qui s'exécute dans un environnement Node.js complet et est responsable de la gestion du cycle de vie de votre application, de l'affichage des interfaces natives, de l'exécution d'opérations privilégiées et de la gestion des processus de rendu. (more on that later).


Pendant l'exécution, Electron recherchera ce script dans le champ [`main`][package-json-main] de la configuration `package.json` de l'application, que vous auriez dû configurer lors de l'étape de [génération de l'application](#scaffold-the-project).


Pour initialiser le script `main`, créez un fichier vide nommé `main.js` dans le dossier racine de votre projet.


> Remarque : Si vous exécutez à nouveau le script `start` à ce stade, votre application ne lancera plus
> d'erreurs ! Cependant, cela ne fera encore rien car nous n'avons pas ajouté de code dans `main.js`.

Pour tester rapidement si votre fichier `main.js` est correctement connecté, vous pouvez ajouter une ligne de code simple comme celle-ci :

```js
console.log('Script principal initialisé !');
```



### Créer une page web


Avant de pouvoir créer une fenêtre pour notre application, nous devons créer le contenu qui y sera chargé. Dans Electron, chaque fenêtre affiche du contenu web qui peut être chargé à partir d'un fichier HTML local ou d'une URL distante.


Pour ce tutoriel, vous allez faire la première option. Créez un fichier `index.html` dans le dossier racine de votre projet :


```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/fr/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Bonjour le monde !</title></title>
  </head>
  <body>
    <h1>Bonjour le monde !</h1>
    Nous utilisons Node.js <span id="node-version"></span>,
    Chromium <span id="chrome-version"></span>,
    et Electron <span id="electron-version"></span>.
  </body>
</html>
```


> Remarque : En regardant ce document HTML, vous pouvez observer que les numéros de version sont
> absents du texte du corps. Nous les insérerons manuellement plus tard en utilisant JavaScript.

### Ouvrir votre page web dans une fenêtre de navigateur


Maintenant que vous avez une page web, chargez-la dans une fenêtre d'application. Pour ce faire, vous aurez besoin de deux modules Electron :


* Le module [`app`][app], qui contrôle le cycle de vie des événements de votre application.
* Le module [`BrowserWindow`][browser-window], qui crée et gère les fenêtres de l'application.


Comme le processus principal exécute Node.js, vous pouvez les importer en tant que modules [CommonJS][commonjs] en haut de votre fichier `main.js` :


```js
const { app, BrowserWindow } = require('electron')
```


Ensuite, ajoutez une fonction `createWindow()` qui charge `index.html` dans une nouvelle instance de `BrowserWindow`.


```js
const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })


  win.loadFile('index.html')
}
```


Ensuite, appelez cette fonction `createWindow()` pour ouvrir votre fenêtre.


Dans Electron, les fenêtres de navigateur ne peuvent être créées qu'après que l'événement [`ready`][app-ready] du module `app` ait été déclenché. Vous pouvez attendre cet événement en utilisant l'API [`app.whenReady()`][app-when-ready]. Appelez `createWindow()` après que `whenReady()` ait résolu sa promesse.


```js @ts-type={createWindow:()=>void}
app.lorsquePrêt().alors(() => {
  créerFenêtre()
})
```


> Remarque : À ce stade, votre application Electron devrait ouvrir avec succès une fenêtre affichant votre page web !


[app] : ../api/app.md
[fenêtre-du-navigateur]: ../api/browser-window.md
[commonjs]: https://nodejs.org/docs/latest/api/modules.html#modules_modules_commonjs_modules
[app-ready]: ../api/app.md#event-ready
[app-when-ready]: ../api/app.md#appwhenready


### Gérer le cycle de vie de votre fenêtre


Bien que vous puissiez maintenant ouvrir une fenêtre de navigateur, vous aurez besoin de quelques lignes de code supplémentaires pour qu'elle se sente plus native à chaque plateforme. Les fenêtres d'application se comportent différemment sur chaque système d'exploitation, et Electron laisse la responsabilité aux développeurs de mettre en œuvre ces conventions dans leur application.


En général, vous pouvez utiliser l'attribut [`platform`][node-platform] de l'objet global `process` pour exécuter du code spécifiquement pour certains systèmes d'exploitation.


#### Quitter l'application lorsque toutes les fenêtres sont fermées (Windows & Linux)


Sur Windows et Linux, fermer toutes les fenêtres quitte généralement complètement une application.


Pour implémenter cela, écoutez l'événement [`'window-all-closed'`] du module `app`.événement [`window-all-closed`], et appelez [`app.quit()`][app-quit] si l'utilisateur n'est pas sur macOS (`darwin`).


```js
app.on('window-all-closed', () => {
  si (process.platform !== 'darwin') app.quit()
})
```
#### Ouvrez une fenêtre si aucune n'est ouverte (macOS)


Alors que les applications Linux et Windows se ferment lorsqu'elles n'ont aucune fenêtre ouverte, les applications macOS continuent généralement de fonctionner même sans aucune fenêtre ouverte, et activer l'application lorsqu'aucune fenêtre n'est disponible devrait en ouvrir une nouvelle.


Pour implémenter cette fonctionnalité, écoutez l'événement [`activate`][activate] du module `app`, et appelez votre méthode `createWindow()` existante si aucune fenêtre de navigateur n'est ouverte.


Parce que les fenêtres ne peuvent pas être créées avant l'événement `ready`, vous ne devez écouter les événements `activate` qu'après que votre application soit initialisée. Faites cela en attachant votre écouteur d'événements depuis votre callback `whenReady()` existant.




```js @ts-type={createWindow:()=>void}
app.lorsquePrêt().alors(() => {
  créerFenêtre()


  app.on('activer', () => {
    si (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})
```


> Remarque : À ce stade, vos contrôles de fenêtre devraient être entièrement fonctionnels !


### Accéder à Node.js depuis le rendu avec un script de préchargement


Maintenant, la dernière chose à faire est d'imprimer les numéros de version d'Electron et de ses dépendances sur votre page web.


Accéder à cette information est trivial à faire dans le processus principal via l'objet global `process` de Node. Cependant, vous ne pouvez pas simplement modifier le DOM depuis le processus principal car il n'a pas accès au contexte `document` du renderer.
Ils sont dans des processus complètement différents !


> Remarque : Si vous avez besoin d'un aperçu plus approfondi des processus Electron, consultez le document [Modèle de Processus][].


C'est ici qu'attacher un script **preload** à votre renderer devient utile.
Un script de préchargement s'exécute avant que le processus de rendu ne soit chargé, et a accès à la fois aux variables globales du rendu (par exemple, `window` et `document`) et à un environnement Node.js.


Créez un nouveau script nommé `preload.js` comme suit :


```js
window.addEventListener('DOMContentLoaded', () => {
  const replaceText = (sélecteur, texte) => {
    const element = document.getElementById(selector)
    if (element) element.
}innerText = texte
  }


  pour (const dépendance de ['chrome', 'node', 'electron']) {
    remplacerTexte(`${dépendance}-version`, process.versions[dépendance])
  }
})
```


Le code ci-dessus accède à l'objet `process.versions` de Node.js et exécute une fonction d'aide de base `replaceText` pour insérer les numéros de version dans le document HTML.


Pour attacher ce script à votre processus de rendu, passez le chemin de votre script de préchargement à l'option `webPreferences.preload` dans votre constructeur `BrowserWindow` existant.


```js
const { app, BrowserWindow } = require('electron')
// inclure le module 'path' de Node.js en haut de votre fichier
const path = require('node:path')


// modifiez votre fonction createWindow() existante
const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })


  win.loadFile('index.html')
}
// ...
```


Il y a deux concepts Node.js qui sont utilisés ici :


* La chaîne [`__dirname`][dirname] pointe vers le chemin du script actuellement en cours d'exécution (dans ce cas, le dossier racine de votre projet).
* L'API [`path.join`][path-join] assemble plusieurs segments de chemin, créant une chaîne de chemin combinée qui fonctionne sur toutes les plateformes.


Nous utilisons un chemin relatif au fichier JavaScript actuellement en cours d'exécution afin que votre chemin relatif fonctionne à la fois en mode développement et en mode empaqueté.



### Bonus : Ajoutez des fonctionnalités à vos contenus web


À ce stade, vous vous demandez peut-être comment ajouter plus de fonctionnalités à votre application.


Pour toute interaction avec vos contenus web, vous devez ajouter des scripts à votre processus de rendu. Parce que le renderer s'exécute dans un environnement web normal, vous pouvez ajouter une balise `<script>` juste avant la balise de fermeture `</body>` de votre fichier `index.html` pour inclure tous les scripts arbitraires que vous souhaitez :


```html
<script src="./renderer.js"></script>
Le code contenu dans `renderer.js` peut alors utiliser les mêmes APIs JavaScript et outils que vous utilisez pour le développement front-end typique, comme utiliser [`webpack`][webpack] pour regrouper et minifier votre code ou [React][react] pour gérer vos interfaces utilisateur.


Le code contenu dans `renderer.js` peut alors utiliser les mêmes API JavaScript et outils que vous utilisez pour le développement front-end typique, comme l'utilisation de [`webpack`][webpack] pour regrouper et minifier votre code ou [React][react] pour gérer vos interfaces utilisateur.


[webpack]: https://webpack.js.org
[react]: https://reactjs.org


### Récapitulatif


Après avoir suivi les étapes ci-dessus, vous devriez avoir une application Electron entièrement fonctionnelle qui ressemble à ceci :


![Application Electron la plus simple](../images/simplest-electron-app.png)


<!--TODO(erickzhao): Supprimer les blocs de code individuels pour le site web statique -->
Le code complet est disponible ci-dessous :


```js
// main.js


// Modules pour contrôler la vie de l'application et créer une fenêtre de navigateur native
const { app, BrowserWindow } = require('electron')
const path = require('node:path')


const createWindow = () => // Créer la fenêtre du navigateur.
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })


  // et charger le index.html de l'application.
  mainWindow.loadFile('index.html')


  // Ouvrir les DevTools.
  // mainWindow.webContents.openDevTools()
}


// Cette méthode sera appelée lorsque Electron aura terminé
// l'initialisation et sera prêt à créer des fenêtres de navigateur.
// Certaines API ne peuvent être utilisées qu'après cet événement.
app.whenReady().then(() => {
  createWindow()


  app.on('activer', () => {
    // Sur macOS, il est courant de recréer une fenêtre dans l'application lorsque l'icône du dock est cliquée et qu'il n'y a pas d'autres fenêtres ouvertes.
    si (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})


// Quitter lorsque toutes les fenêtres sont fermées, sauf sur macOS. Là-bas, il est courant
// que les applications et leur barre de menu restent actives jusqu'à ce que l'utilisateur quitte
// explicitement avec Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})


// Dans ce fichier, vous pouvez inclure le reste du code du processus principal spécifique de votre application. Vous pouvez également les mettre dans des fichiers séparés et les inclure ici.
```js
// preload.js 
Texte à traduire : ```


```js
// preload.js
```


// Toutes les API Node.js sont disponibles dans le processus de préchargement.
// Il a le même bac à sable qu'une extension Chrome.
window.addEventListener('DOMContentLoaded', () => {
  const replaceText = (sélecteur, texte) => {
    const element = document.getElementById(selector)
    if (element) element.innerText = text
  }


  pour (const dépendance de ['chrome', 'node', 'electron']) {
    remplacerTexte(`${dépendance}-version`, process.versions[dépendance])
  }
})
```


```html
<!--index.html-->
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/fr/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Bonjour le monde !</titre>
  </tête>
  <corps>
    <h1>Bonjour le monde !</h1>
    Nous utilisons Node.js <span id="node-version"></span>,
    Chromium <span id="chrome-version"></span>,
    et Electron <span id="electron-version"></span>.


    <!-- Vous pouvez également exiger d'autres fichiers pour s'exécuter dans ce processus -->
    <script src="./renderer.js"></script>
  </body>
</html>
```


```fiddle docs/fiddles/quick-start
```


Pour résumer toutes les étapes que nous avons effectuées :


* Nous avons initialisé une application Node.js et ajouté Electron comme dépendance.
* Nous avons créé un script `main.js` qui exécute notre processus principal, qui contrôle notre application et s'exécute dans un environnement Node.js. Dans ce script, nous avons utilisé les modules `app` et `BrowserWindow` d'Electron pour créer une fenêtre de navigateur qui affiche du contenu web dans un processus séparé (le renderer). Afin d'accéder à certaines fonctionnalités de Node.js dans le renderer, nous avons attaché un script de préchargement à notre constructeur `BrowserWindow`.

## Emballez et distribuez votre application


La manière la plus rapide de distribuer votre application nouvellement créée est d'utiliser [Electron Forge](https://www.electronforge.io).


:::info


Pour créer un paquet RPM pour Linux, vous devrez [installer ses dépendances système requises](https://www.electronforge.io/config/makers/rpm).


:::


1. Ajoutez une description à votre fichier `package.json`, sinon rpmbuild échouera. Les descriptions vides ne sont pas valides.
2. Ajoutez Electron Forge comme dépendance de développement de votre application, et utilisez sa commande `import` pour configurer l'ossature de Forge :


   ```sh npm2yarn
   npm install --save-dev @electron-forge/cli
   npx electron-forge import
```


   ✔ Vérification de votre système
   ✔ Initialisation du dépôt Git
   ✔ Écriture du fichier package.json modifié
   ✔ Installation des dépendances
   ✔ Écriture du fichier package.json modifié
   ✔ Correction du fichier .gitignore


   Nous avons TENTÉ de convertir votre application dans un format que electron-forge comprend.


   Merci d'utiliser "electron-forge" !!!
   ```


3. Créez un distribuable en utilisant la commande `make` de Forge :


   ```sh npm2yarn
   npm run make


   > my-electron-app@1.0.0 make /my-electron-app
   > electron-forge make


   ✔ Vérification de votre système
   ✔ Résolution de la configuration Forge
   Nous devons empaqueter votre application avant de pouvoir la créer.
   ✔ Préparation de l'emballage Application pour l'architecture : x64
   ✔ Préparation des dépendances natives
   ✔ Emballage de l'application
   Création pour les cibles suivantes : zip
   ✔ Création pour la cible : zip - Sur la plateforme : darwin - Pour l'architecture : x64
   ```


   Electron Forge crée le dossier `out` où votre package sera situé :


   ```plain
   // Exemple pour macOS
   out/
   ├── out/make/zip/darwin/x64/my-electron-app-darwin-x64-1.0.0.zip
   ├── ...
   └── out/my-electron-app-darwin-x64/my-electron-app.app/Contents/MacOS/my-electron-app
   ```

