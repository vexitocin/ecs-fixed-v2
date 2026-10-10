# Economy Simulator source code (fixed)

For this to work you will need a Domain, and Windows (Linux hasn't been tested yet).

## 1. Installing Redis
Open PowerShell as Administrator, then run ```wsl --install```. This installs Ubuntu on Windows.

if you get an error, enable "Windows Subsystem For Linux" and "Windows Virtual Machine" in the "Turn Windows features on or off" application.

Once installation is complete, restart your computer then open WSL, Ubuntu will open, let it install.

Afterwards, it will ask for a uixd name. Enter a name such as "ECS", or whatever name you want. then enter a password. 

Run ```sudo apt update``` after its done installing, after it updates its packages run ```sudo apt install redis``` and press the "y" key when it asks for approval.

When it has finished installing run ```redis-server```. Keep this window open!

## 2. Installing the other Prerequistes
These are all ```.msi``` files which install the other required applications for Economy Simulator to run. Don't run any of these yet!

Node.js 18: ```https://nodejs.org/dist/v18.16.1/node-v18.16.1-x64.msi```

PostgreSQL: ```https://sbp.enterprisedb.com/getfile.jsp?fileid=1258627```

Dotnet 6.0: ```https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-6.0.412-windows-x64-installer```

Golang: ```https://go.dev/dl/go1.20.6.windows-amd64.msi```

## 3. Installing

Run the installer msi file and press next until it asks for a password, make sure you remember this. then keep pressing next and wait for it to install.

Then you can install Node.js, Dotnet and then Golang.

## 4. Creating the Database

Once PostgreSQL has installed, open the ```SQL Shell (psql)``` shortcut that has been made in your Start Menu (Windows Button).

When the Shell opens, it will promt for a server, database, port and username. Press ```Enter``` for all of these, as these are the default options.

Afterwards, it will prompt for the password you set earlier, enter it and you should be met with something like: ```postgres=#```

Copy this command into the Shell, replacing ```database_name``` with your actual database name.

```CREATE DATABASE database_name;```

It should respond by saying "CREATE DATABASE", this means it was created successfully.

## 5. Configuration
### Part 1
Navigate to ```services/api``` and make a file named ```config.json``` and paste this code into it.
```
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

### Part 2
Now Navigate to ```services/Roblox/Roblox.Website``` and rename ```appsettings.example.json``` to ```appsettings.json``` and then open it. go to line 37 and change the line ```"OwnerUserId": "12",``` to ```"OwnerUserId": "1",```

Then navigate to line 26, and change the line ```"Postgres": "Host=127.0.0.1; Database=economysimulator; Password=test; Username=postgres; Maximum Pool Size=20",``` to ``` "Postgres": "Host=127.0.0.1; Database=your_database; Password=your_password; Username=postgres; Maximum Pool Size=20",```, replacing the Database and Password once again

Now press ```ctrl + h``` and change 

Go into ```services/api``` create a folder named ```storage``` inside that folder make one named ```asset``` then go to ```services/api/public/images``` make a folder named ```thumbnails``` and ```group```

go to ```services/Roblox/Roblox.Website``` and in command prompt do ```dotnet run```

go to ```services/admin``` and run ```npm i``` and ```npm run build``` in a command.

go to ```services/2016-roblox-main``` and create a file named ```config.json```

paste this into it

```
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

replacing your.domain with your domain.

now in a command in the same folder do ```npm i``` and ```npm run build``` then ```npm run start```

now go to ```services/AssetValidationServiceV2``` and in command do ```go run main.go``` 

now go to ```services/RCCService``` and in command do ```RCCService.exe -console -placeid:1818```

now go to ```services/game-server``` and make a file named config.json in the file paste this
```
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

now this gets a little complicated, go into ```services/game-server/scripts```


in gameserver.lua press ```ctrl + h``` and replace ```economy-simulator.org``` with your domain, then go back to scripts, go into ```player``` folder and each file do the replacing, then in asset folders files. make sure to not replace the http and https

now go back into game-server and run ```npm i``` ```npm run build``` and ```npm run start```

step 6. cloudflare tunneling

Ok, so we got the whole site setup, exit out of all the cmd tabs, and go to https://cloudflare.com and sign up, add your domain onto the site. 

then go into the domain, and on the side bar press ```traffic``` then press ```cloudflare tunnel``` then launch zero trust dashboard sign up for the free option if it asks you to do that.

then press your email.

on the sidebar press ```access``` then ```tunnels``` then create a tunnel, name it and press save tunnel.

then download https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-windows-amd64.msi and run the power shell as administrator.

it says ```run the following command``` copy that and paste it into the powershell and scroll down on cloudflare wait for connectors to show your connected and press next.

on domain put your domain and type put http and url put ```localhost:5000``` and press save hostname.

Step 7. 

congrats site is setup go into ```/services``` on the source and run ```runall.bat``` and then when its all done go to your site, sign up the ROBLOX account, make sure its ID 1 and is all caps, then go to https://your.domain/admin and go to create player, put id 2 and name UGC and a random password, then go to the user on admin panel and nullify password.

now go back to create and id 12 name BadDecisions make sure its a hard pass.

now you can sign up.

congrats site made

- Made by Deadly
