<h1 align="center">Economy Simulator source code (fixed)</h1>

<p align="center">For this to work you will need a Domain, and Windows (Linux hasn't been tested yet).

The WebClient will be added soon.</p>

<h2 align="center">1. Installing Redis</h2>

Open PowerShell as Administrator, then run ```wsl --install```. This installs Ubuntu on Windows.

if you get an error, enable "Windows Subsystem For Linux" and "Windows Virtual Machine" in the "Turn Windows features on or off" application.

Once installation is complete, restart your computer then open WSL, Ubuntu will open, let it install.

Afterwards, it will ask for a uixd name. Enter a name such as "ECS", or whatever name you want. then enter a password. 

Run ```sudo apt update``` after its done installing, after it updates its packages run ```sudo apt install redis``` and press the "y" key when it asks for approval.

When it has finished installing run ```redis-server```. Keep this window open!

<h2 align="center">2. Installing the other Prerequistes</h2>

These are all ```.msi``` files which install the other required applications for Economy Simulator to run. Don't run any of these yet!

**Node.js 18**: ```https://nodejs.org/dist/v18.16.1/node-v18.16.1-x64.msi```

**PostgreSQL**: ```https://sbp.enterprisedb.com/getfile.jsp?fileid=1258627```

**Dotnet 6.0**: ```https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-6.0.412-windows-x64-installer```

**Golang**: ```https://go.dev/dl/go1.20.6.windows-amd64.msi```

<h2 align="center">3. Installing</h2>

Run the installer msi file and press next until it asks for a password, make sure you remember this. then keep pressing next and wait for it to install.

Then you can install Node.js, Dotnet and then Golang.

<h2 align="center">4. Creating the Database</h2>

Once PostgreSQL has installed, open the ```SQL Shell (psql)``` shortcut that has been made in your Start Menu (Windows Button).

When the Shell opens, it will promt for a server, database, port and username. Press ```Enter``` for all of these, as these are the default options.

Afterwards, it will prompt for the password you set earlier, enter it and you should be met with something like: ```postgres=#```

Copy this command into the Shell, replacing ```database_name``` with your actual database name.

```CREATE DATABASE database_name;```

It should respond by saying "CREATE DATABASE", this means it was created successfully.

<h2 align="center">5. Configuration</h2>

<h3 align="center">Part I - Database Migration</h3>

Navigate to ```services/api``` and make a file named ```config.json``` and paste this code into it.
```json
{
    "knex": {
	"client": "pg",
        "connection": {
        "host": "127.0.0.1",
        "user": "postgres",
        "password": "password",
        "database": "postgres"
        }
    }
}
```
Replace the Password and Database with the ones you have set.

Now open Command Prompt in the ```services/api``` folder and run ```npm i``` which installs the required node modules.

When this completes, Run ```npx knex migrate:latest``` which saves the Database Tables and Structure to the one you have made.

<h3 align="center">Part II - Backend Config</h3>

Now Navigate to ```services/Roblox/Roblox.Website``` and rename ```appsettings.example.json``` to ```appsettings.json``` and then open it. go to line 37 and change the line ```"OwnerUserId": "12",``` to ```"OwnerUserId": "1",```

Then navigate to line 26, and change the line ```"Postgres": "Host=127.0.0.1; Database=economysimulator; Password=test; Username=postgres; Maximum Pool Size=20",``` to ``` "Postgres": "Host=127.0.0.1; Database=your_database; Password=your_password; Username=postgres; Maximum Pool Size=20",```, replacing the Database and Password once again

Now press ```ctrl + h``` and change ```D:\\path\\``` to where you have downloaded this source code to, keeping the double backslashes.

<h3 align="center">Part III - Fixing folders</h3>

Go into ```services/api``` create a folder named ```storage``` inside that folder make one named ```asset``` then go to ```services/api/public/images``` and make two folders named ```thumbnails``` and ```group```.

<h3 align="center">Part IV - Building the Backend and Admin Panel</h3>

Then Go to ```services/Roblox/Roblox.Website``` and open a Command Prompt, Run ```dotnet run```

After, Go to ```services/admin``` and run ```npm i``` and ```npm run build``` in a command prompt.

<h3 align="center">Part V - Frontend Config</h3>

go to ```services/2016-roblox-main``` and create a file named ```config.json```

paste this into it

```json
{
  "serverRuntimeConfig": {
    "backend": {"csrfKey":"g0qiiDZw7jM2l54+7qsuRaymx6nBGdCKT9Kc0bqJB3aZ26rSsPMXfg8uWfUBtTqWenDVy+AQS1jkdrgvUwVSsw=="}
  },
  "publicRuntimeConfig": {
    "backend": {
      "proxyEnabled": true,
      "flags": {
        "myAccountPage2016Enabled": true,
        "catalogGenreFilterSupported": true,
        "catalogPageLimit": 28,
        "catalogSaleCountVisibleFromDetailsEndpoint": true,
        "commentsEndpointHasAreCommentsDisabledProp": true,
        "catalogDetailsPageResellerLimit": 10,
        "avatarPageInventoryLimit": 10,
        "friendsPageLimit": 25,
        "settingsPageThemeSelectorEnabled": true,
        "tradeWindowInventoryCollectibleLimit": 10,
        "moneyPagePromotionTabVisible": true,
        "gameGenreFilterSupported": true,
        "avatarPageOutfitCreatedAtAvailable": true,
        "catalogDetailsPageOwnersTabEnabled": true,
        "launchUsingEsURI": true
      },
      "baseUrl": "https://your.domain/",
      "apiFormat": "https://your.domain/apisite/{0}{1}"
    }
  }
}
```

Replacing your.domain with your Domain.

Now in a Command Prompt in the same folder, run ```npm i``` and ```npm run prod```, which builds and starts the Frontend.

<h3 align="center">Part VI - Asset Validation & Renders</h3>

Navigate to ```services/AssetValidationServiceV2``` and in a new Command Prompt run ```go run main.go```, and when it asks, allow it access to the Firewall.

Go to ```services/RCCService``` and in a Command Prompt run ```RCCService.exe -console -placeid:1818```. (For this source code, patching is Not required).

Staying in the RCCService folder, edit ```AppSettings.xml```, changing the URL from ```economy-simulator.org``` to your Domain.

Now go to ```services/game-server``` and make a file named config.json in the file paste this, replacing the authorisation with the ones from the Backends ```Appsettings.json```.
```json
{
    "rcc": "C:\\Users\\yourusername\\Downloads\\ECS\\services\\RCCService",
    "authorization": "90WGEGNJGWHIWGOI31900H9GIOGI",
    "baseUrl": "http://localhost:5000",
    "rccPort": 64989,
    "port": 3040,
    "websiteBotAuth": "UW8U8TU9W9R8RHGRJOGWGOINOOWGNWRNJWWNRJ",
    "thumbnailWebsocketPort": 3189,
    "dockerDisabled": true
}
```

Now go into ```services/game-server/scripts```

In ```gameserver.lua```, press ```ctrl + h``` and replace ```economy-simulator.org``` with your Domain once again, then do the same in every file in the ```player``` folder and the ```asset``` folder, making sure **not** to replace HTTP or HTTPS.

Now go back into game-server and run ```npm i``` ```npm run build``` and ```npm run start```.

<h2 align="center">6. Cloudflare Tunnels</h2>

Ok, so now you've got the whole site fully setup, exit out of all the cmd tabs, and go to ```https://cloudflare.com``` and sign up, or login.

Add your domain onto Cloudflare if you haven't already. 

Then select your Domain, and on the Sidebar press ```Traffic``` then press ```Cloudflare tunnel``` then launch zero trust dashboard sign up for the free option if it asks you to do that, for some reason Cloudflare needs a Credit card even for the free option.

Press your email, and on the sidebar press ```Access``` then ```Tunnels``` then create a tunnel, name it and press save tunnel.

Then download Cloudflared: ```https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.msi```.

Open a new PowerShell window and copy the phrase under ```run the following command``` on the Cloudflare website and paste it into the PowerShell window and scroll down and wait for "Connectors" to show your computer as connected and press next.

On domain put your domain and type put http and url put ```localhost:5000``` and press save hostname.

<h2 align="center">7. Making ID 1's account</h2>

On your site, sign up as usual, and your application should be automatically accepted by the backend.

<h2 align="center">8. Running the site</h2>

For running the website 24/7, I suggest buying a VPS, which is a Server which hosts your website. Don't get the cheapest one, as they are usually shit.

To run the site, run ```start site.bat``` which is located in ```services```.

**Economy Simulator is now runnning!**

happy skidding

- vexi
