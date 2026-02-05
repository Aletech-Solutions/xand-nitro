# Nitro Docker (2026 !)
Nitro docker is a quick-to-setup docker dev environment. It can be run on Windows, Linux or OSX just with a few commands

# Installation
- Install ``just`` from here: https://github.com/casey/just#installation
- Install docker desktop (and for windows, enable WSL support) from here: https://www.docker.com/get-started/
- Clone this repository
- Execute the following commands inside cloned repository
- `just install`
- `just start-all`
- Wait 5/10 minutes (it's important)
- Once nitro accessible on [http://192.168.0.12:1080?sso=123](http://192.168.0.12:1080?sso=123)
- You can extract all assets easily with the command `just extract-nitro-assets`

‼️ If you have have the 20% error, look at the console, maybe it miss the "ExternalText". Just extract assets: previous command ⬆️

## More commands
- `just restart-arcturus`: restart arcturus emulator
- `just restart-nitro`: restart nitro dev server (Craco in nitro-react)
- `just watch-arcturus`: watch the emulator output
- `just watch-nitro`: watch the nitro dev server output
- `just --list`: display all usefull commands <3

## More informations
### Mysql
Mysql server use these credentials:
```
      - MYSQL_ROOT_PASSWORD=arcturus_root_pw
      - MYSQL_USER=arcturus_user
      - MYSQL_PASSWORD=arcturus_pw
      - MYSQL_DATABASE=arcturus
```
The first database come from mysql/dumps, it's the base Arcturus database for 3.0.X with just a default SSO ticket (123).

### Arcturus
- Arcturus is stored in emulator/arcturus, it's just a submodule from krews.git. Each time you run the nitro-arcturus image, each time the emulator is recompiled. You can recompile manualy with the command `just recompile-arcturus`.
- The NitroWebSocket plugin is already added
- All the configuration can be found in `emulator/config.ini`

### Nitro
- Nitro is the folder for nitro dev server, assets, swf, converters...
- The nitro-image contains 3 server: assets server (for all *.nitro files), swf server from morningstar (who contain all current useful SWF) and nitro-dev server on nitro-react.
- All extracted assets come from nitro-swf and will be extracted in nitro-assets (the nitro converter use the swf server)
- All the configurations for nitro-react are in `nitro/configuration`. If you make some change, just make `just restart-nitro`

## Server Configuration (External Deployment)

### Changing Server IP Address
If you're deploying to an external server (not localhost), you need to update the IP address in the following files:

**Files that need your server's IP address (e.g., `192.168.0.12` or `yourdomain.com`):**
1. `nitro/configuration/nitro-react/public/renderer-config.json`
   - Update: `socket.url`, `asset.url`, `image.library.url`, `hof.furni.url`
   - Example: `"socket.url": "ws://192.168.0.12:2096"`
   
2. `nitro/configuration/nitro-converter/configuration.json`
   - Update all URLs containing the IP address
   - Example: `"flash.client.url": "http://192.168.0.12:8081/gordon/PRODUCTION/"`

**Files that should keep `0.0.0.0` (server binding):**
- `emulator/config.ini` - Keep `game.host=0.0.0.0` and `rcon.host=0.0.0.0`
  - This tells the server to listen on all network interfaces

### Port Configuration
Current ports are configured as follows:
- **Port 3004**: Game server (Arcturus emulator)
- **Port 3001**: RCON server
- **Port 2096**: WebSocket connection
- **Port 8083**: Assets HTTP server
- **Port 8081**: SWF HTTP server
- **Port 1080**: Nitro React dev server (mapped to internal 5154)

To change ports, update:
1. `docker-compose.yaml` - Port mappings (e.g., `3004:3004`, `8083:8083`)
2. `emulator/config.ini` - `game.port` value
3. `nitro/supervisor/supervisord.conf` - HTTP server `-p` flags
4. `nitro/configuration/nitro-react/public/renderer-config.json` - URLs with port numbers
5. `nitro/configuration/nitro-converter/configuration.json` - URLs with port numbers

After making changes, restart the services: `just restart-all` or rebuild containers.

### Have fun <3
