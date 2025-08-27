# AI-babovky-azure-doc
Návod na deploy na Azure

Tento dokument obsahuje podrobný návod pro nasazení aplikací na Microsoft Azure, včetně vytvoření resource group, static web app a app service pro Java aplikace.

## Obsah

1. [Vytvoření Azure subskripce](#vytvoření-azure-subskripce)
2. [Vytvoření Resource Group](#vytvoření-resource-group)
3. [Static Web App s GitHub integrací](#static-web-app-s-github-integrací)
4. [App Service pro Java s GitHub integrací](#app-service-pro-java-s-github-integrací)

---

## Vytvoření Azure Subskripce

1. **Přihlaste se do my.visualstudio.com**
   - Otevřete [my.visualstudio.com](https://my.visualstudio.com/)
   - Přihlaste se pomocí svého Azure účtu  (jmeno.prijmeni@etnetera.cz a heslo stejne jako do AD )

2. **Vytvoření nové Azure subskripce**
   - V sekci "Benefits" uvidíte možnost "Activate" pro novou subskripci
   - Klikněte na "Activate" a postupujte podle pokynů

   ![vse-azure-create.png](/img/vse-azure-create.png)

## Vytvoření Resource Group

Resource Group je logická skupina pro správu Azure prostředků. Všechny prostředky v jedné resource group sdílejí stejný životní cyklus.

### Postup vytvoření přes Azure Portal:

1. **Přihlaste se do Azure Portal**
   - Otevřete [portal.azure.com](https://portal.azure.com)
   - Přihlaste se pomocí svého Azure účtu  (jmeno.prijmeni@etnetera.cz a heslo stejne jako do AD )

2. **Vytvoření nové Resource Group**
   - V levém menu klikněte na "Resource groups"

![menu resource group](/img/azure-menu-resourcegroup.png) <br>

   - Klikněte na "+ Create" nebo "+ Vytvořit"

![create resource group](/img/azure-resourcegroup.png) <br>

3. **Vyplnění základních údajů**
   - **Subscription**: Vyberte správné předplatné
   - **Resource group name**: Zadejte název (např. `my-app-rg`)
   - **Region**: Vyberte region (doporučeno: West Europe)

![create resource group](/img/azure-resourcegroup-create.png) <br>

4. **Tagy (volitelné)**
   - Přidejte tagy pro lepší organizaci (např. `Environment: Production`)

5. **Dokončení**
   - Klikněte na "Review + create"
   - Po validaci klikněte na "Create"

### Postup vytvoření přes Azure CLI:

```bash
# Přihlášení do Azure
az login

# Vytvoření resource group
az group create --name my-app-rg --location westeurope

# Ověření vytvoření
az group list --output table
```

---

## Static Web App s GitHub integrací

Azure Static Web Apps je služba pro hostování statických webových aplikací s automatickým CI/CD z GitHub.

### Předpoklady:

- GitHub repozitář s vaší statickou aplikací (HTML, CSS, JS, React, Angular, Vue.js, atd.)
- Azure účet s aktivním předplatným

### Postup vytvoření:

1. **Příprava GitHub repozitáře**
   - Ujistěte se, že váš kód je v GitHub repozitáři
   - Struktura projektu by měla obsahovat soubory pro build (např. `package.json` pro Node.js projekty)

2. **Vytvoření Static Web App v Azure Portal**
   - V Azure Portal klikněte na "+ Create a resource"
   - Vyhledejte "Static Web App" a vyberte ji
   - Klikněte na "Create"

   ![create static web app](/img/azure-swa-search.png)

3. **Konfigurace základních nastavení**
   - **Subscription**: Vyberte předplatné
   - **Resource Group**: Vyberte existující nebo vytvořte novou
   - **Name**: Zadejte název aplikace (např. `my-static-app`)
   - **Plan type**: Free 

   ![create static web app](/img/azure-swa-create.png)

4. **GitHub integrace**
   - **Source**: GitHub
   - **GitHub account**: Autorizujte přístup k vašemu GitHub účtu
   - **Organization**: Vyberte organizaci
   - **Repository**: Vyberte repozitář
   - **Branch**: Vyberte hlavní větev (obvykle `main` nebo `master`)

5. **Build konfigurace**
   - **Build Presets**: Vyberte framework (React, Angular, Vue, Vanilla JavaScript, atd.)
   - **App location**: Cesta ke zdrojovým souborům (obvykle `/` nebo `/src`)
   - **Api location**: Cesta k API (pokud máte Azure Functions, jinak ponechte prázdné)
   - **Output location**: Cesta k build výstupu (např. `build`, `dist`, `public`)

6. **Dokončení**
   - Klikněte na "Review + create"
   - Po validaci klikněte na "Create"

### Co se stane po vytvoření:

- Azure automaticky vytvoří GitHub Action workflow v `.github/workflows/`
- Při každém push do vybrané větve se spustí automatický build a deploy
- Aplikace bude dostupná na URL ve formátu: `https://[název-aplikace].azurestaticapps.net`


---

## App Service pro Java s GitHub integrací

Azure App Service umožňuje hostování webových aplikací s podporou různých jazyků včetně Java, NodeJs, PHP a dalších.

### Předpoklady:

- Java aplikace v GitHub repozitáři (Spring Boot, Maven/Gradle projekt)
- Dockerfile nebo buildpacks kompatibilní struktura

### Postup vytvoření:

1. **Příprava Java aplikace**
   - Ujistěte se, že máte `pom.xml` (Maven) nebo `build.gradle` (Gradle)
   - Aplikace by měla běžet na portu definovaném v `PORT` environment proměnné
   - Pro Spring Boot přidejte do `application.properties`:
     ```properties
     server.port=${PORT:8080}
     ```

2. **Vytvoření App Service v Azure Portal**
   - V Azure Portal klikněte na "+ Create a resource"
   - Vyhledejte "Web app" a vyberte ji
   - Klikněte na "Create"

   ![create web app](/img/azure-appservice-create.png)

3. **Konfigurace základních nastavení**
   - **Subscription**: Vyberte předplatné  
   - **Resource Group**: Vyberte existující resource group
   - **Name**: Zadejte název (např. `my-java-app`)
   - **Publish**: Code
   - **Runtime stack**: Java (vyberte verzi, např. Java 21)
   - **Java web server stack**: Java SE (pro Spring Boot) nebo Tomcat
   - **Operating System**: Linux (doporučeno)
   - **Region**: West Europe

4. **App Service Plan**
   - Vytvořte nový
   - Zadejte název (např. `my-java-asp`). 
   - Jako Pricing Plan vyberte "Basic B1"

5. **GitHub Deployment**
   - V tabu "Deployment" vyberte "Enable continuous deployment"
   - **Source**: GitHub
   - **GitHub account**: Autorizujte přístup
   - **Organization**: Vyberte organizaci
   - **Repository**: Vyberte repozitář s Java aplikací
   - **Branch**: Vyberte větev

6. **Pokročilá nastavení (volitelné)**
   - **Startup Command**: Ponechte prázdné pro auto-detekci
   - **Application Settings**: Přidejte environment proměnné

7. **Dokončení**
   - Klikněte na "Review + create"
   - Po validaci klikněte na "Create"

### Konfigurace build procesu:

Azure automaticky detekuje Java aplikace a použije vhodný buildpack. Pro vlastní konfiguraci můžete přidat soubor `.deployment`:

```ini
[config]
SCM_BUILD_ARGS=-Dspring.profiles.active=prod
```

### Environment proměnné:

1. V Azure Portal přejděte na vaši App Service
2. V levém menu vyberte "Configuration"
3. V záložce "Application settings" přidejte potřebné proměnné:
   - `SPRING_PROFILES_ACTIVE=production`
   - `DATABASE_URL=...`
   - atd.

### Monitoring a logování:

1. **Application Insights**: Povolte pro sledování výkonu
2. **Log Stream**: V Azure Portal → vaše App Service → "Log stream"
3. **Kudu konzole**: `https://[název-aplikace].scm.azurewebsites.net`


---


## Užitečné odkazy

- [Azure dokumentace](https://docs.microsoft.com/azure/)
- [Azure CLI reference](https://docs.microsoft.com/cli/azure/)
- [GitHub Actions pro Azure](https://github.com/Azure/actions)
- [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/)
