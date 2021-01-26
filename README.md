# Install
`docker run --publish 7080:7080 --publish 127.0.0.1:3370:3370 --rm --volume ~/.sourcegraph/config:/etc/sourcegraph --volume ~/.sourcegraph/data:/var/opt/sourcegraph sourcegraph/server:3.24.0`

# Initial Steps
Access the web UI at http://localhost:7080 and follow the prompt to create an account.

# Configure
Generate a personal access token on GitHub: https://github.com/settings/tokens

Go to the ["Manage Repositories"](http://localhost:7080/site-admin/external-services) page in Sourcegraph and set the configuration below with the access token you generated. Optionally, change the repositories section to match whichever ones you'd like search over.

![Repository Config](/config.png)

# Chrome Extension Setup (for GitHub integration)

While Sourcegraph has a nice "View in GitHub" button, the reverse doesn't exist unless you setup the [Sourcegraph Chrome extension](https://chrome.google.com/webstore/detail/sourcegraph/dgjhfomjieaadpoljlnidmbgkdffpack?hl=en) (which has a bunch of other nice features too): 

Unfortunately, it requires setting up SSL on our local Sourcegraph instance.

`brew install mkcert`

`sudo CAROOT=~/.sourcegraph/config mkcert -install`
  
`sudo CAROOT=~/.sourcegraph/config mkcert -cert-file ~/.sourcegraph/config/sourcegraph.crt -key-file ~/.sourcegraph/config/sourcegraph.key codesearch.com`
  
Edit the `server` block of ~/.sourcegraph/config/nginx.conf to look like this:

    server {
        # Do not remove. The contents of sourcegraph_server.conf can change
        # between versions and may include improvements to the configuration.
        include nginx/sourcegraph_server.conf;

        listen 7080 ssl;
        server_name codesearch.com;
        ssl_certificate         sourcegraph.crt;
        ssl_certificate_key     sourcegraph.key;

Edit /etc/hosts (with sudo) and add a line `127.0.0.1 codesearch.com` so that https://codesearch.com:7080 points to your local sourcegraph instance.

Head to the ["Site configuration"](https://codesearch.com:7080/site-admin/configuration) page of Sourcegraph and set "externalURL" to `https://codesearch.com:7080`

Go to the Sourcegraph extension settings page and set "Sourcegraph URL" to `https://codesearch.com:7080`
