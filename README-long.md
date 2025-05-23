![Node.js CI](https://github.com/danja/wstore/actions/workflows/ci.yml/badge.svg)

# hyperdata-wstore

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/danja/hyperdata-wstore)

A cheap & cheerful Node.js HTTP server and minimal companion clients for file storage and retrieval.

Supports HTTP GET, POST, PUT, DELETE ([RFC 9112](https://www.rfc-editor.org/rfc/rfc9112)) with HTTP Basic ([RFC 7617](https://www.rfc-editor.org/rfc/rfc7617)) authentication. Includes example nginx config for proxying (you will want the server to do HTTPS for bare minimum acceptable security).

**Status 2025-05-16** : Renamed to hyperdata-wstore, published to npm, added DeepWiki badge.

_Not production-ready, it may never be._

---

## Quickstart

1. Install dependencies for both server and client:
   ```bash
   cd server && npm install
   cd ../client && npm install
   ```
2. In one terminal run:
   ```bash
   cd server
   node WebStore.js
   ```
3. In another terminal run:
   ```bash
   cd client
   BASE_URL="http://localhost:4500/"
   AUTH="admin:password"
   ./wstore.js --baseUrl=$BASE_URL --auth=$AUTH post ./hello.json tests/hello.json
   ./wstore.js -i get tests/hello.json
   ./wstore.js -h
   ```

---

## Testing

- To run all tests locally (unit, integration, e2e):
  ```bash
  npm test
  ```
- All tests are run automatically on GitHub Actions (see badge above).

---

## Server Features

- HTTP methods: GET, POST, PUT, DELETE
- HTTP Basic authentication for write operations (PUT, POST, DELETE)
- Content/media type handling based on file extensions
- Runs on port 4500

## Client Features

- Object-oriented command-line interface using yargs
- Support for GET, POST, PUT, DELETE operations
- Maps between local files and server resources
- Authentication support

---

## Installation

### Server Setup

Copy `server/config.example.js` to `server/config.js`, add desired credentials

```bash
cd server
npm install
node WebStore.js
```

### Client Setup

```bash
cd client
npm install
chmod +x wstore.js
```

#### Quick Test
```sh
./wstore.js --baseUrl=http://localhost:4500/ get hello.txt
```

---

## Deployment

1. Copy the server directory to your server location
2. Copy the nginx configuration to `/etc/nginx/sites-available/`
3. Create a symbolic link:
   ```bash
   ln -s /etc/nginx/sites-available/webstorage.conf /etc/nginx/sites-enabled/
   ```
4. Test new nginx config : `sudo nginx -t`
5. Restart nginx : `sudo systemctl nginx restart`
6. Update configuration in `config.js` or set environment variables for production settings
7. Install the client globally with `npm install -g` from the client directory or use it directly

---

## Environment Variables

You can configure the server and client using a `.env` file in the project root. Example:

```
AUTH_USERNAME=testuser
AUTH_PASSWORD=testpass
STORAGE_DIR=server/storage
default_baseUrl=http://localhost:4500/
```

- `AUTH_USERNAME` and `AUTH_PASSWORD`: Credentials for HTTP Basic authentication (server and client).
- `STORAGE_DIR`: Directory for file storage on the server.
- `default_baseUrl`: Default base URL for the client.

The server and client will automatically use these values if present.

---

## Client Usage

The following examples demonstrate how to use the `wstore` client with different HTTP methods.

## Setup Variables

For these examples, let's assume:

```bash
# Server location
BASE_URL="http://localhost:4500/"

# Authentication credentials
AUTH="admin:password"
```

## GET Examples

### Download a file to current directory

```bash
./wstore.js --baseUrl=$BASE_URL get documents/report.pdf ./report.pdf
```

**Output:**

```
File saved to ./report.pdf
```

### Display text file content directly in terminal

```bash
./wstore.js --baseUrl=$BASE_URL get notes/todo.txt
```

**Output:**

```
Buy groceries
Fix the server
Call John
```

### GET with custom location

```bash
./wstore.js --baseUrl=$BASE_URL get images/logo.png ./downloads/company-logo.png
```

**Output:**

```
File saved to ./downloads/company-logo.png
```

## POST Examples

### Create a new file on the server

```bash
./wstore.js --baseUrl=$BASE_URL --auth=$AUTH post ./data.json documents/data.json
```

**Output:**

```
File ./data.json created successfully at documents/data.json
```

### POST with environment variables for auth

```bash
export WSTORE_AUTH="admin:password"
./wstore.js --baseUrl=$BASE_URL post ./report.docx documents/reports/quarterly.docx
```

**Output:**

```
File ./report.docx created successfully at documents/reports/quarterly.docx
```

## PUT Examples

### Update an existing file

```bash
./wstore.js --baseUrl=$BASE_URL --auth=$AUTH put ./updated-data.json documents/data.json
```

**Output:**

```
File ./updated-data.json updated successfully at documents/data.json
```

### PUT with different server and explicit authentication

```bash
./wstore.js --baseUrl="http://otherserver.com/storage" --auth="user:secret" put ./logo-new.png branding/logo.png
```

**Output:**

```
File ./logo-new.png updated successfully at branding/logo.png
```

## DELETE Examples

### Delete a file from the server

```bash
./wstore.js --baseUrl=$BASE_URL --auth=$AUTH delete documents/old-report.pdf
```

**Output:**

```
File documents/old-report.pdf deleted successfully
```

### DELETE with environment variables

```bash
export WSTORE_BASEURL="http://hyperdata.it/files"
export WSTORE_AUTH="admin:password"
./wstore.js delete temp/cache.json
```

**Output:**

```
File temp/cache.json deleted successfully
```

## Advanced Examples

### Batch operations using shell script

```bash
#!/bin/bash
# Sync a directory of images

# Configuration
WSTORE="./wstore.js --baseUrl=http://hyperdata.it/files --auth=admin:password"

# Upload all PNG files in current directory
for file in *.png; do
  $WSTORE put "$file" "images/$file"
done

echo "All PNG files uploaded"
```

### Usage with npm global installation

Once installed globally with `npm install -g`, you can use the client without the `.js` extension:

```bash
wstore --baseUrl=http://hyperdata.it/files get documents/report.pdf
```

---

## Project Structure

```
webstore-project/
│
├── server/                      # Server application
│   ├── package.json             # Server dependencies
│   ├── WebStore.js              # Main server application
│   ├── config.js                # Server configuration
│   └── storage/                 # Default storage directory (created by the app)
│
├── client/                      # Command line client
│   ├── package.json             # Client dependencies
│   └── wstore.js                # Client application
│
└── nginx/                       # NGINX configuration (for reference during dev)
    └── webstorage.conf          # Will be moved to /etc/nginx/sites-available/ in production
```

---

## Contributing

Pull requests are welcome! Please ensure all tests pass locally (`npm test`) before submitting a PR.

---

## Troubleshooting

```sh
rm -rf node_modules package-lock.json
npm install
```