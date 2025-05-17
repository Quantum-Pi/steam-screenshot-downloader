# steam-screenshot-downloader

## How to use

1. Get your [Steam Web API Key](https://steamcommunity.com/dev/apikey)
2. Get the App ID of the game you would like to download your screenshots for
    - You can do this by navigating to a game on the Steam Store and checking the URL
3. Get your SteamID / Username
    - Your username must be the one found in the URL on your profile page (known as the vanity url)
4. Select a directory to save the images too
5. Click start

## FAQ

### Q: Can I batch download screenshots without downloading this program?

You can, but the process is a little technical:

1. Navigate [here](https://steamapi.xpaw.me/#IPublishedFileService/GetUserFiles) which provides a way to call the API for accessing screenshots
2. Fill in the following fields:
    - Key: enter your [Steam Web API Key](https://steamcommunity.com/dev/apikey)
    - steamid: enter your steam id. There are numerous 3rd party websites that lets you find this
    - appid: enter the app id of the game you want your screenshots for. This can be found in the URL on he store page for the game
    - filetype: 4
3. Click execute. This will open a new tab with the response from the API. Note down the total field and return back to the previous page
4. Fill in the additional field:
    - numperpage: the total value from step 3
5. Click execute. You will now have an object for every screenshot for the provided app id. The field `file_url` contains the full resolution image
6. Use your prefered method of choice and batch download every url.

### Q: Can I download every screenshot on my account and not by individual games?

Not at the moment. If there is interest I may consider it.