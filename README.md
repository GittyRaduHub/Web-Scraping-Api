# Web-Scraping-Api
I have created a web scraping Api using express.js and React
Pentru stilizare am folosit css simplu si am facut pagina responsive folosind media queries.
Lupta cea mai mare a fost sa incarc proiectul pe git....

Nu prea stiu cum sa explic documentatia, asa ca o sa incerc sa o fac aici....

Pentru a rula programul se v-a introduce url-ul in campul de input si se v-a trimite informatia catre server.
Dupa aceasta se v-a apasa pe butonul de afisare informatie si datele extrase de pe pagina se vor afisa.

!Disclaimer!
Uneori api-ul nu primeste data,(am incercat sa repar dar nu mi-am dat seama care este problema) + este prima oara cand incerc sa fac un scraper!
Prin restart se repara problema si aplicatia primeste data.

Deoarce s-a mentionat faptul ca id-urile si clasele din css sunt dinamice, m-am legat de elementele html cand am gandit aceasta aplicatie.!

      --Aici am luat titlul de pe pagina:
      const title = $("a:not(:has(img))")
      .map((index, element) => $(element).text())
      .get();
      
      --Aici am luat descrierea de pe pagina:
    const shortDescription = $(".group")
      .map((index, element) => $(element).find("div:not(:has(a))").text())
      .get();

      --Aici am luat adresa imaginii:
    const image = $("a")
      .map(
        (index, element) =>
          "https://wsa-test.vercel.app" + $(element).find("img").attr("src")
      )
      .get();
      
      --Aici am luat adresa linkurilor din pagina:
    const href = $("a:not(:has(img))")
      .map(
        (index, element) =>
          "https://wsa-test.vercel.app" + $(element).attr("href")
      )
      .get();

      --Aici am gandit logica functiei care arata ce sentiment exista in blog..
      Nu am fost prea sigur cum sa abordez aceasta problema, dar m-am gandit ca cel mai simplu ar fi
      sa ma leg de cuvintele din titlu:
      const sentiment = function (titlu) {
      if (titlu.includes("Joys") || titlu.includes("Radiant")) {
        return "positive";
      } else if (titlu.includes("Challenges") || titlu.includes("Junk")) {
        return "negative";
      } else if (titlu.includes("Neutral")) {
        return "neutral";
      }
    };

    --Aici am gandit functia pentru a numara cuvintele din descriere:
    Am folosit metoda trim() pentru a scapa de spatii libere nedorite si cu split() am impartit stringul intr-un array si am returnat lungimea array-ului.
    const wordCounter = function (description) {
      const string = description.trim();
      if (string === "") {
        return 0;
      }
      const stringArray = string.split(/\s+/);
      return stringArray.length;
    };
    
      --Aici am mapat toate datele obtinute mai sus:
      const data = title.map((title, index) => ({
      title,
      shortDescription: shortDescription[index],
      image: image[index],
      href: href[index],
      sentiment: (rsp = sentiment(title)),
      words: (words = wordCounter(shortDescription[index])),
    }));

      --Aici am primit adresa url-ului.
    const newUrl = app.post("/", (req, res) => {
        const url = req.body.url;
        res.send(`Data received: ${url}`);
        console.log(`Received ${url}`);
        console.log(url);
        return url;
      });

      --Aceasta este functia care transforma informatiile primite in JSON:
      onst dateScraper = (async () => {
        const newArray = await scraper("https://wsa-test.vercel.app");
        console.log(newArray);
        const jsonArray = JSON.stringify(newArray);
        console.log(jsonArray);
        return jsonArray;
      })();

      --Aici trimit catre front informatiile:
      app.get("/", async (req, res) => {
        const result = await dateScraper;
        res.send(result);
      });
    

