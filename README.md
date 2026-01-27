# Web-Scraping mit Crawlee

[![Promo](https://github.com/bright-data-de/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.de/) 

Erfahren Sie, wie Sie Crawlee für effizientes [Web-Scraping mit Node.js](https://brightdata.de/blog/how-tos/web-scraping-with-node-js) verwenden:

- [Grundlegendes Web-Scraping mit Crawlee](#basic-web-scraping-with-crawlee)
- [Proxy-Rotation mit Crawlee](#proxy-rotation-with-crawlee)
- [Sitzungsverwaltung mit Crawlee](#sessions-management-with-crawlee)
- [Umgang mit dynamischen Inhalten mit Crawlee](#dynamic-content-handling-with-crawlee)

## Prerequisites

Bevor Sie beginnen, stellen Sie sicher, dass Sie die folgenden Voraussetzungen installiert haben:

* **[Node.js](https://nodejs.org/).**
* **[npm](https://www.npmjs.com/):** Dies ist in der Regel in Node.js enthalten. Sie können die Installation überprüfen, indem Sie `node -v` oder `npm -v` in Ihrem Terminal ausführen.
* **Ein Code-Editor Ihrer Wahl:** Dieses Tutorial verwendet [Visual Studio Code](https://code.visualstudio.com/).

## Basic Web Scraping with Crawlee

Beginnen wir damit, die Website [Books to Scrape](https://books.toscrape.com/) zu scrapen.

Öffnen Sie Ihr Terminal oder Ihre Shell und initialisieren Sie ein Node.js-Projekt:

```bash
mkdir crawlee-tutorial
cd crawlee-tutorial
npm init -y
```

Installieren Sie die Crawlee-Bibliothek:

```bash
npm install crawlee
```

Um Daten effektiv zu scrapen, prüfen Sie die HTML-Struktur der Ziel-Website. Öffnen Sie die Website in Ihrem Browser, klicken Sie mit der rechten Maustaste irgendwo auf die Seite und wählen Sie **Inspect** oder **Inspect Element** in den **Developer Tools**.

![Inspect HTML element](https://github.com/bright-data-de/crawlee-web-scraping/blob/main/images/Inspect-HTML-element-1024x540.png)

Der Tab **Elements** in den **Developer Tools** zeigt das HTML-Layout der Seite. In diesem Beispiel:  

- Jedes Buch befindet sich innerhalb eines `article`-Tags mit der Klasse `product_pod`.  
- Der Buchtitel befindet sich in einem `h3`-Tag, wobei der eigentliche Titel im `title`-Attribut des verschachtelten `a`-Tags gespeichert ist.  
- Der Buchpreis befindet sich innerhalb eines `p`-Tags mit der Klasse `price_color`.  

![Inspect the HTML elements on the Books to Scrape website](https://github.com/bright-data-de/crawlee-web-scraping/blob/main/images/Inspect-the-HTML-elements-on-the-Books-to-Scrape-website-1024x522.png)

Erstellen Sie im Root-Verzeichnis Ihres Projekts eine Datei mit dem Namen `scrape.js` und fügen Sie den folgenden Code hinzu:

```js
const { CheerioCrawler } = require('crawlee');

const crawler = new CheerioCrawler({
    async requestHandler({ request, $ }) {
        const books = [];
        $('article.product_pod').each((index, element) => {
            const title = $(element).find('h3 a').attr('title');
            const price = $(element).find('.price_color').text();
            books.push({ title, price });
        });
        console.log(books);
    },
});

crawler.run(['https://books.toscrape.com/']);
```

Dieser Code verwendet `CheerioCrawler` aus `crawlee`, um Buchtitel und Preise von `https://books.toscrape.com/` zu extrahieren. Er ruft das HTML ab, wählt `<article class="product_pod">`-Elemente mit einer jQuery-ähnlichen Syntax aus und protokolliert die Ergebnisse in der Konsole.

Nachdem Sie den Code zu Ihrer Datei `scrape.js` hinzugefügt haben, führen Sie ihn mit folgendem Befehl aus:

```bash
node scrape.js
```

Ein Array mit Buchtiteln und Preisen sollte in Ihrem Terminal ausgegeben werden:

```
…output omitted…
  {
    title: 'The Boys in the Boat: Nine Americans and Their Epic Quest for Gold at the 1936 Berlin Olympics',
    price: '£22.60'
  },
  { title: 'The Black Maria', price: '£52.15' },
  {
    title: 'Starving Hearts (Triangular Trade Trilogy, #1)',
    price: '£13.99'
  },
  { title: "Shakespeare's Sonnets", price: '£20.66' },
  { title: 'Set Me Free', price: '£17.46' },
  {
    title: "Scott Pilgrim's Precious Little Life (Scott Pilgrim #1)",
    price: '£52.29'
  },
…output omitted…
```

## Proxy Rotation with Crawlee

Ein Proxy fungiert als Zwischenstation zwischen Ihrem Computer und dem Internet, leitet Ihre Web-Anfragen weiter und maskiert dabei Ihre IP-Adresse. Dies hilft dabei, Ratenbegrenzungen und IP-Sperren zu verhindern.

Crawlee vereinfacht die Proxy-Implementierung durch integrierte Verarbeitung von Wiederholungsversuchen, Fehlern und rotierenden Proxies.

Als Nächstes richten Sie einen Proxy ein, beschaffen eine gültige Proxy-Adresse und überprüfen, dass Ihre Anfragen darüber geroutet werden.

Da kostenlose Proxies oft langsam, unsicher und unzuverlässig für sensible Web-Aufgaben sind, sollten Sie die Nutzung eines vertrauenswürdigen Dienstes wie Bright Data in Betracht ziehen, der sichere, stabile und zuverlässige Proxies bereitstellt. Außerdem bietet der Dienst kostenlose Testversionen, damit Sie den Service testen können, bevor Sie sich festlegen. 

Um Bright Data zu verwenden, klicken Sie auf der [Startseite](https://brightdata.de/) auf die Schaltfläche **Start free trial** und füllen Sie die erforderlichen Informationen aus, um ein Konto zu erstellen.

Sobald Ihr Konto erstellt ist, melden Sie sich im Bright Data-Dashboard an, navigieren Sie zu **Proxies & Scraping Infrastructure** und fügen Sie einen neuen Proxy hinzu, indem Sie **[Residential Proxies](/proxy-types/residential-proxies)** auswählen:

![Add a residential proxy](https://github.com/bright-data-de/crawlee-web-scraping/blob/main/images/Add-a-residential-proxy-1024x574.png)

Behalten Sie die Standardeinstellungen bei und schließen Sie die Erstellung Ihres Residential Proxy ab, indem Sie auf **Add** klicken.

Wenn Sie aufgefordert werden, ein Zertifikat zu installieren, können Sie **Proceed without certificate** auswählen. Für Produktion und echte Anwendungsfälle sollten Sie jedoch das Zertifikat einrichten, um Missbrauch zu verhindern, falls Ihre Proxy-Informationen jemals offengelegt werden.

Notieren Sie nach der Erstellung die Proxy-Anmeldedaten, einschließlich Host, Port, Username und Password. Sie benötigen diese im nächsten Schritt:

![Bright Data proxy credentials](https://github.com/bright-data-de/crawlee-web-scraping/blob/main/images/Bright-Data-proxy-credentials-1024x557.png)

Führen Sie im Root-Verzeichnis Ihres Projekts den folgenden Befehl aus, um die Bibliothek [axios](https://www.npmjs.com/package/axios) zu installieren:

```bash
npm install axios
```

Die Bibliothek `axios` wird verwendet, um eine GET-Anfrage an `http://lumtest.com/myip.json` zu senden, die Details über den verwendeten Proxy zurückgibt.

Um dies zu implementieren, erstellen Sie im Root-Verzeichnis Ihres Projekts eine Datei namens `scrapeWithProxy.js` und fügen Sie den folgenden Code hinzu:

```js
const { CheerioCrawler } = require("crawlee");
const { ProxyConfiguration } = require("crawlee");
const axios = require("axios");

const proxyConfiguration = new ProxyConfiguration({
  proxyUrls: ["http://USERNAME:PASSWORD@HOST:PORT"],
});

const crawler = new CheerioCrawler({
  proxyConfiguration,
  async requestHandler({ request, $, response, proxies }) {
    // Make a GET request to the proxy information URL
    try {
      const proxyInfo = await axios.get("http://lumtest.com/myip.json", {
        proxy: {
          host: "HOST",
          port: PORT,
          auth: {
            username: "USERNAME",
            password: "PASSWORD",
          },
        },
      });
      console.log("Proxy Information:", proxyInfo.data);
    } catch (error) {
      console.error("Error fetching proxy information:", error.message);
    }

    const books = [];
    $("article.product_pod").each((index, element) => {
      const title = $(element).find("h3 a").attr("title");
      const price = $(element).find(".price_color").text();
      books.push({ title, price });
    });
    console.log(books);
  },
});

crawler.run(["https://books.toscrape.com/"]);
```

> **Note:**
> 
> Stellen Sie sicher, dass Sie `HOST`, `PORT`, `USERNAME` und `PASSWORD` durch Ihre Zugangsdaten ersetzen.

Dieser Code verwendet `CheerioCrawler` aus `crawlee`, um Daten von `https://books.toscrape.com/` zu scrapen, während Anfragen über einen angegebenen Proxy geroutet werden.  

- Der Proxy wird mit `ProxyConfiguration` konfiguriert.  
- Eine GET-Anfrage an `http://lumtest.com/myip.json` ruft Proxy-Details ab und protokolliert sie.  
- Buchtitel und Preise werden mit Cheerio’s jQuery-ähnlicher Syntax extrahiert und in der Konsole protokolliert.  

Führen Sie den Code aus, um das Proxy-Setup zu testen und seine Funktionalität zu überprüfen:

```bash
node scrapeWithProxy.js
```

Sie sehen ähnliche Ergebnisse wie zuvor, aber diesmal werden Ihre Anfragen über Bright Data-Proxies geroutet. Sie sollten außerdem die Details des Proxys in der Konsole protokolliert sehen:

```js
Proxy Information: {
  country: 'US',
  asn: { asnum: 21928, org_name: 'T-MOBILE-AS21928' },
  geo: {
    city: 'El Paso',
    region: 'TX',
    region_name: 'Texas',
    postal_code: '79925',
    latitude: 31.7899,
    longitude: -106.3658,
    tz: 'America/Denver',
    lum_city: 'elpaso',
    lum_region: 'tx'
  }
}
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
  { title: 'Soumission', price: '£50.10' },
  { title: 'Sharp Objects', price: '£47.82' },
  { title: 'Sapiens: A Brief History of Humankind', price: '£54.23' },
  { title: 'The Requiem Red', price: '£22.65' },
…output omitted..
```

Das Ausführen des Skripts mit `node scrapingWithBrightData.js` sollte jedes Mal eine andere IP-Adresse anzeigen und bestätigen, dass Bright Data Standorte und IPs automatisch rotiert. Diese Rotation hilft, Blockierungen und IP-Sperren beim Scraping von Websites zu verhindern.

> **Note:**
> 
> In der `proxyConfiguration` hätten Sie unterschiedliche Proxy-IPs übergeben können, aber da Bright Data das für Sie übernimmt, müssen Sie die IPs nicht angeben.

## Sessions Management with Crawlee

Sitzungen helfen dabei, den Zustand über mehrere Anfragen hinweg beizubehalten, insbesondere bei Websites, die Cookies oder Login-Sitzungen verwenden.  

Um Sitzungsverwaltung zu implementieren, erstellen Sie im Root-Verzeichnis Ihres Projekts eine Datei namens `scrapeWithSessions.js` und fügen Sie den folgenden Code hinzu:

```js
const { CheerioCrawler, SessionPool } = require("crawlee");

(async () => {
  // Open a session pool
  const sessionPool = await SessionPool.open();

  // Ensure there is a session in the pool
  let session = await sessionPool.getSession();
  if (!session) {
    session = await sessionPool.createSession();
  }

  const crawler = new CheerioCrawler({
    useSessionPool: true, // Enable session pool
    async requestHandler({ request, $, response, session }) {
      // Log the session information
      console.log(`Using session: ${session.id}`);

      // Extract book data and log it (for demonstration)
      const books = [];
      $("article.product_pod").each((index, element) => {
        const title = $(element).find("h3 a").attr("title");
        const price = $(element).find(".price_color").text();
        books.push({ title, price });
      });
      console.log(books);
    },
  });

  // First run
  await crawler.run(["https://books.toscrape.com/"]);
  console.log("First run completed.");

  // Second run
  await crawler.run(["https://books.toscrape.com/"]);
  console.log("Second run completed.");
})();
```

Dieser Code verwendet `CheerioCrawler` und `SessionPool` aus `crawlee`, um Daten von `https://books.toscrape.com/` zu scrapen.  

- Ein SessionPool wird initialisiert und dem Crawler zugewiesen.  
- Der `requestHandler` protokolliert Sitzungsdetails und extrahiert Buchtitel und Preise mit Cheerio-Selektoren.  
- Das Skript führt zwei aufeinanderfolgende Scraping-Läufe aus und protokolliert jedes Mal die session ID.  

Führen Sie den Code aus, um zu überprüfen, dass unterschiedliche Sitzungen verwendet werden.

```bash
node scrapeWithSessions.js
```

Sie sollten ähnliche Ergebnisse wie zuvor sehen, aber diesmal mit der session ID für jeden Lauf:

```
Using session: session_GmKuZ2TnVX
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
…output omitted…
Using session: session_lNRxE89hXu
[
  { title: 'A Light in the Attic', price: '£51.77' },
  { title: 'Tipping the Velvet', price: '£53.74' },
…output omitted…
```

Wenn Sie den Code erneut ausführen, sollten Sie sehen, dass eine andere session ID verwendet wird.

## Dynamic Content Handling with Crawlee

Das Scraping **dynamischer Websites** (die Inhalte über JavaScript laden) kann herausfordernd sein, da Daten erst nach dem Rendern verfügbar sind.  

Um damit umzugehen, integriert sich Crawlee mit [Puppeteer](https://pptr.dev/), einem Headless-Browser, der JavaScript rendert und mit Webseiten wie ein Mensch interagiert.  

Zur Demonstration scrapen wir Inhalte von [dieser YouTube-Seite](https://www.youtube.com/watch?v=wZ6cST5pexo). **Überprüfen Sie vor dem Scraping immer die Regeln und Nutzungsbedingungen der Website.**  

Nachdem Sie die Bedingungen überprüft haben, erstellen Sie im Root-Verzeichnis Ihres Projekts eine Datei namens `scrapeDynamicContent.js` und fügen Sie den folgenden Code hinzu:

```js
const { PuppeteerCrawler } = require("crawlee");

async function scrapeYouTube() {
  const crawler = new PuppeteerCrawler({
    async requestHandler({ page, request, enqueueLinks, log }) {
      const { url } = request;
      await page.goto(url, { waitUntil: "networkidle2" });

      // Scraping first 10 comments
      const comments = await page.evaluate(() => {
        return Array.from(document.querySelectorAll("#comments #content-text"))
          .slice(0, 10)
          .map((el) => el.innerText);
      });

      log.info(`Comments: ${comments.join("\n")}`);
    },

    launchContext: {
      launchOptions: {
        headless: true,
      },
    },
  });

  // Add the URL of the YouTube video you want to scrape
  await crawler.run(["https://www.youtube.com/watch?v=wZ6cST5pexo"]);
}

scrapeYouTube();
```

Führen Sie den Code anschließend mit folgendem Befehl aus:

```bash
node scrapeDynamicContent.js
```

Dieser Code verwendet `PuppeteerCrawler` aus Crawlee, um Kommentare aus einem YouTube-Video zu scrapen.  

- Der Crawler navigiert zu einer bestimmten YouTube-Video-URL und wartet, bis die Seite vollständig geladen ist.  
- Er wählt die ersten zehn Kommentare mit dem CSS-Selektor `#comments #content-text` aus.  
- Extrahierte Kommentare werden in der Konsole protokolliert.  

Bei der Ausführung gibt das Skript die ersten zehn Kommentare des ausgewählten Videos aus.

```
INFO  PuppeteerCrawler: Starting the crawler.
INFO  PuppeteerCrawler: Comments: Who are you rooting for?? US Marines or Ex Cons 
Bro Mateo is a beast, no lifting straps, close stance.
ex convict doing the pushups is a monster.
I love how quick this video was, without nonsense talk and long intros or outros
"They Both have combat experience" is wicked 
That military guy doing that deadlift is really no joke.. ...
One lives to fight and the other fights to live.
Finally something that would test the real outcome on which narrative is true on movies
I like the comradery between all of them. Especially on the Bench Press ... Both team members quickly helped out on the spotting to protect from injury. Well done.
I like this style, no youtube funny business. Just straight to the lifts
…output omitted…
```

Sie finden den gesamten in diesem Tutorial verwendeten Code auf [GitHub](https://github.com/See4Devs/crawlee-web-scraping).

## Conclusion

Crawlee kann helfen, die Effizienz und Zuverlässigkeit Ihrer Web-Scraping-Projekte zu verbessern. Sind Sie bereit, Ihre Web-Scraping-Projekte mit professionellen Daten, Tools und Proxies auf das nächste Level zu heben? Entdecken Sie die umfassende Web-Scraping-Plattform von Bright Data, die [einsatzbereite Datensätze](https://brightdata.de/products/datasets) und [fortschrittliche Proxy-Services](https://brightdata.de/proxy-types) bietet, um Ihre Datenerfassung zu optimieren.

Registrieren Sie sich jetzt und starten Sie Ihre kostenlose Testversion!